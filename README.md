# EC2 Monitoring and Remediation System

### <ins>System Overview</ins><br/>
The AWS EC2 Monitoring and Remediation System is a centralized, semi-automated ServiceNow incident response system that helps DevOps engineer teams quickly remediate failing AWS EC2 instances. It automatically detects when EC2 instances fail, creates incidents for faulty EC2 instances, provides AI-powered knowledge retrieval for remediation guidance, sends instance failure alert and remediation guidance through Slack notifications, and allows engineers to trigger AWS remediation directly from ServiceNow during critical incidents. By providing instant detection, notification, documentation, and remediation, the EC2 Monitoring and Remediation System greatly reduces incident response time and contributes to an improved customer experience. <br/>

### <ins>Implementation Overview</ins><br/>
Configuration of the EC2 Monitoring and Remediation System can be separated into three main components:<br/>
1. EC2 Monitoring Infrastructure<br/>
   o	Custom tables to track EC2 instance status and remediation attempts<br/>
   o	Secure AWS integration for automated remediation calls<br/>
2. DevOps Team Workflow<br/>
   o	One-click remediation interface for DevOps engineers using UI Action and Script Includes<br/>
   o	Log of all remediation attempts for audit and analysis<br/>
   o	Automated incident response<br/>
4. AI-Powered Knowledge Retrieval and Notification<br/>
   o	AI Search Custom action to surface relevant remediation documentation<br/>
   o	Knowledge base articles that AI can discover during incidents<br/>
   o	Slack notifications with retrieved knowledge articles to DevOps team<br/>

### <ins>Implementation Steps<ins/><br/>
After creating a scoped application: ***EC2 Monitoring and Remediation***, two tables must be created, which will serve as the brain of the EC2 monitoring infrastructure: 
The <ins>**EC2 Instance**</ins> table which will include records of Instance Name, Instance ID, and Instance Status as columns;<br/>
and the <ins>**Remediation Log**</ins> table which will populate records for Attempted Status, Success, Timestamp, Request Payload, Response Payload, Response Time, HTTP Status Code, and Error Messages, all in reference to the aforementioned EC2 Instance Table.<br/>

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/01043c8e-f2f4-4a06-86c6-245f7ebd5c0b" />
<br/>
<br/>
<br/>
<img width="2553" height="1315" alt="EC2 Instance table" src="https://github.com/user-attachments/assets/88cc41a9-a704-4e62-a194-c2286a6a1359" />
<img width="2555" height="388" alt="EC2 Instance sample" src="https://github.com/user-attachments/assets/a76249c0-3251-4b72-983f-634ef4f3da76" />
<br/>
<br/>
<br/>
<img width="2543" height="1313" alt="Rem Log table" src="https://github.com/user-attachments/assets/038cde3b-383e-4724-a3a1-ba8298077fe7" />
<img width="2555" height="821" alt="Rem Log sample" src="https://github.com/user-attachments/assets/9f44aff1-8fab-4e9a-93a1-554551140024" />
<br/>
<br/>
<br/>
Once the tables are in place, secure AWS integration for automated remediation calls is established through a <ins>credential alias</ins> (AWS Integration Server C C Alias), which will include an <ins>HTTP connection</ins> (AWS Inttegration Server Connection) to the AWS integration Server, as well as basic authorization <ins>credentials</ins> (AWS Integration Server Credentials) to the integration server.<br/>
<br/>
<img width="2555" height="1017" alt="Alias" src="https://github.com/user-attachments/assets/a54a4480-4013-4cf2-83f4-8a22c83e8309" />
<img width="2551" height="940" alt="HTTP connection" src="https://github.com/user-attachments/assets/9921334c-dc98-42e9-b2d5-34c850fd5b7b" />
<img width="2550" height="933" alt="Credentials" src="https://github.com/user-attachments/assets/c4c62f76-7d8c-46cd-a546-38ddc00d0953" />
<br/>
<br/>
<br/>
Having established connection to the AWS server, a manual remediation can be implemented that will be tested and logged for errors/success. A form button (Trigger EC2 Remediation) is created as a <ins>UI Action</ins> on the EC2 Instance record form, that will call a <ins>Script Include</ins> (EC2 Remediation Helper), in order to send an API call to the AWS Integration Server, and reestablish connection with the downed EC2 instance.<br/>
<br/>
<img width="2553" height="432" alt="Trigger Rem button" src="https://github.com/user-attachments/assets/a330d780-4a5d-4a74-a61a-4dd59573fabb" />
<img width="2545" height="1314" alt="UI action" src="https://github.com/user-attachments/assets/f1c6c695-356b-490a-a44b-cf5ba5283fd0" />
<img width="2544" height="1314" alt="script include" src="https://github.com/user-attachments/assets/bc84d53e-4684-4586-a73a-886c353ecb6d" />
<br/>
<br/>
<br/>
At this point, a manual remediation can be made to the EC2 instance every time it goes off. In order to ensure that appropriate DevOps team members are notified, a workflow is created to automate a new <ins>incident record</ins>, as well as a <ins>Slack notification</ins> that includes an <ins>AI search</ins> for relevant documentation from the Knowledge Base. 
<img width="1007" height="648" alt="workflow1" src="https://github.com/user-attachments/assets/11ee493b-d304-4994-a146-5631b637aed6" /><br/>
The workflow is triggered when a record is updated or created on the EC2 Instance table, and the Instance Status field is OFF. This is to ensure it captures all existing records that get updated, as well as any new instance records that are added in the future.
<img width="2232" height="647" alt="workflow trigger" src="https://github.com/user-attachments/assets/feca73b2-f880-404f-b691-e8302558d586" />
The triggered action creates a new record on the Incident table, describing the instance that is down. Impact/Urgency(Priority) are all set to 1- High. The Assignment Group is Networking Operations, and consequently, the Category is Network.
<img width="2223" height="952" alt="Workflow action" src="https://github.com/user-attachments/assets/4963d8da-4cba-4054-8bd9-99f6315dc616" />
Along with a new incident record for the failed instance, a Slack notification is sent to the DevOps team, detailing the instance name and ID that is down. Additionally, an AI search is performed for relevant documentation that is referenced in the Slack Notification.
<img width="2208" height="777" alt="Workflow AI search" src="https://github.com/user-attachments/assets/1cd2bce3-2b00-41f6-9a9b-843c94200713" />
<img width="2545" height="1046" alt="Workflow slack" src="https://github.com/user-attachments/assets/394ee0cb-021d-431c-9ef2-285e05d90cf8" />
After being triggered, the workflow completes its tasks and the Slack notification is sent to the DevOps team. Finally, the DevOps team has been instantly notified, and can remediate the EC2 instance as needed.
<img width="1485" height="258" alt="slack" src="https://github.com/user-attachments/assets/695fafb0-4e3a-45bb-a4ce-2565ed8bc00c" />

### <ins>Architecture Diagram</ins><br/>
<img width="1511" height="986" alt="Diagram" src="https://github.com/user-attachments/assets/b3875189-f072-4b53-ad93-beb81499c30e" /><br/>
### <ins>Optimization</ins><br/> 
The workflow has been created to minimize response time between incident and resolution. New incident records are instantly created the moment an EC2 Instance status is OFF. Slack notifications that follow have been designed to dynamically retrieve the failed instance name and ID for faster response. Additionally, the AI search function retrieves relevant documentation to reduce DevOps delays. In a future iteration or second version of this system, I might want to add an automated email notification to DevOps users that is triggered upon creation of the new incident. Despite the redundancy, it would capture more margins of error or missed areas of opportunity. Additionally, I could play with the idea of systemizing a remediation workflow trigger link within the Slack notification message to forego having to manually look for the individual failed instance and toggling the UI Action button. Taking it even a step further, we could remove the middle man and create a fully automated agentic workflow that triggers the EC2 remediation any time an instance status is OFF, to resolve the issue autonomously. Of course, these ideas haven't been properly formulated, tested, or created *yet.*
### <ins>DevOps Usage</ins><br/> 
<img width="1209" height="1016" alt="KB Article" src="https://github.com/user-attachments/assets/1f7f14b7-099c-4ace-8227-69c11136c794" />
