## Functional Requirements

Inventory Management: The store owner should be able to register products, edit prices and update stock balances.

Purchase Flow: The end customer should be able to view the product display, add items to ther cart, and make payments via credit card.

Notification: The system should send a confirmation email after payment approval.

Reports: The store owner needs a screen that shows the total sales per day.

## Constraints

The "Go-live" date is non-negotiable: 21 consecutive days.

Initial infrastructure budget of a maximum of USD 200/month.

## Non-Functional Requirements

### Cost Efficiency

Infrastructure must not exceed USD 200 per month

For MVP use single database deployment

Serverless or low-cost compute

### Availability

Maximum acceptable downtime = 7h 12m per month

The system must not remain unavailable for more than 15 consecutive minutes during business hours

Scheduled maintenance is permitted between 02:00 AM and 05:00 AM

### Scalability

System must support scaling without major architectural changes

Infrastructure should allow incremental scaling with minimal cost impact

### Security

External communication must use HTTPS

Access to the store owner must require authentication and authorization

Password must be stored using  secret vaults

Sensitive payment data must not be stored internally

The presentation Layer don't comunicate with Dabatase ou Persistence Layer

### Reliability and Fault Tolerance

If the email notification fails, the purchase must still be completed sucessfully

Payment confirmation must be persisted reliably in the database

### Maintainability

The codebase must follow modular design principles

Source code must be versioned using Git

Basic documentation must be available for deployment and operations

### Observability

The system must allow monitoring and trobleshooting

Application logs record: payment events, errors, authentication attempts

Logs retained for at least 30 days

Must alert the team in case of downtime

### Deployment

The system must support fast and safe deployments

Must be automated via CI/CD pipelines

Rollback procedures must be available in case of failure

### Data Integrity

Payment transactions must be ACID compliant

Transactions must be accurate



## SLA and SLO Considerations

SLA: The Legal Department finalized an agreement with the initial retailers for 99,0% monthly availability.

SLO: The CEO expects that the system supports the launch without stay offline for more than 15 consecutive minutes during business hours.

Maintenance Window: the Business authorizes scheduled maintenance between 2:00AM and 5:00AM, if necessary.