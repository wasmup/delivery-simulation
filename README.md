# Systems Design Interview

## Design a 2-Hour Interval Order Delivery Simulation

The system receives finalized orders (with 2-hour intervals scheduled delivery time from 9am to 11pm for the next 4 days). To facilitate timely delivery, the system must send a courier request to a 3rd party logistics provider 1 hour prior to the scheduled delivery time via a web HTTP request, which has a 5% error rate.

The logistics provider responds with one of four possible status codes:

1. Courier Sourcing (status code 0)
2. Courier Assigned (status code 1)
3. Delivery Confirmed (status code 2)
4. Courier Unavailable (status code 3), indicating that no courier was found within 15 minutes.

The received status is then forwarded to the Core service, which initially requested the delivery, via an HTTP web-hook. However, this communication also has a 5% error rate, necessitating retry mechanisms.

The system architecture is as follows:

Core Service → Delivery Service → Logistics Provider (5% error rate, with retries)  
Logistics Provider → Delivery Service (status update) → Core Service (5% error rate, with retries)

Design Goals:

1. Reliability: Ensure that orders are delivered efficiently and reliably, despite potential errors and retries.
2. Error Handling: Implement robust error handling mechanisms to mitigate the impact of 5% error rates in both courier requests and status updates.
3. Scalability: Design the system to handle a high volume of orders and scale accordingly to meet demand.

Key Considerations:

1. Retry Mechanisms: Implement exponential backoff or similar strategies to handle retries and minimize the impact of errors.
2. Timeouts: Establish reasonable timeouts for courier requests and status updates to prevent indefinite waiting.
3. Queue Management: Consider using message queues to handle the high volume of orders and ensure that requests are processed efficiently.
4. Monitoring and Logging: Implement comprehensive monitoring and logging to track system performance, errors, and retries.

By addressing these design goals and considerations, the system can ensure reliable, efficient, and scalable order delivery, despite the complexities of error handling and retries. 
 
### Suggestions

1. Enhanced Retry Mechanism: To bolster the design, it is recommended that a detailed retry strategy be implemented, including specifics such as the initial delay, maximum delay, and the number of retries. For instance, an exponential backoff strategy could be employed, where the delay between retries increases exponentially (e.g., 1 second, 2 seconds, 4 seconds, etc.) up to a maximum delay (e.g., 1 minute). This approach would help mitigate the impact of errors and ensure timely retries.

2. Explicit Timeout Values: Establishing specific timeout values for both courier requests and status updates is crucial for preventing indefinite waiting and ensuring timely retries. For example, a timeout of 30 seconds could be set for the initial request, with subsequent retries having increasingly longer timeouts (e.g., 1 minute, 2 minutes, etc.). This would enable the system to balance between waiting for a response and retrying the request in a timely manner.

3. Configured Queue Management: To efficiently handle the high volume of orders, it is essential to configure the message queue with appropriate settings. This includes defining the queue size, consumer groups, and message time-to-live (TTL). For instance, a queue size of 10,000 messages could be set, with 5 consumer groups processing messages concurrently, and a TTL of 1 hour to ensure that messages are not retained indefinitely.

4. Error Rate Thresholds and Alerting: Defining thresholds for error rates is vital for identifying and addressing potential issues proactively. For example, if the error rate exceeds 10% for a certain period (e.g., 1 hour), an alert could be triggered to notify the operations team, enabling them to take corrective action. This would help prevent minor issues from escalating into major problems.
5. Detailed Interface Specifications: A more detailed description of the interface between the Core Service, Delivery Service, and Logistics Provider is necessary to clarify the interaction and data exchange between these components. This includes specifying API endpoints, request and response formats, and any relevant authentication or authorization mechanisms. For instance, the Core Service could use a RESTful API to send requests to the Logistics Provider, with the response format being JSON.
6. Scalability Metrics and Monitoring: Including specific metrics for measuring scalability is essential for evaluating the system's performance under various loads. For example, metrics such as orders per minute, system response time under load, and error rates could be monitored to provide a clear indication of the system's performance. This would enable the operations team to identify bottlenecks and optimize the system for improved scalability.

### Additional Considerations:

1. Idempotent Requests: Ensuring that courier requests and status updates are idempotent is crucial for preventing unintended side effects due to retries. This means that making the same request multiple times should have the same effect as making it once. For instance, if a courier request is retried due to a timeout, the Logistics Provider should be able to handle the duplicate request without assigning multiple couriers to the same order.
2. Courier Assignment Strategy: If the Logistics Provider assigns couriers based on certain criteria (e.g., proximity, availability), detailing this strategy could help in understanding the system's behavior under different scenarios. For example, the Logistics Provider could use a greedy algorithm to assign the closest available courier to each order, or a more complex algorithm that takes into account factors such as traffic and road conditions.
3. Feedback Loop and Adaptive Request Timing: Implementing a feedback loop where the Core Service can adjust its request timing or volume based on the response from the Logistics Provider could enhance the system's adaptability and efficiency. For instance, if the Logistics Provider reports a high error rate or courier unavailability, the Core Service could adjust its request timing to avoid peak hours or reduce the volume of requests to prevent overwhelming the Logistics Provider. This would enable the system to dynamically adapt to changing conditions and optimize its performance.

By incorporating these suggestions and considerations, the design can be further refined to ensure a robust, scalable, and reliable order delivery process that meets the needs of the business and its customers.

