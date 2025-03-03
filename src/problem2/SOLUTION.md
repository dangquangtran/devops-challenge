Provide your solution here:
1. Overview architecture
Architectural diagram includes:
-Frontend: React + AWS CloudFront + S3
-API Gateway: AWS API Gateway
-Microservices: Lambda & ECS Fargate
-Database: Amazon Aurora PostgreSQL + ElastiCache Redis
-Message Queue: Amazon SQS + Kafka
-Realtime Trading Engine: AWS Kinesis + WebSocket (via API Gateway)
-Monitoring & Security: AWS WAF, CloudWatch, IAM


2. Details of ingredients
Ingredient	        Describe	                    Why choose AWS?	                                        Alternatives
Frontend	        ReactJS + S3 + CloudFront	    Optimize static load and reduce latency	                Self-host on EC2
API Gateway	AWS     API Gateway	                    Provide WebSocket, attach JWT Auth	                    Traefik on ECS
Microservices	    ECS Fargate (Auto Scaling)	    No need to manage servers, flexible expansion	        EKS (Kubernetes)
Trading Engine	    AWS Kinesis + Lambda	        Real-time transaction processing with low latency	    Kafka + EC2
Database	        Aurora PostgreSQL	            Supports HA, read-replica, low-latency	                DynamoDB (NoSQL)
Cache	            ElastiCache Redis	            Reduce DB load, speed up queries	                    Memcached
Queue	            SQS + Kafka	                    Increase throughput, async processing	                RabbitMQ
Security	        AWS WAF, IAM, CloudTrail	    DDOS protection, access management	                    Cloudflare WAF


3. Expansion plan
-Automatically scale ECS Fargate based on CPU/memory usage
-Increase the number of Read Replica Aurora when DB load increases
-Use Multi-AZ for database to avoid downtime
-ElastiCache Redis clustering to scale memory
-Use AWS Global Accelerator to reduce latency for global users


4. Meet performance requirements
Solution                    Criteria
500 RPS                     API Gateway + ECS Auto Scaling
p99 < 100ms                 Redis caching + Kinesis streaming
Optimize DB                 Indexing, Read Replica, Connection Pooling
Frontend acceleration       CloudFront CDN, gzip compression