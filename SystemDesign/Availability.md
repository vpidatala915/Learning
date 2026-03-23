Availability = Uptime/Totaltime or Successful Requests/Total Requests

Higher availability is acheived at the cost of higher complexity and operating costs

According to SRE reports. Most outages occur because
1. Bad deployment
2. Configuration Errors
3. Dependency Failures
4. Traffic Spikes
5. Hardware Failure
6. Region outages 

This is why SRE practise emphasizes:
1. Safe deployment practise
2. Monitoring
3. Gradual Rollouts

As a design to acheive avaiallity we need to:
1. Avoid single point of failure
2. Better architecture - load balancer
    Client -> load balamcer -- multiple API Servere == Replicated Database
3. Multiple Availablity Zones
    1. Independent Zones -- this means we IP address given to Loadbalancer, API server should from different avabilty zones as these zones have individual power, networking and separate physical infastructure.
4. Database Avaailbity:
    Datrabasers are often the largest avaialbilty risk
    1. Primary DBs and replicas
        Primary handles writes
        Replicas handle reads