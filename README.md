## FastAPI AWS Lambda Python

# 1. Create Local Runnable version of Docker FastAPI AWS Lambda code.
### Docker simple AWS Lambda Python

/Users/tonyodonnell/Kong-Lambda-Docker-FastAPI

Local run of container
````
docker buildx build --platform linux/amd64 --provenance=false -t docker-image:fastapi .
````
````
docker run --platform linux/amd64 -p 9001:8080 docker-image:fastapi
````

port mapping  HOST_PORT:CONTAINER_PORT <9001:8080>

docker run --platform linux/amd64 -d -p 9001:8080 docker-image:test lambda_function.handler

curl http://localhost:9001/lamdbda_function 

curl -v http://localhost:8080/

curl "http://localhost:9001/2015-03-31/functions/function/invocations" -d '{}'

sleep 2 && curl "http://localhost:9001/2015-03-31/functions/function/invocations" -d '{}'

#### Perfect! The server is now running successfully on port 9001. 

The Lambda function is responding correctly. You can now test it with:

•  curl "http://localhost:9001/2015-03-31/functions/function/invocations" -d '{}'

•  curl "http://localhost:9001/2015-03-31/functions/function/invocations" -d '{"payload":"hello world!"}'

---

# 2. Now deploy Docker FastAPI AWS Lambda code to AWS as a Lambda Python

````
curl "http://localhost:9001/2015-03-31/functions/function/invocations" -d '{}'

curl "http://localhost:9001/2015-03-31/functions/function/invocations" -d '{"payload":"hello world!"}'
````

tonyodonnell@Tonys-MacBook-Pro-2 Kong-Lambda-Docker % curl "http://localhost:9001/2015-03-31/functions/function/invocations" -d '{}'

````
"Hello from AWS Lambda using Python3.12.12 (main, Nov  3 2025, 10:02:13) [GCC 11.5.0 20240719 (Red Hat 11.5.0-5)]!"
````
---
````
aws ecr get-login-password --region eu-west-1 | docker login --username AWS --password-stdin 129269632956.dkr.ecr.eu-west-1.amazonaws.com
````
````
aws ecr create-repository --repository-name hello-world --region eu-west-1 --image-scanning-configuration scanOnPush=true --image-tag-mutability MUTABLE
````

````
{
    "repository": {
        "repositoryArn": "arn:aws:ecr:eu-west-1:129269632956:repository/hello-world",
        "registryId": "129269632956",
        "repositoryName": "hello-world",
        "repositoryUri": "129269632956.dkr.ecr.eu-west-1.amazonaws.com/hello-world",
        "createdAt": "2025-11-15T18:57:13.759000+00:00",
        "imageTagMutability": "MUTABLE",
        "imageScanningConfiguration": {
            "scanOnPush": true
        },
        "encryptionConfiguration": {
            "encryptionType": "AES256"
        }
    }
}
````

"repositoryUri": "905418199363.dkr.ecr.eu-west-1.amazonaws.com/hello-world"

````
docker tag docker-image:test 129269632956.dkr.ecr.eu-west-1.amazonaws.com/hello-world:latest
````
````
docker push 129269632956.dkr.ecr.eu-west-1.amazonaws.com/hello-world:latest
````

role
````
aws iam create-role --role-name lambda-ex --assume-role-policy-document '{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "lambda.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}'
````
output
````
{
    "Role": {
        "Path": "/",
        "RoleName": "lambda-ex",
        "RoleId": "AROAR4GISB66LKLCITVVL",
        "Arn": "arn:aws:iam::129269632956:role/lambda-ex",
        "CreateDate": "2025-11-15T19:06:47+00:00",
        "AssumeRolePolicyDocument": {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Effect": "Allow",
                    "Principal": {
                        "Service": "lambda.amazonaws.com"
                    },
                    "Action": "sts:AssumeRole"
                }
            ]
        }
    }
}
````

````
aws lambda create-function --function-name hello-world --package-type Image --code ImageUri=129269632956.dkr.ecr.eu-west-1.amazonaws.com/hello-world:latest --role arn:aws:iam::129269632956:role/lambda-ex
````
---

<br>

````
aws lambda invoke --function-name hello-world response.json
````
output. ( >> response.json file)

````
{
    "StatusCode": 200,
    "ExecutedVersion": "$LATEST"
}
(END)
````
