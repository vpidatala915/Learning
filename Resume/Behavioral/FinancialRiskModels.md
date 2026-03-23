## Owned the Financial Risk Model Deployment Platform—a scalable analytics infrastructure for model runs—delivering features like simultaneous forecasting and runtime diagnostics using  C#, ASP.NET, Kafka, Grafana  


             ┌───────────────┐
             │   User/API    │
             └──────┬────────┘
                    │
                    ▼
           ┌──────────────────┐
           │ ASP.NET Service  │
           │ (job submission) │
           └──────┬───────────┘
                  │
                  ▼
           ┌──────────────────┐
           │ Kafka Queue      │
           │ (job pipeline)   │
           └──────┬───────────┘
                  │
                  ▼
           ┌──────────────────┐
           │ Worker Services  │
           │ (run models)     │   
           └──────┬───────────┘
                  │
                  ▼
           ┌──────────────────┐
           │ Metrics + Logs   │
           │ (runtime data)   │
           └──────┬───────────┘
                  │
                  ▼
           ┌──────────────────┐
           │ Grafana Dashboard│
           │ (diagnostics UI) │
           └──────────────────┘

### Simulatenous Forecast Run
                   ┌──────────────────────┐
                   │   Client / UI / API  │
                   └──────────┬───────────┘
                              │
                              ▼
                   ┌──────────────────────┐
                   │   API Layer          │
                   │ (ASP.NET / Gateway)  │
                   └──────────┬───────────┘
                              │
                              ▼
                   ┌──────────────────────┐
                   │   Job Queue (Kafka)  │
                   │  (partitioned)       │
                   └──────────┬───────────┘
                              │
         ┌────────────────────┼────────────────────┐
         ▼                    ▼                    ▼
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│ Worker Pool 1   │  │ Worker Pool 2   │  │ Worker Pool N   │
│ (Orchestrator)  │  │ (Orchestrator)  │  │ (Orchestrator)  │
└────────┬────────┘  └────────┬────────┘  └────────┬────────┘
         │                    │                    │
         └──────────────┬─────┴──────────────┬─────┘
                        ▼                    ▼

         ┌────────────────────────────────────────────┐
         │ Distributed Compute Layer (Spark / EMR)    │
         │ - parallel data processing                 │
         │ - batch / streaming jobs                  │
         └──────────────┬────────────────────────────┘
                        │
                        ▼
         ┌────────────────────────────────────────────┐
         │   Data Storage Layer                       │
         │  - S3 / Data Lake (raw + processed data)   │
         │  - Parquet / partitioned datasets          │
         └──────────────┬────────────────────────────┘
                        │
                        ▼
         ┌────────────────────────────────────────────┐
         │   Result Storage                           │
         │  - S3 (outputs)                            │
         │  - DB (job metadata/status)                │
         └────────────────────────────────────────────┘
                        │
                        ▼
         ┌────────────────────────────────────────────┐
         │ Observability Layer                        │
         │ - Metrics (Prometheus / CloudWatch)        │
         │ - Logs                                     │
         │ - Grafana dashboards                       │
         └────────────────────────────────────────────┘
## Worker service
Reads jobs from Kafka
Executes the model
Tracks progress
Emits metrics/logs
Updates job status


## Example Model Run
### Input: Loan data, transactions, historical risk data
### Output: Risk score / forecast / predictions

public async Task Execute(JobMessage job)
{
    // 1. Load
    var data = LoadFromS3(job.InputLocation);

    // 2. Preprocess
    var cleaned = Clean(data);

    // 3. Feature engineering
    var features = Transform(cleaned);

    // 4. Run model
    var predictions = ModelPredict(features);

    // 5. Save results
    SaveResults(job.JobId, predictions);
}


### What is Spark
Spark is a distributed data processing engine that allows us to process large datasets in parallel. In our system, the worker triggers a Spark job, which reads data from S3, processes it across multiple nodes, and writes the results back, enabling us to scale model execution efficiently



### Runtime diagnostics
Worker → triggers job → Spark runs model
                      ↓
                emits metrics
                      ↓
              Monitoring system
                      ↓
                Grafana dashboards


# Metrics
App → Prometheus / CloudWatch → Grafana
data_load_time
preprocessing_time
feature_engineering_time
model_execution_time
write_time
rows_processed
records_per_second
memory_usage
cpu_usage
disk_io
job_submitted
job_running
job_completed
job_failed
failure_stage
error_type
retry_count
kafka_lag
job_queue_depth
worker_utilization

## Grafana dashboard
Job Overview
- Total jobs running
- Success vs failure rate
- Avg job duration

Bar chart:
- data load time
- preprocessing time
- model time

Time series:
records/sec over time

CPU %
Memory %
per worker / per job

Top failure reasons:
- out of memory
- bad input
- timeout

Lag per partition

## Resume - Managed AWS infrastructure as code using Terraform (EKS, Lambda, and supporting services), enabling reproducible environments, safer changes, and consistent deployments across teams.
                   ┌──────────────────────────────┐
                   │   Client / UI / API          │
                   └──────────────┬───────────────┘
                                  │
                                  ▼
                   ┌──────────────────────────────┐
                   │ Ingress (ALB / Nginx)        │
                   └──────────────┬───────────────┘
                                  │
                                  ▼
                   ┌──────────────────────────────┐
                   │ API Service (ClusterIP)      │
                   └──────────────┬───────────────┘
                                  │
                                  ▼
                   ┌──────────────────────────────┐
                   │ API Pods (ASP.NET Deployment)│
                   └──────────────┬───────────────┘
                                  │
                                  ▼
                   ┌──────────────────────────────┐
                   │ Kafka (MSK or StatefulSet)   │
                   │ (partitioned topics)         │
                   └──────────────┬───────────────┘
                                  │
         ┌────────────────────────┼────────────────────────┐
         ▼                        ▼                        ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│ Worker Pods     │    │ Worker Pods     │    │ Worker Pods     │
│ (Deployment)    │    │ (Deployment)    │    │ (Deployment)    │
└────────┬────────┘    └────────┬────────┘    └────────┬────────┘
         │                      │                      │
         └──────────────┬───────┴──────────────┬───────┘
                        ▼                      ▼

        ┌──────────────────────────────────────────────┐
        │ Spark on Kubernetes (Driver + Executors)     │
        │ (SparkOperator / EMR on EKS)                 │
        └──────────────┬───────────────────────────────┘
                       │
                       ▼
        ┌──────────────────────────────────────────────┐
        │ S3 (Data Lake + Results Storage)             │
        │ - input data                                │
        │ - output results                            │
        └──────────────┬───────────────────────────────┘
                       │
                       ▼
        ┌──────────────────────────────────────────────┐
        │ Observability Stack                          │
        │ - Prometheus (metrics)                       │
        │ - Grafana (dashboards)                       │
        │ - Fluentd (logs)                             │
        └──────────────────────────────────────────────┘

        ---------------------------------------------

## Problem before your change
- Anyone could:
- click buttons
- modify customer data
- perform actions

## What we 
Only authorized users can perform actions


## DESIGN

User logs in
   ↓
UI gets user role/permissions
   ↓
UI decides:
 - show button
 - hide button
 - disable button
   ↓
If user clicks allowed button
   ↓
API request sent with token
   ↓
Backend checks permission again
   ↓
If allowed → execute action + audit log
If not allowed → 403

┌──────────────────────────────┐
│ User opens application       │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Redirect to Identity Provider│
│ (OAuth/OIDC login)           │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ User authenticates           │
│ (username/password/SSO)      │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Identity Provider issues     │
│ JWT (token with roles/claims)│
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Angular UI receives token    │
│ - extracts roles/permissions │
│ - stores token (memory)      │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ UI renders page              │
│ - hides unauthorized buttons │
│ - disables restricted actions│
└──────────────┬───────────────┘
               │
     (User clicks a button)
               │
               ▼
┌──────────────────────────────┐
│ UI sends API request         │
│ Authorization: Bearer JWT    │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Backend API                  │
│ - validates JWT signature    │
│ - extracts roles/claims      │
│ - checks permission policy   │
└──────────────┬───────────────┘
               │
        allowed?
       ├──────────────► 403 Forbidden
       │
       ▼
┌──────────────────────────────┐
│ Business Logic / Action      │
│ (customer operation)         │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Audit Logging                │
│ - userId                     │
│ - action                     │
│ - customerId                 │
│ - timestamp                  │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Response to UI               │
│ success / failure            │
└──────────────────────────────┘




## Complete Design
                                      ┌──────────────────────────────┐
                                      │ Identity Provider            │
                                      │ (OAuth / OIDC)              │
                                      │ - login / SSO               │
                                      │ - issues JWT with claims    │
                                      └──────────────┬──────────────┘
                                                     │
                                                     │ JWT token
                                                     ▼
┌──────────────────────────────┐        ┌──────────────────────────────┐
│ User / Client                │───────▶│ Angular UI / Frontend        │
│ - opens app                  │        │ - reads token claims         │
│ - clicks buttons             │        │ - hides/disables actions     │
└──────────────┬───────────────┘        │ - sends API requests         │
               │                        └──────────────┬──────────────┘
               │                                       │
               │ HTTPS + Bearer Token                  │
               ▼                                       ▼
                   ┌──────────────────────────────┐
                   │ Ingress (ALB / Nginx)        │
                   └──────────────┬───────────────┘
                                  │
                                  ▼
                   ┌──────────────────────────────┐
                   │ API Service (ClusterIP)      │
                   └──────────────┬───────────────┘
                                  │
                                  ▼
                   ┌──────────────────────────────┐
                   │ API Pods (ASP.NET Deployment)│
                   │ - validate JWT               │
                   │ - authorize action           │
                   │ - write audit event          │
                   │ - create job                 │
                   │ - publish to Kafka           │
                   └──────────────┬───────────────┘
                                  │
                                  │ audit / job metadata
                                  │
              ┌───────────────────┴───────────────────┐
              ▼                                       ▼
┌──────────────────────────────┐       ┌──────────────────────────────┐
│ Audit Log Store              │       │ Job Metadata Store           │
│ - who did what               │       │ - jobId                      │
│ - customerId                 │       │ - submitted/running/failed   │
│ - timestamp                  │       │ - progress / stage           │
└──────────────────────────────┘       └──────────────────────────────┘
                                  │
                                  ▼
                   ┌──────────────────────────────┐
                   │ Kafka (MSK or StatefulSet)   │
                   │ - partitioned topics         │
                   │ - buffers jobs               │
                   └──────────────┬───────────────┘
                                  │
         ┌────────────────────────┼────────────────────────┐
         ▼                        ▼                        ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│ Worker Pods     │    │ Worker Pods     │    │ Worker Pods     │
│ (Deployment)    │    │ (Deployment)    │    │ (Deployment)    │
│ - consume jobs  │    │ - consume jobs  │    │ - consume jobs  │
│ - update status │    │ - update status │    │ - update status │
│ - emit metrics  │    │ - emit metrics  │    │ - emit metrics  │
│ - trigger Spark │    │ - trigger Spark │    │ - trigger Spark │
└────────┬────────┘    └────────┬────────┘    └────────┬────────┘
         │                      │                      │
         └──────────────┬───────┴──────────────┬───────┘
                        │                      │
                        ▼                      ▼
        ┌──────────────────────────────────────────────┐
        │ Spark on Kubernetes (Driver + Executors)     │
        │ (SparkOperator / EMR on EKS)                 │
        │ - reads large input data from S3             │
        │ - preprocesses / transforms data             │
        │ - runs model execution in parallel           │
        │ - writes outputs back to S3                  │
        │ - emits stage/runtime metrics                │
        └──────────────┬───────────────────────────────┘
                       │
                       ▼
        ┌──────────────────────────────────────────────┐
        │ S3 (Data Lake + Results Storage)             │
        │ - raw input data                             │
        │ - processed data                             │
        │ - model outputs                              │
        │ - diagnostics artifacts                      │
        └──────────────┬───────────────────────────────┘
                       │
                       ▼
        ┌──────────────────────────────────────────────┐
        │ Observability Stack                          │
        │ - Prometheus / CloudWatch metrics            │
        │ - Grafana dashboards                         │
        │ - Fluentd / logs                             │
        │ - job runtime diagnostics                    │
        └──────────────────────────────────────────────┘