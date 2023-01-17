# Failover MI

Start with:

> docker compose up

Test with:

> curl http://localhost:8290/health/hospital/SVQ002 -H 'Content-Type: application/json' -w "\n" | jq -c

> wrk -d60s -t1 -c 1 http://localhost:8290/health/hospital/SVQ002

Just stop the blue instance with `docker stop hospital-blue` and, after several seconds, just restart with `docker start hospital-blue` during the test above.

```xml
<endpoint name="Blue">
    <http method="get" uri-template="http://hospital-blue:8080/hospital/{uri.var.hospitalId}">
        <suspendOnFailure>
            <initialDuration>5000</initialDuration>
        </suspendOnFailure>
        <markForSuspension>
            <errorCodes>101507,101508,101505,101506,101509,101500,101510,101001,101000,101503,101504,101501</errorCodes>  
            <retriesBeforeSuspension>0</retriesBeforeSuspension>
            <retryDelay>1000</retryDelay>
        </markForSuspension>
        <timeout>
            <duration>200</duration>
            <responseAction>fault</responseAction>
        </timeout>
    </http>
</endpoint>
```

Following are the important configuration details of above example:
    1. **initialDuration** - Once the endpoint reach to 'suspended' state ( 'open' state  in Circuit Breaker pattern), it waits for 5000 ms to perform next retry.
    2. **retriesBeforeSuspension** - This is the failure count to move endpoint into 'Suspended' ( 'open' state  in Circuit Breaker pattern) state. 
    3. **retryDelay** - This is the delay in between failure calls.  