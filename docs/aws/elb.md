# AWS ELB

## Types of Load Balancer
* Application Load Balancer [Layer 7]
* Network Load Balancer [Layer 4] - AWS most expensive
* Classic Load Balancer - legacy purposes - non-recommended

### Application Load Balancers
* Best suited for load balancing of http and https traffic
* Operate at layer 7 and are application-aware
* Intelligent - you can create advanced request routing, sending specified requests to specific web servers

### Network Load Balancers
* Best suited for load balancing of TCP traffic where extremer performance is required
* Operating at the connection level (Layer 4)
* Capable of handling millions of requests per second while maintaining ultra-low latencies
* Use for extreme performance. [most expensive to buy]

### Classic Load Balancers
Legacy Elastic Load Balancers

* Load balance http/https applications and use Layer 7 specific features such as X-Forwarded and sticky sessions
* Can also use strict Layer 4 load balancing for applications that rely purely on the TCP protocol
* If application stops responding the ELB responds with a 504 error.
* This means app is having issues - at either Web Server or Database Layer
* Identify where the application is failing, and scale it up or out where possible (504 err)

X-Forwarded-For Header

How to get IPv4 public address when load balancer sends EC2 the private address? Uses X-Forwarded-For header
