# smartRCAAssistant

AI-Powered Root Cause Analysis Assistant using Amazon Bedrock Agent, AWS Lambda, and CloudWatch Logs.

---

# Smart RCA Assistant using Amazon Bedrock Agent

## Overview

This repository contains the implementation of an AI-powered Root Cause Analysis (RCA) system for AWS Lambda workloads. The solution automatically retrieves Lambda error logs from Amazon CloudWatch Logs, analyzes failures using Amazon Bedrock foundation models (Claude 3.5 Sonnet / LLaMA3), and returns intelligent fix recommendations through a Bedrock Agent.

The project helps DevOps and Cloud Engineering teams reduce Mean Time To Resolution (MTTR) by automating log analysis and providing contextual RCA insights using Generative AI.

---

# Features

- Automated Lambda log retrieval from CloudWatch Logs
- AI-powered Root Cause Analysis using Amazon Bedrock
- Bedrock Agent + Action Group integration
- Intelligent error summarization and remediation suggestions
- Supports Claude 3.5 Sonnet and LLaMA3 models
- Event-driven serverless architecture
- Real-time RCA generation
- Human-readable troubleshooting responses
- OpenAPI-based agent orchestration

---

# Architecture

<img width="2048" height="1065" alt="rootCauseAnalysis" src="https://github.com/user-attachments/assets/b46137a3-a85c-4906-9ddb-ee60698a7723" />


## Components

### Amazon CloudWatch Logs
Stores Lambda execution logs and error traces.

### Bedrock Agent
Receives user queries and orchestrates RCA workflows.

### Action Group
Connects the Bedrock Agent to custom RCA Lambda functions.

### RCAlambdaFx (Custom Lambda)
Responsible for:
- Reading CloudWatch logs
- Filtering error patterns
- Constructing prompts
- Invoking Bedrock LLMs
- Returning structured RCA summaries

### Amazon Bedrock (LLM API)
Analyzes logs and generates:
- Root cause explanation
- Fix recommendation
- AWS best practice suggestions

### Application/UI
Displays RCA results through:
- API
- Chatbot
- Dashboard
- Slack/MS Teams integrations

---

# Workflow

1. User/Application asks:
   ```text
   Why did my Lambda function 'paymentProcessorFx' fail?
   ```

2. Bedrock Agent receives the query.

3. Agent invokes the Action Group.

4. Action Group triggers:
   ```text
   RCAlambdaFx
   ```

5. RCA Lambda:
   - Reads recent CloudWatch logs
   - Detects error patterns
   - Builds LLM prompt

6. Lambda invokes Amazon Bedrock.

7. Bedrock LLM generates:
   - Root Cause
   - Recommendation

8. Final RCA result is returned to the application/UI.

---

# Repository Structure

```text
smartRCAAssistant/
│
├── lambdaFunctions/
│   └── RCAlambdaFx.py
│
├── openAPISchema/
│   └── openAPI.yaml
│
└── README.md
```

---

# AWS Services Used

| Service | Purpose |
|----------|----------|
| Amazon Bedrock | LLM inference |
| Bedrock Agent | AI orchestration |
| AWS Lambda | RCA processing |
| Amazon CloudWatch Logs | Log storage |
| IAM | Permissions |
| OpenAPI | Agent action schema |
| EventBridge (optional) | Automated triggers |
| SNS/Slack (optional) | Notifications |

---

# Lambda Functions

## RCAlambdaFx

### Responsibilities
- Fetch Lambda logs from CloudWatch
- Filter critical errors
- Build AI prompts
- Invoke Bedrock LLM
- Parse RCA responses

### Supported Error Patterns
- Exceptions
- Timeouts
- Tracebacks
- AccessDenied
- Failed Invocations
- Runtime Errors

---

# Bedrock Agent Configuration

## Agent Name

```text
root-cause-analysis-agent
```

---

## Action Group Name

```text
RCA_agentLambda
```

---

# OpenAPI Integration

The Bedrock Agent uses an OpenAPI schema to invoke the Lambda function.

## Endpoint

```text
/analyzeLambdaError
```

---

## Example Input

```json
{
  "function_name": "paymentProcessorFx"
}
```

---

## Example Output

```json
{
  "functionAnalyzed": "paymentProcessorFx",
  "rootCause": "Lambda timed out while processing DynamoDB records.",
  "recommendation": "Optimize DynamoDB query or increase Lambda timeout configuration."
}
```

---

# Deployment Guide

## Step 1 — Create Lambda Function

Create:
```text
RCAlambdaFx
```

Runtime:
```text
Python 3.12
```

Upload:
```text
RCAlambdaFx.py
```

---

## Step 2 — Configure IAM Permissions

Attach permissions:

```json
{
  "Effect": "Allow",
  "Action": [
    "logs:FilterLogEvents",
    "logs:GetLogEvents",
    "logs:DescribeLogStreams",
    "bedrock:InvokeModel"
  ],
  "Resource": "*"
}
```

---

## Step 3 — Create Bedrock Agent

Agent Name:
```text
root-cause-analysis-agent
```

Select:
- Claude 3.5 Sonnet
- OR LLaMA3 model

---

## Step 4 — Create Action Group

Action Group:
```text
RCA_agentLambda
```

Attach:
- OpenAPI schema
- Lambda function

---

## Step 5 — Add Lambda Invoke Permission

Allow Bedrock to invoke Lambda:

```bash
aws lambda add-permission \
  --function-name RCAlambdaFx \
  --statement-id allow-bedrock \
  --action lambda:InvokeFunction \
  --principal bedrock.amazonaws.com
```

---

## Step 6 — Test the Agent

Example Query:

```text
Why did my Lambda function 'errorThrowingLambdaFx' fail?
```

---

# Prompt Engineering

The Lambda dynamically constructs prompts using:
- Error logs
- Stack traces
- Runtime exceptions
- AWS metadata

Example Prompt:

```text
Analyze the following Lambda logs and provide:
1. Root Cause
2. Recommendation
3. AWS best practice suggestions
```

---

# Sample RCA Output

## User Query

```text
Why did my Lambda function 'imageProcessorFx' fail?
```

## AI Response

```text
Root Cause:
The Lambda execution timed out while processing large image payloads.

Recommendation:
Increase Lambda timeout and optimize image processing logic using asynchronous workflows.
```

---

# Productivity Gains

- Faster root cause identification
- Reduced manual log analysis
- Improved operational efficiency
- Reduced downtime
- AI-assisted troubleshooting
- Lower MTTR for production incidents
