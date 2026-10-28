# AI-Powered Customer Support Ticketing System

A Salesforce Service Cloud project for a telecom support team. It automatically sets ticket priority and SLA deadlines, escalates overdue tickets, and requires resolution notes before closing. An Agentforce AI agent summarizes tickets so agents can resolve issues faster.

**Tech stack:** Salesforce, Apex, Agentforce, Service Console, Reports and Dashboards

## Features
- **Auto-priority:** Network Outage tickets are always set to High. Tickets without a priority become Medium.
- **SLA tracking:** High = 4 hours, Medium = 24 hours, Low = 72 hours.
- **Auto-escalation:** A scheduled batch job escalates open tickets that pass their SLA.
- **Validation:** A ticket cannot be closed without Resolution Notes.
- **AI summary:** An Agentforce agent summarizes a ticket and saves it on the case.
- **Security:** A `Support_Agent` permission set controls field and class access.

## Project Structure
| Component | Purpose |
|---|---|
| Case custom fields | Service Type, SLA Due Date, AI Summary, Resolution Notes |
| `CaseTrigger` / `TicketTriggerHandler` | Sets priority and SLA on create and update |
| `TicketSLAService` | SLA and priority rules |
| `TicketEscalationBatch` | Escalates overdue tickets |
| `TicketSummaryAction` | Apex action used by the Agentforce agent |
| Validation rule | Resolution Notes required on close |
| `Support_Agent` | Permission set |
| `TicketTests` | Apex test class |
| `data/sample_tickets.csv` | Sample data |

## Setup
1. Create a free Salesforce Developer Edition org.
2. Deploy the project:
   ```
   sf org login web --alias myorg
   sf project deploy start --source-dir force-app --target-org myorg
   ```
3. Run the tests:
   ```
   sf apex run test --class-names TicketTests --target-org myorg --result-format human
   ```
4. Import `data/sample_tickets.csv` with the Data Import Wizard (Setup > Data Import Wizard > Cases).
5. Schedule the hourly escalation in Developer Console > Execute Anonymous:
   ```apex
   System.schedule('Hourly SLA Escalation', '0 0 * * * ?', new TicketEscalationBatch());
   ```

## Declarative Configuration
1. Create queues: Network Support, Billing Support, Activation Support.
2. Add Case Assignment Rules based on Service Type.
3. Create a Service Console app and add the new fields to the Case page layout.
4. Build reports: Cases by Priority, SLA Breaches, Open Cases by Owner. Add them to a Support Dashboard.
5. Set Case sharing to Private and grant access through roles and queues.

## Agentforce Setup
1. Go to Setup > Agentforce Agents > New Agent (Service Agent).
2. Create a topic called "Ticket Support".
3. Add the Apex action **Summarize Support Ticket**.
4. Test in Agent Builder, for example: "Summarize case 00001001".
5. Activate the agent and assign the `Support_Agent` permission set.

## Demo
1. Create a Network Outage ticket and show High priority and the SLA Due Date.
2. Ask the agent to summarize the ticket.
3. Try to close a ticket without notes and show the validation error.
4. Show the dashboard.

Demo video: <add your link here>
