## Cookiecutter used to download the SAM Demo Project
```
sudo pip3 install cookiecutter
```
# Java Demo
```
cookiecutter https://github.com/aws/aws-sam-cli-app-templates.git --no-input \
  --directory java11-image/cookiecutter-aws-sam-hello-java-gradle-lambda-image \
  project_name=test

cd test/HelloWorldFunction
```
### - Dockerfile from https://gallery.ecr.aws/lambda/java
```
cat <<'EOF'> Dockerfile
FROM public.ecr.aws/lambda/java:11
# Copy function code and runtime dependencies from Gradle layout
COPY build/dependency/* ${LAMBDA_TASK_ROOT}/lib/
COPY build/classes/java/main ${LAMBDA_TASK_ROOT}
# Set the CMD to your handler (could also be done as a parameter override outside of the Dockerfile)
CMD ["helloworld.App::handleRequest"]
EOF
```
### - Gradle task from https://gallery.ecr.aws/lambda/java
```
cat <<'EOF'>> build.gradle

// prepare the runtime dependencies
task copyRuntimeDependencies(type: Copy) {
    from configurations.runtimeClasspath
    into 'build/dependency'
}
build.dependsOn copyRuntimeDependencies

task buildZip(type: Zip) {
    from compileJava
    from processResources
    into('lib') {
        from configurations.runtimeClasspath
    }
    //archiveFileName = "package.zip"
    destinationDirectory = file("$buildDir/libs")
}

build.dependsOn buildZip
EOF
```
### - Build and Test
```
./gradlew clean build

docker build -t test .
```
```
docker run -it --rm --name test -p 9000:8080 \
  -v $HOME/.aws:/.aws:ro \
  -e AWS_PROFILE="${AWS_PROFILE:-default}" \
  -e AWS_SHARED_CREDENTIALS_FILE="/.aws/credentials" \
  -e AWS_DEFAULT_REGION="sa-east-1" \
  test
```
```
curl -s -XPOST "http://localhost:9000/2015-03-31/functions/function/invocations" \
  -d '{"payload":"hello world!"}' | jq -r .body
```
# Golang Demo
```
cookiecutter https://github.com/aws/aws-sam-cli-app-templates.git --no-input \
  --directory go1.x-image/cookiecutter-aws-sam-hello-golang-lambda-image \
  project_name=test

cd test/hello-world

docker build -t test .
docker run -it --rm -p 9000:8080 test

curl -s -XPOST "http://localhost:9000/2015-03-31/functions/function/invocations" \
  -d '{"payload":"hello world!"}' | jq -r .body
```
# NodeJS Demo
```
cookiecutter https://github.com/aws/aws-sam-cli-app-templates.git --no-input \
  --directory nodejs16.x-image/cookiecutter-aws-sam-hello-nodejs-lambda-image \
  project_name=test

cd test/hello-world

docker build -t test .
docker run -it --rm -p 9000:8080 test

curl -s -XPOST "http://localhost:9000/2015-03-31/functions/function/invocations" \
  -d '{"payload":"hello world!"}' | jq -r .body
```
# Python Demo
```
cookiecutter https://github.com/aws/aws-sam-cli-app-templates.git --no-input \
  --directory python3.9-image/cookiecutter-aws-sam-hello-python-lambda-image \
  project_name=test

cd test/hello_world

docker build -t test .
docker run -it --rm -p 9000:8080 test

curl -s -XPOST "http://localhost:9000/2015-03-31/functions/function/invocations" \
  -d '{"payload":"hello world!"}' | jq -r .body
```
