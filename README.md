## Cookiecutter used to download the SAM Demo Project
```
pip3 install cookiecutter
```
# Java Demo
```
cookiecutter https://github.com/aws/aws-sam-cli-app-templates.git --no-input \
  --directory java17/hello-img-gradle \
  project_name=my-lambda-java
```
### Build and Test
```

docker build -t test my-lambda-java/HelloWorldFunction
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
  --directory al2023/go/hello-img \
  project_name=my-lambda-go

docker build -t test my-lambda-go/hello-world

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
