## Scenario: Real-Time Lead Synchronization for Acme Sales Solutions
### Business Context

Acme Sales Solutions is a rapidly growing organization that runs multiple marketing campaigns to capture leads. 
These leads are stored in a MySQL database and need to be synchronized with Acme’s SaaS CRM (Salesforce) to ensure that the sales team always has up-to-date information. 
The business has noticed significant fluctuations in lead volumes throughout the day and now requires an integration solution that can handle these variable loads while meeting strict latency, auditability, and error recovery requirements.

Use Case Requirements

Data Volume and Timing
- Average Load: Approximately 30,000 new leads per day.
- Peak Load: Up to 10,000 new leads in one hour during peak times.
- Update Frequency: New leads must be made available in Salesforce every hour.
- Latency Expectation: Although the synchronization can occur hourly, the CRM system should be updated as quickly as possible within that timeframe.

Integration Characteristics
- One-Way Synchronization: Data flows from the MySQL database to Salesforce.
- SaaS Integration: The target system (Salesforce) is a cloud-based SaaS CRM, which has its own API limits and response time characteristics.
- Auditability & Traceability: Every record processed must be auditable. This means that you must capture detailed logs or records that trace the processing steps for each lead.
- Error Handling & Human Intervention: In the event of a record failing to process (e.g., due to validation errors or API timeouts), the system must support human intervention. Failed records should be isolated and made available for post-processing or manual reprocessing.

Infrastructure Constraints
- Deployment Environment: The Mule application is to be deployed on CloudHub using one worker sized at 0.2 vCore with 1GB of heap memory.
- Resource Efficiency: Given the constrained resources, the solution must be designed to optimize memory and CPU usage, ensuring that high-volume processing does not cause performance degradation or resource exhaustion.

### Exercise Questions
- What are the benefits of choosing an asynchronous integration style for this scenario?
- Propose a high-level design that uses Mule batch processing combined with asynchronous sub-flows. What would the sequence of events look like from data extraction to Salesforce update?
- How would you implement auditing to ensure every record’s lifecycle is traceable?
- Describe an error handling strategy that allows for automatic retries and manual post-processing of failed records.

### Guiding Diagram Solution
```mermaid
graph TD
    A[Scheduler Trigger] -->|Every Hour| B[Lead Polling Flow]
    B -->|Query New Leads| C[(MySQL DB)]
    B -->|Create Sync Record| D[(Audit DB)]
    B -->|Batch Leads| E[Lead Processing Flow]
    E -->|Transform| F[Salesforce Sync Flow]
    F -->|API Calls| G[Salesforce]
    F -->|Failed Records| H[Error Queue]
    F -->|Update Status| D
    I[Manual Reprocessing Flow] -->|Retrieve Failed| H
    I -->|Reprocess| F
    
    end
```
