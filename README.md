# Authentication and Authorization

A2A models agents as enterprise applications (and can do so because A2A agents are opaque and do not share tools and resources). This quickly brings enterprise-readiness to agentic interop.

A2A follows OpenAPI’s Authentication specification for authentication. Importantly, A2A agents do not exchange identity information within the A2A protocol. Instead, they obtain materials (such as tokens) out of band and transmit materials in HTTP headers and not in A2A payloads.

While A2A does not transmit identity in-band, servers do send authentication requirements in A2A payloads. At minimum, servers are expected to publish their requirements in their Agent Card. Thoughts about discovering agent cards are in this topic.

Clients should use one of the servers published authentication protocols to authenticate their identity and obtain credential material. A2A servers should authenticate every request and reject or challenge requests with standard HTTP response codes (401, 403), and authentication-protocol-specific headers and bodies (such as a HTTP 401 response with a WWW-Authenticate header indicating the required authentication schema, or OIDC discovery document at a well-known path). More details discussed in Enterprise Ready.

    Note: If an agent requires that the client/user provide additional credentials during execution of a task (for example, to use a specific tool), the agent should return a task status of Input-Required with the payload being an Authentication structure. The client should, again, obtain credential material out of band to A2A.


Source: https://google.github.io/A2A/#/documentation?id=actors

# Example use case stories

## Booking a holiday

A user Jack wants to book a holiday with a travel agency.

Let's suppose there are the following actors:
* The User: Jack
* Client: A UI software installed on the user workstation called MyAI
* Personal Assistant: a personal assistant agent card on personalassistant1.org
* Web Search: Agent Card on websearch1.org
* Travel Agencies: various agents card on travelagency1.org, travelagency2.org, travelagency3.org
* Booking Agent: another agent card on booking1.org
* Payment Agent: an agent card on mybank1.com

### Step 1
Assumptions: the client authenticates with personalassistant1.org via OAuth.
The user asks the Personal Assistant to book a holiday in a specific place, for example, "I want to go to Paris next week". The Personal Assistant will create a task with the following information:
* Task ID: 123456
* Task Type: Book a holiday
* Task Status: Input-Required
* Task Payload: {
    "destination": "Paris",
    "date": "next week",
    "user": "Jack",
    "client": "MyAI"
}
### Step 2
Assumptions: The Personal Assistant will authenticate with the Web Search agent via OAuth2.
The Personal Assistant will send the task to the Web Search agent. The Web Search agent will search for the best travel agencies and return the result to the Personal Assistant. The Personal Assistant will update the task status to "Completed" and add the travel agency information to the task payload.
The Personal Assistant will then proceed to contact all the travel agencies agent cards to get the best price for the trip. The Personal Assistant will send the task to the Travel Agency agent. The Travel Agency agent will search for the best price and return the result to the Personal Assistant. The Personal Assistant will update the task status to "Completed" and add the travel agency information to the task payload.

The Personal Assistant will send a task completed summary to the Client to list all the possible offerings from the travel agencies. The Client will display the information to the user.

The User is interested in for example 3 different offers but to get an accurate quote he/she needs to provide more information.

Key Decision here: Each of the Travel Agency agent will ask for PII information related to the person that can be for example full name, age, email address and name of the other passengers. 

At this stage the user needs to be notified for permission and we need to ask for the PII information which could also be stored in the Personal Assistant private vault.

    There is no concept of a user or client identifier in A2A schemas. Instead, A2A conveys authentication requirements (scheme and materials) through the protocol. The client is responsible for negotiating with the appropriate authentication authority (out of band to A2A) and retrieving/storing credential materials (such as OAuth tokens). Those credential materials will be transmitted in HTTP headers and not in any A2A payload.

    Different authentication protocols and service providers have different requirements and individual requests may require multiple identifiers and identities in scheme specific headers. It is recommended that clients always present a client identity (representing the client agent) and user identity (representing their user) in requests to A2A servers.

    Note: Multi-identity federation is an open topic for discussion. For example, User U is working with Agent A requiring A-system's identifier. If Agent A then depends on Tool B or Agent B which requires B-system identifier, the user may need to provide identities for both A-system and B-system in a single request. (Assume A-system is an enterprise LDAP identity and B-system is a SaaS-provider identity).

    It is currently recommended that if Agent A requires the user/client to provide an alternate identity for part of a task, it sends an update in the INPUT-REQUIRED state with the specific authentication requirements (in the same structure as an AgentCard's authentication requirements) to the client. The client then, out of band to A2A and also out of band to A-system, negotiates with B-system's authority to obtain credential material. Those materials (such as tokens) can be passed through Agent A to Agent B. Agent B will exchange when speaking to its upstream systems.

    A2A servers are expected to authorize requests based on both the user and application identity. We recommend that individual agents manage access on at least two axes:

    Skills
    Agents are expected to advertise (via an Agent Card) their capabilities in the form of skills. It is recommended that agents authorize on a per-skill basis (for example, OAuthScope 'foo-read-only' could limit access only to 'generateRecipe' skills).

    Tools (Actions and Data)
    It is recommended that Agents restrict access to sensitive data or actions by placing them behind Tools. When an agentic flow or model needs to access this data, the agent should authorize access to a tool based on the application+user priviledge. We highly recommend utilizing API Management with Tool access. 

Question: how can we make sure to the user that the request comes from the travel agency and not from a malicious agent?

Ideas:

1. The agent card could be digitally signed by the public key of the travel agency.
2. The client should show a temporal sequence of all the authentication steps performed via OAtuh2, OpenID Connected, API keys etc etc
3. The client should know all the protocols involved, were they all TLS secured? All the certified were valid? Any mTLS capabilities between the agents?
4. How to prevent possible issue with passing PII data between agents for example in a complex chain of interactions one agent card may need PII data and go back to its chain to of other say N agents to get the PII data from the user or users (let's tak about this later!), the other agents may just relay the PII data without asking the user which violates most GDPR law (un-necessarsy sharing of PII data).

# Open questions

Drawing from traditional application development, multi agent with distributed architecture will face a number of issues which I briefly describe below:

## Deadlock

Traditional Context:
In multi-threaded or multi-process systems, deadlock occurs when two or more processes each hold a resource and simultaneously wait for the other resources held by the other processes. Because none of the processes relinquish their resources, every process stays stuck indefinitely.

Multi-Agent Scenario:
Imagine two personal assistants interacting to complete tasks that span multiple services. For example, consider:

    Agent A is responsible for booking a flight. It first locks the flight inventory system for a preferred seat.

    Agent B is in charge of arranging travel insurance and needs the same flight information from a different system before proceeding.

    At the same time, Agent A also needs to verify payment details, which Agent B already locked for insurance payment processing.

If each agent locks a resource (e.g., flight inventory, payment gateway) and waits for the other resource to become available, neither can proceed—this is analogous to a deadlock situation. Both agents are stuck in a waiting state, and the user might find that their travel booking never completes.

## Race Conditions

Traditional Context:
A race condition happens when the behavior of software depends on the sequence or timing of uncontrollable events, such as the order in which processes execute. Without proper synchronization, data integrity can be compromised.

Multi-Agent Scenario:
Consider a situation where two different assistants are trying to update the same travel itinerary in a shared cloud-based calendar:

    Agent A automatically schedules a meeting related to the trip.

    Agent B concurrently attempts to change the booking details because of a last-minute flight reschedule.

If both agents try to modify the schedule simultaneously without an effective locking or version-control mechanism, the calendar might reflect only one change or, worse, merge changes in an inconsistent way (for example, a meeting might be scheduled based on an outdated flight time). This conflicting update is a manifestation of a race condition.

## Livelock

Traditional Context:
Livelock is similar to deadlock except that the states of the processes involved in the livelock constantly change with no effective progress. They essentially keep responding to each other’s actions to avoid deadlock but never complete their tasks.

Multi-Agent Scenario:
Envision two assistants negotiating access to a shared scheduling service:

    Agent M detects that the schedule is busy and decides to postpone its update, then notifies the system.

    Agent N, upon noticing Agent M’s waiting state, also defers its action to avoid collision.

Both agents might keep deferring their actions in an attempt to resolve the conflict. The system remains active (with both agents continuously making adjustments), but the actual booking or update never gets finalized—resulting in a livelock. This scenario can delay time-critical appointments or lead to inefficiencies in daily operations.

## Practical Examples in a Multi-Agent Ecosystem

###    Travel and Accommodation Coordination:
    Two assistants working for the same user might attempt to secure hotel reservations and flight bookings concurrently. Without properly managed synchronization, you could encounter:

        Deadlock: Both agents lock different providers (one locks the flight booking API, the other locks the hotel API) and wait for cross-confirmation, stalling the entire process.

        Race Conditions: If both agents try to update the itinerary simultaneously (e.g., one adding flight details while the other updates hotel reservations), inconsistent or overlapping information can appear.

###    Finance and Payment Processing:
    Multiple assistants might interact with the same digital wallet or banking service:

        Starvation: An assistant tasked with routine account balance checks might never get the chance if an urgent payment authorization system constantly preempts it.

        Livelock: Two payment agents might end up in an oscillating state—each attempting to reverse a payment hold on behalf of different transactions, causing the account to flicker between provisional states without any transaction completing.

###    Healthcare Appointment Scheduling:
    For a user's medical needs:

        Deadlock: One assistant handling the booking for a specialist appointment locks the doctor’s scheduler, while another handles a conflicting check for an available slot with a general practitioner. Both may wait indefinitely for the other system to release the lock.

        Race Conditions: If two assistants attempt to update the user’s availability at the same time, the final appointment time might become inconsistent or double-booked.