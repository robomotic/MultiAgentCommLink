# MultiAgentCommLink

# Context
In the not so far future each major vendor will provide Intelligent Assistants (IA) that can cooperate to solve complex tasks for a human operator.

Those tasks can be very daily routine for example healthcare, car, transport, holiday, food, deliveries and so on.

# Healthcare Example
A person has a health issue and needs to contact a doctor, but the doctor is not available and so asks for a IA healthcare about his conditions. The IA Healthcare then contact his medical report database IA (proviate or public provider) for medication and previous conditions, it formulates hypothesis and then find possible specialist advisor in the area of the person. It auto schedule and book based on calendar IA the best provider that is supported by his private or public insurance plan via the insurance IA.
After the surgergy is completed it will also provide invoices and payment to the insurance IA, it will
also update the medical records and will auto order the medications required which will be auto shipped by another IA.
Another IA will track his progress through feedback and biomedical sensors like heart reate and samples that will be autoscheduled and booked and so forth.
This scenario involves at least 5-8 IA agents that need different access to information and authorization to perfrom actions on behalf of the user.

# Requirements
We want to develop a common protocol that will allow IA agents to communicate and coordinate their actions in a secure and efficient way.
The protocol should provide:
* Authorization: the IA agents need to be authorized by the human operators or the vendors to perform certain actions on behalf of other agents and humans.
* Authentication: both for human operators and IA agents, which needs to be abel to authenticate between themselves.
* Safety: no harm should be possible to human operators directly or indirectly
* Security: secure the communication between IA agents and human operators to avoid information leaks
* Privacy: personal data shouold be always requested and only used for the task described
* Explainability: all the sequence of actiosn and decisions should be in natural language

# Protocol
The protocol should support multiple LLM (text and multimodal) stacks and should use separate channels to support efficiently the requirements.