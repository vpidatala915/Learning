1. Requirements gathering

Topics:

Functional requirements
Non-functional requirements
Scale estimation : basic back of the envelope estimation
Throughput, latency, availability
Read vs write patterns
Consistency needs
Security and compliance
Cost constraints
Multi-region or single-region needs - points to availability

What to know:

DAU / MAU
QPS / RPS
Peak traffic
Data size growth
SLA / SLO / error budget
2. Core architecture

Topics:

Monolith vs microservices
Service-oriented architecture
Modular monolith
Event-driven architecture
Layered architecture
Hexagonal / clean architecture
Serverless architecture
Multi-tenant architecture

Technologies:

REST
gRPC
GraphQL
WebSockets
API Gateway
Service mesh
3. Networking fundamentals

Topics:

DNS
TCP / UDP
HTTP / HTTPS
HTTP/1.1 vs HTTP/2 vs HTTP/3
TLS / SSL
Proxies and reverse proxies
NAT
Load balancing
Connection pooling
Keep-alive
CDN routing

Technologies:

Nginx
Envoy
HAProxy
Cloudflare
Route 53
Akamai
4. API design

Topics:

Resource modeling
Idempotency
Pagination
Filtering / sorting
Versioning
Rate limiting
Retries
Timeouts
Error handling
Backward compatibility

Technologies:

OpenAPI / Swagger
REST
gRPC
GraphQL
Postman
5. Load balancing and traffic management

Topics:

L4 vs L7 load balancers
Round robin
Least connections
Weighted routing
Sticky sessions
Health checks
Failover routing
Blue-green deployment
Canary release
Geo-routing

Technologies:

AWS ELB / ALB / NLB
GCP Load Balancer
Azure Load Balancer / App Gateway
Nginx
HAProxy
Envoy
6. Caching

Topics:

Client-side cache
CDN cache
Reverse proxy cache
Application cache
Database cache
Read-through / write-through / write-back
Cache eviction
TTL
Cache invalidation
Hot keys
Cache stampede
Distributed cache

Technologies:

Redis
Memcached
Varnish
CloudFront
Fastly
Akamai
7. Databases
Relational databases

Topics:

ACID
Normalization / denormalization
Indexes
Joins
Transactions
Replication
Partitioning / sharding
Read replicas
Query optimization

Technologies:

PostgreSQL
MySQL
SQL Server
Oracle
NoSQL databases

Topics:

Key-value stores
Document stores
Wide-column stores
Time-series DBs
Tradeoffs vs RDBMS
Eventual consistency
Flexible schema

Technologies:

DynamoDB
MongoDB
Cassandra
HBase
Couchbase
InfluxDB
TimescaleDB
8. Storage systems

Topics:

Blob/object storage
Block storage
File storage
Cold vs hot storage
Replication
Durability
Lifecycle management
Backup / restore
Archival

Technologies:

Amazon S3
Azure Blob Storage
Google Cloud Storage
EBS
NFS
HDFS
9. Data partitioning and sharding

Topics:

Vertical partitioning
Horizontal partitioning
Consistent hashing
Range-based sharding
Hash-based sharding
Directory-based sharding
Rebalancing
Hot partitions
Cross-shard queries

Technologies:

Vitess
Citus
Cassandra partitioning
Dynamo-style partitioning
10. Replication and consistency

Topics:

Leader-follower replication
Multi-leader replication
Leaderless replication
CAP theorem
Strong consistency
Eventual consistency
Quorum reads/writes
Read-your-writes
Monotonic reads
Split brain
Consensus

Technologies:

Raft
Paxos
ZooKeeper
etcd
Consul
11. Messaging and asynchronous processing

Topics:

Message queues
Publish-subscribe
Event streaming
At-most-once / at-least-once / exactly-once
Dead letter queues
Backpressure
Consumer groups
Ordering guarantees
Idempotent consumers
Event replay

Technologies:

Kafka
RabbitMQ
ActiveMQ
AWS SQS / SNS
Google Pub/Sub
Azure Service Bus
Pulsar
12. Background jobs and schedulers

Topics:

Job queues
Cron jobs
Retry policies
Delayed jobs
Workflow orchestration
Distributed scheduling
Dependency chains

Technologies:

Celery
Sidekiq
Quartz
Airflow
Temporal
Argo Workflows
13. Search systems

Topics:

Full-text search
Inverted index
Ranking
Typo tolerance
Faceted search
Autocomplete
Relevance scoring
Search indexing pipeline

Technologies:

Elasticsearch
OpenSearch
Solr
Lucene
Algolia
14. Real-time systems

Topics:

WebSockets
SSE
Long polling
Presence systems
Streaming pipelines
Low-latency messaging
Fan-out
Ordering and delivery guarantees

Technologies:

Kafka
Redis Streams
WebSocket gateways
Socket.IO
Pub/Sub systems
15. Distributed systems fundamentals

Topics:

Consensus
Coordination
Failure detection
Heartbeats
Clock skew
Logical clocks
Vector clocks
Leader election
Distributed locking
Idempotency
Retries
Duplicate suppression

Technologies:

ZooKeeper
etcd
Consul
Raft-based systems
16. Reliability and fault tolerance

Topics:

Redundancy
Failover
Graceful degradation
Retries with backoff
Circuit breakers
Bulkheads
Timeouts
Hedged requests
Disaster recovery
Chaos testing

Technologies:

Hystrix concepts
Resilience4j
Istio
Chaos Monkey
LitmusChaos
17. Availability and disaster recovery

Topics:

Single AZ / multi-AZ / multi-region
Active-active
Active-passive
RTO
RPO
Backup strategies
Point-in-time recovery
Regional failover

Technologies:

Route 53 failover
Aurora Global Database
Multi-region Redis / DB setups
Cloud disaster recovery services
18. Scalability

Topics:

Vertical scaling
Horizontal scaling
Stateless services
Auto-scaling
Bottleneck analysis
Fan-out bottlenecks
Partitioning for scale
Load shedding

Technologies:

Kubernetes HPA
ASG
Cloud auto-scaling groups
Service mesh autoscaling
19. Security

Topics:

Authentication
Authorization
RBAC / ABAC
OAuth
OpenID Connect
JWT
Session management
Secrets management
Encryption at rest / in transit
Key rotation
WAF
DDoS protection
Audit logs

Technologies:

OAuth 2.0
OIDC
Keycloak
Auth0
Azure AD / Entra ID
AWS IAM
Vault
KMS
WAF
20. Observability and operations

Topics:

Logging
Metrics
Tracing
Alerting
SLIs / SLOs / SLAs
Dashboarding
Correlation IDs
Incident response
Runbooks

Technologies:

Prometheus
Grafana
ELK stack
Loki
Jaeger
Zipkin
OpenTelemetry
Datadog
New Relic
Splunk
21. Deployment and DevOps

Topics:

CI/CD
Rolling deployment
Blue-green deployment
Canary deployment
Feature flags
Rollback
Immutable infrastructure
Infrastructure as code

Technologies:

Jenkins
GitHub Actions
GitLab CI
ArgoCD
Spinnaker
Terraform
Pulumi
Ansible
22. Containers and orchestration

Topics:

Containers
Image lifecycle
Resource requests/limits
Scheduling
Service discovery
Secrets/configs
Sidecars
Init containers

Technologies:

Docker
Kubernetes
Helm
Kustomize
OpenShift
Nomad
23. Service discovery and configuration

Topics:

Dynamic service registration
Discovery by DNS
Centralized config
Distributed config refresh
Feature toggles

Technologies:

Consul
Eureka
ZooKeeper
etcd
Spring Cloud Config
ConfigMaps / Secrets
24. CDN and edge computing

Topics:

Static asset distribution
Edge caching
Cache purge
Geographic latency optimization
Edge compute
DDoS shielding

Technologies:

Cloudflare
CloudFront
Fastly
Akamai
Vercel Edge
Workers
25. Data processing and analytics

Topics:

ETL / ELT
Batch processing
Stream processing
Data warehouse
Data lake
Aggregation pipelines
OLTP vs OLAP

Technologies:

Spark
Flink
Hadoop
BigQuery
Snowflake
Redshift
Databricks
Airflow
26. Specialized databases / use-case systems

Topics:

Time-series workloads
Graph workloads
Search workloads
Vector search
Geospatial queries

Technologies:

Neo4j
JanusGraph
InfluxDB
TimescaleDB
Elasticsearch
Pinecone
Weaviate
Milvus
pgvector
27. File and media systems

Topics:

Image/video upload
Pre-signed URLs
Media transcoding
Chunked uploads
Thumbnail generation
Content moderation pipelines

Technologies:

S3
Cloudinary
FFmpeg
CDN
MediaConvert
28. Rate limiting and abuse prevention

Topics:

Token bucket
Leaky bucket
Fixed window
Sliding window
IP throttling
User-level throttling
CAPTCHA
Spam prevention

Technologies:

Redis
API Gateway throttling
Envoy rate limiting
Cloudflare bot protection
29. Financial / transactional design topics

Topics:

Idempotent payments
Ledger systems
Double-entry bookkeeping
Reconciliation
Exactly-once semantics
Fraud checks
Auditability

Technologies:

Kafka
SQL databases
Redis
Payment gateways
Event sourcing patterns
30. Domain-specific design topics often asked

Examples:

URL shortener
Tiny pastebin
Chat system
Notification service
News feed
Rate limiter
Parking lot
Ticket booking
Ride sharing
Food delivery
Video streaming
Search autocomplete
File storage like Google Drive
Web crawler
Ad click tracking
Metrics/telemetry pipeline
31. Interview-specific design thinking topics

Topics:

Estimation
Tradeoff analysis
Bottleneck identification
Capacity planning
Choosing between SQL and NoSQL
Choosing sync vs async
Choosing consistency vs availability
API sketching
Data model sketching
Scaling plan from 1x to 100x
Failure mode analysis
32. Advanced concepts

Topics:

Event sourcing
CQRS
Saga pattern
Outbox pattern
Change data capture
Distributed transactions
Stream-table duality
Multi-tenancy isolation
Zero-downtime migration
Online schema changes

Technologies:

Debezium
Kafka Connect
Temporal
EventStore
CDC pipelines
33. Frontend-aware system design

Topics:

SSR vs CSR
CDN delivery
Asset versioning
Browser caching
BFF pattern
Web performance
Realtime UI updates

Technologies:

React
Next.js
CDN
Edge rendering
GraphQL
BFF services
34. AI / modern system design topics

Topics:

Vector databases
Embeddings
RAG architecture
Inference serving
Model routing
Prompt caching
Token limits
Evaluation pipelines
Safety filters
Hallucination mitigation
Model observability

Technologies:

OpenAI / Azure OpenAI
vLLM
Triton Inference Server
LangChain
LlamaIndex
Pinecone
Weaviate
Milvus
pgvector
OpenTelemetry for LLMs
35. Cost and efficiency

Topics:

Storage cost optimization
Compute cost optimization
Egress costs
Cache vs DB cost tradeoff
Reserved vs on-demand capacity
Compression
Tiered storage
36. Common diagrams you should know how to draw
High-level architecture diagram
Request flow diagram
Sequence diagram
Database schema
Sharding diagram
Caching layers
Retry/failure flow
Deployment diagram
Multi-region failover diagram
If you want the shortest “master checklist”

These are the must-cover pillars:

Requirements
APIs
Load balancers
Caching
Databases
Sharding
Replication
Messaging/queues
Consistency
Scalability
Availability
Reliability
Security
Observability
Deployment/DevOps
Tradeoffs
Cost
Good way to study this

Study in this order:

Requirements and estimation
Load balancer + API gateway + app servers
Cache
Database + indexing + replication + sharding
Queue / async processing
Availability + consistency + reliability
Security + observability
Deployment + scaling
Practice common design problems

I can turn this into a clean interview-ready roadmap table next.