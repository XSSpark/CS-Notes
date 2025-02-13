---
Author:
  - Xinyang YU
Author Profile:
  - https://linkedin.com/in/xinyang-yu
tags:
  - Datadog
Creation Date: 2023-12-05T10:27:00
Last Date: 2024-01-18T16:16:36+08:00
References: 
title: "Mastering Datadog APM in ECS Fargate: A Comprehensive Guide for Seamless AWS Integration"
description: "Unlock the full potential of Datadog APM for Application Performance Monitoring (APM) with our comprehensive guide on setting up Datadog APM in ECS Fargate. Whitelist outbound traffic, integrate AWS Firelens, and deploy the Datadog Agent Sidecar effortlessly. Optimize your ECS Fargate setup with Terraform sample codes and fine-tune Datadog Agent settings. Dive into seamless monitoring with Datadog and elevate your AWS integration game. #APM #Datadog #ECSFargate #AWSIntegration"
---

## Abstract
---
Datadog APM is used for [[Application Performance Monitoring (APM)]]

>[!info] Whitelist outbound traffic to Datadog Endpoints
>In some deployment environment, by default all outbound traffic is denied. [Here](https://docs.datadoghq.com/agent/configuration/network/?tab=agentv6v7) is a list of datadog endpoints you can use to whitelist the traffic

## ECS Fargate Setup
---
Make sure you have done the following first!
- [ ] [[Datadog Integration#AWS Integration]]
- [ ] [[Datadog ddtrace]] 

The rest of the setup is around [[ECS#Task Definition]], we need to add in 3 parts into it
- [ ] [[#Pipe log to AWS Firelens]]
- [ ] [[#AWS Firelens]] 
- [ ] [[#Datadog Agent Sidecar]]

### Pipe log to AWS Firelens

Add the following block inside the [[ECS#Task Definition]] of **container** that we want to access the log

Update the highlighted parts with your own values

```json {4-5, 7-9}
"logConfiguration": {
  "logDriver": "awsfirelens",
  "options": {
    "Host": "http-intake.logs.datadoghq.eu",
    "Name": "datadog",
    "TLS": "on",
    "apikey": "<YOUR_API_KEY>",
    "dd_service": "AEGIS-dev-backend",
    "dd_source": "AEGIS-dev-backend-firelens",
    "provider": "ecs"
  }
}
```

### AWS Firelens
You can refer to [[ECS#Hardware Details]] for the `cpu` and `memory` configuration

Update the highlighted parts with your own values

```json {2, 4-5}
{
	"name": "log_router",
	"image": "amazon/aws-for-fluent-bit:stable",
	"cpu": 256,
	"memory": 512,
	"portMappings": [],
	"essential": true,
	"environment": [],
	"mountPoints": [],
	"volumesFrom": [],
	"user": "0",
	"firelensConfiguration": {
		"type": "fluentbit",
		"options": {
			"enable-ecs-log-metadata": "true"
		}
	}
}
```

### Datadog Agent Sidecar
There is [a list of environment variables](https://docs.datadoghq.com/serverless/guide/agent_configuration/) you can add to fine tune the agent

Update the highlighted parts with your own values

```json {2, 4-5, 11, 15, 19, 23, 27}
{
	"name": "datadog-agent",
	"image": "public.ecr.aws/datadog/agent:latest",
	"cpu": 256,
	"memory": 512,
	"portMappings": [],
	"essential": true,
	"environment": [
		{
			"name": "ECS_FARGATE",
			"value": "true"
		},
		{
			"name": "DD_API_KEY",
			"value": "<YOUR_API_KEY>"
		},
		{
			"name": "DD_SITE",
			"value": "datadoghq.eu"
		},
		{
			"name": "DD_APM_ENV",
			"value": "aegis-stg"
		},
		{
			"name": "DD_APM_IGNORE_RESOURCES",
			"value": "GET /health"
		}
	],
	"mountPoints": [],
	"volumesFrom": []
}
```


> [!info] `DD_APM_ENV` overrides `DD_ENV`

> [!tip] We can use `DD_APM_IGNORE_RESOURCE` to ignore [[Trace]] from transmitted to Datadog
### Terraform Sample Codes
Refer to the above sections for configuration details


```hcl
resource "aws_ecs_task_definition" "backend_app" {
  family                   = ""
  requires_compatibilities = ["FARGATE"]
  network_mode             = "awsvpc"
  cpu                      = 2048
  memory                   = 4096
  execution_role_arn       = ""
  task_role_arn            = ""

  container_definitions = jsonencode([
    {
      name      = ""
      image     = ""
      cpu       = 1024
      memory    = 2048
      essential = true



      portMappings = []
      secrets = []

	  environment = [
	  {
          "name": "DD_SERVICE",
          "value": ""
	  },
	  {
          "name": "DD_ENV",
          "value": ""
	  }
	  ]

      logConfiguration = {
        logDriver : "awsfirelens",
        options : {
          "Host": "",
          "Name": "",
          "TLS": "on",
          "apikey": "<YOUR_API_KEY>",
          "dd_service": "",
          "dd_source": "",
          "provider": "ecs"
        }
      }
    },
    {
      name: "",
      image: "amazon/aws-for-fluent-bit:stable",
      cpu: 256,
      memory: 512,
      portMappings: [],
      essential: true,
      environment: [],
      mountPoints: [],
      volumesFrom: [],
      user: "0",
      firelensConfiguration: {
        type: "fluentbit",
        options: {
          "enable-ecs-log-metadata": "true"
        }
      }
    },
    {
      "name": "",
      "image": "public.ecr.aws/datadog/agent:latest",
      "cpu": 256,
      "memory": 512,
      "portMappings": [],
      "essential": true,
      "environment": [
        {
          "name": "ECS_FARGATE",
          "value": "true"
        },
        {
          "name": "DD_API_KEY",
          "value": "<YOUR_API_KEY>"
        },
        {
          "name": "DD_SITE",
          "value": ""
        },
        {
          "name": "DD_APM_ENV",
          "value": ""
        },
        {
          "name": "DD_APM_IGNORE_RESOURCES",
          "value": ""
        }
      ],
      "mountPoints": [],
      "volumesFrom": [],
    }
  ])
  runtime_platform {
    operating_system_family = "LINUX"
    cpu_architecture        = "X86_64"
  }
}
```

>[!bug] 
>`DD_APM_IGNORE_RESOURCES` takes in a list of resources, but I wasn't able to pass a list object to the key-value pair environment variable. Please let me know if you find a way around it 😃


## References
---

- [Official ECS Fargate Integration](https://docs.datadoghq.com/integrations/ecs_fargate/?tab=webui)
- [Official Datadog Agent Configuration](https://docs.datadoghq.com/serverless/guide/agent_configuration)