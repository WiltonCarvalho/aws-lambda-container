## Cookiecutter used to download the SAM Demo Project
```
sudo pip3 install cookiecutter
```
## Java Demo
```
cookiecutter https://github.com/aws/aws-sam-cli-app-templates.git --no-input \
  --directory java11-image/cookiecutter-aws-sam-hello-java-gradle-lambda-image \
  project_name=test
cd test/HelloWorldFunction
```
## Dockerfile from https://gallery.ecr.aws/lambda/java
```
cat <<'EOF'> Dockerfile.podman
FROM public.ecr.aws/lambda/java:11 as build-image
COPY src/ src/
COPY gradle/ gradle/
COPY build.gradle gradlew ./
RUN ./gradlew build

FROM public.ecr.aws/lambda/java:11
# Copy function code and runtime dependencies from Gradle layout
COPY --from=build-image ${LAMBDA_TASK_ROOT}/build/dependency/* ${LAMBDA_TASK_ROOT}/lib/
COPY --from=build-image ${LAMBDA_TASK_ROOT}/build/classes/java/main ${LAMBDA_TASK_ROOT}
# Set the CMD to your handler (could also be done as a parameter override outside of the Dockerfile)
CMD ["helloworld.App::handleRequest"]
EOF
```
## Gradle task from https://gallery.ecr.aws/lambda/java
```
cat <<'EOF'>> build.gradle
// prepare the runtime dependencies
task copyRuntimeDependencies(type: Copy) {
    from configurations.runtimeClasspath
    into 'build/dependency'
}
build.dependsOn copyRuntimeDependencies
EOF
```
## Docker Build and Test
```
docker build -t test .
docker run -it --rm -p 9000:8080 test

curl -s -XPOST "http://localhost:9000/2015-03-31/functions/function/invocations" \
  -d '{"payload":"hello world!"}' | jq -r .body
```
