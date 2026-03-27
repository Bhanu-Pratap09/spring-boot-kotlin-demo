version: 0.2

phases:
  install:
    runtime-versions:
      java: corretto17

  pre_build:
    commands:
      - echo Logging in to Amazon ECR...
      - aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <358133388983.dkr.ecr.us-east-1.amazonaws.com/springboot-app>

  build:
    commands:
      - ./gradlew build
      - docker build -t springboot-app .
      - docker tag springboot-app:latest <358133388983.dkr.ecr.us-east-1.amazonaws.com/springboot-app>:latest

  post_build:
    commands:
      - docker push <ECR-URI>:latest
      - printf '[{"name":"springboot-container","imageUri":"%s"}]' <358133388983.dkr.ecr.us-east-1.amazonaws.com/springboot-app>:latest > imagedefinitions.json

artifacts:
  files:
    - imagedefinitions.json
