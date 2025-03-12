# RFC DRAFT: Multi-Agent Communication Protocol for Intelligent Assistants

**RFC Title**: Multi-Agent Communication Protocol (MACP)  
**Status**: DRAFT  
**Date**: 2025-03-12  
**Authors**: robomotic  
**Version**: 0.1  

## Abstract

This RFC proposes a communication protocol for enabling secure, efficient, and transparent interactions between Intelligent Assistants (IAs) from different vendors. The protocol aims to establish a standardized framework for multi-agent cooperation in performing complex tasks on behalf of human operators, while adhering to strict requirements for authorization, authentication, safety, security, privacy, and explainability.

## 1. Introduction

### 1.1 Background

As artificial intelligence advances, major vendors are developing increasingly capable Intelligent Assistants that can perform complex tasks. In the near future, these systems will need to cooperate across vendor boundaries to provide seamless services for users. Examples include coordinating healthcare services, transportation, lodging, food delivery, and other daily activities.

The lack of a standardized protocol for secure inter-agent communication presents a significant barrier to this vision. This RFC proposes a Multi-Agent Communication Protocol (MACP) to address this gap.

### 1.2 Scope

This protocol covers:
- Communication between Intelligent Assistants from different vendors
- Authorization and authentication mechanisms for agents and humans
- Safety and security measures for protecting human interests
- Privacy controls for personal data
- Explainability requirements for agent actions and decisions

## 2. Use Cases

### 2.1 Healthcare Coordination (Primary Example)

A healthcare scenario demonstrates the need for multi-agent coordination:
1. A patient contacts a Healthcare IA about a health issue
2. Healthcare IA retrieves relevant medical history from Medical Records IA
3. Healthcare IA identifies specialists and coordinates with Calendar IA
4. Insurance IA verifies coverage and approves appointments
5. Post-treatment, Billing IA handles payments and insurance claims
6. Pharmacy IA coordinates medication delivery
7. Monitoring IA tracks recovery via biomedical sensors
8. Follow-up appointments are scheduled as needed

This scenario involves 5-8 different IAs, each requiring specific authorization levels and access to different information domains.

### 2.2 Other Potential Use Cases

- Travel planning and coordination
- Smart home management
- Financial services integration
- Educational resource coordination
- Emergency response systems

## 3. Protocol Requirements

### 3.1 Authorization

The protocol must implement a robust authorization framework that:
- Allows human operators to explicitly authorize agent actions
- Supports delegation of authority between agents with appropriate constraints
- Implements time-bound and scope-limited authorization tokens
- Provides revocation mechanisms for removing authorizations
- Maintains an auditable record of all authorization grants

### 3.2 Authentication

Authentication mechanisms must:
- Verify the identity of human operators through secure multi-factor methods
- Authenticate IA agents using cryptographic signatures or certificates
- Prevent impersonation attacks
- Support federated authentication across different vendor ecosystems
- Provide non-repudiation for critical transactions

### 3.3 Safety

Safety requirements include:
- Prevention of actions that could directly or indirectly harm humans
- Implementation of circuit-breaker mechanisms to halt harmful action chains
- Continuous monitoring for unintended consequences
- Fallback mechanisms when safety cannot be guaranteed
- Regular safety audits and certification

### 3.4 Security

Security measures must:
- Encrypt all communications between agents and humans
- Implement robust access controls for sensitive information
- Prevent unauthorized data access or exfiltration
- Detect and mitigate potential security breaches
- Follow security best practices for all implementations

### 3.5 Privacy

Privacy protections include:
- Just-in-time consent for personal data usage
- Purpose-limited data collection and processing
- Minimal data sharing between agents
- Transparent data handling policies
- Compliance with relevant privacy regulations

### 3.6 Explainability

Explainability requirements include:
- Natural language explanations for all agent actions and decisions
- Complete audit trails of multi-agent interactions
- Visualization of decision trees when appropriate
- Human-readable summaries of complex processes
- Mechanisms for questioning agent decisions

## 4. Protocol Architecture

### 4.1 Layered Architecture

The MACP employs a five-layer architecture to provide a complete communication framework:

1. **Transport Layer**
   - Underlying network protocols (HTTPS, WebSockets, gRPC)
   - TLS 1.3+ for all communications
   - Support for both synchronous and asynchronous communication patterns
   - Quality of Service (QoS) guarantees for time-critical operations

2. **Security Layer**
   - Authentication and authorization mechanisms
   - End-to-end encryption for sensitive data
   - Key exchange and rotation protocols
   - Certificate management for agent identities
   - Zero-trust architecture principles

3. **Session Layer**
   - Session establishment and termination
   - Transaction boundaries and atomicity
   - Continuity across network interruptions
   - Context preservation between interactions
   - Session timeout and idle management

4. **Message Layer**
   - Message envelope format and versioning
   - Content encoding and serialization
   - Message routing and addressing
   - Priority mechanisms for urgent messages
   - Message correlation and threading

5. **Application Layer**
   - Task-specific protocols for different domains (healthcare, finance, etc.)
   - Domain-specific ontologies and schemas
   - Intent recognition and fulfillment patterns
   - Capability discovery and negotiation
   - Error handling and recovery mechanisms

### 4.2 Communication Channels

The protocol supports logically separated channels for different types of communication:

#### 4.2.1 Control Channel
- Low-latency, high-priority messages
- Authentication and authorization flows
- Session management commands
- Error signaling and recovery coordination
- Heartbeat and health monitoring

#### 4.2.2 Data Channel
- High-bandwidth content exchange
- Support for text, structured data, and binary payloads
- Multimodal content (images, audio, video)
- Large payload handling with chunking and resumability
- Compression for efficient transfer

#### 4.2.3 Metadata Channel
- Context information exchange
- User preferences and constraints
- Environment and situational awareness data
- Agent capabilities and limitations
- Task progress and status indicators

#### 4.2.4 Audit Channel
- Complete logging of all cross-agent interactions
- Non-repudiable cryptographic receipts
- Explanation traces for decision-making
- Performance metrics and timing information
- Compliance verification data

### 4.3 Message Format

#### 4.3.1 Message Envelope

```json
{
  "header": {
    "protocol_version": "1.0.0",
    "message_id": "uuid-v4",
    "correlation_id": "uuid-v4",
    "timestamp": "ISO-8601",
    "sender": {
      "agent_id": "agent-identifier",
      "vendor": "vendor-identifier",
      "capabilities": ["capability-uri-1", "capability-uri-2"]
    },
    "recipient": {
      "agent_id": "agent-identifier",
      "vendor": "vendor-identifier"
    },
    "message_type": "request|response|notification",
    "priority": "low|normal|high|critical",
    "security": {
      "authentication_token": "jwt-or-other-token",
      "encryption_metadata": {}
    },
    "expiration": "ISO-8601"
  },
  "body": {
    "content_type": "mime-type",
    "content_encoding": "encoding-type",
    "schema_uri": "uri-to-schema-definition",
    "payload": {}
  },
  "attachments": [
    {
      "attachment_id": "identifier",
      "content_type": "mime-type",
      "size": "bytes",
      "hash": "content-hash",
      "uri": "reference-or-inline-content"
    }
  ],
  "metadata": {
    "task_id": "task-identifier",
    "session_id": "session-identifier",
    "user_context": {},
    "privacy_level": "public|restricted|confidential|sensitive",
    "retention_policy": "transient|short-term|long-term|permanent",
    "explanation": {
      "intent": "natural-language-description",
      "justification": "natural-language-explanation",
      "alternatives_considered": []
    }
  },
  "signature": "cryptographic-signature-of-message"
}
```

#### 4.3.2 Content Formats

The protocol supports multiple content formats including:

- **Structured Data**: JSON, Protocol Buffers, Avro
- **Text**: Plain text, Markdown, HTML
- **Rich Media**: Images (PNG, JPEG, SVG), Audio (MP3, WAV, FLAC), Video (MP4, WebM)
- **Domain-Specific Formats**: FHIR for healthcare, FIX for finance, etc.
- **Vector Data**: Embeddings and tensor representations

#### 4.3.3 Versioning

- Semantic versioning for the protocol (MAJOR.MINOR.PATCH)
- Backward compatibility guarantees for MINOR and PATCH versions
- Version negotiation during session establishment
- Deprecation notices and migration paths for breaking changes
- Feature flags for optional protocol extensions

### 4.4 Session Management

#### 4.4.1 Session Establishment

1. **Discovery**: Agents discover each other through directory services or direct references
2. **Capability Exchange**: Agents exchange capability manifests
3. **Authentication**: Mutual authentication through certificates or tokens
4. **Session Parameters**: Negotiation of protocol versions, encryption, timeouts
5. **Authorization Scope**: Establishment of permission boundaries

#### 4.4.2 Session Maintenance

- Periodic heartbeats to maintain session liveness
- Session refresh mechanisms for long-running interactions
- Context synchronization to ensure shared understanding
- Dynamic adjustment of communication parameters based on network conditions
- Graceful degradation when capabilities become unavailable

#### 4.4.3 Session Termination

- Normal termination with completion acknowledgment
- Emergency termination for security or safety concerns
- Resource cleanup and state persistence
- Final audit log entries
- User notification of significant outcomes

### 4.5 LLM Integration

#### 4.5.1 Abstraction Layer

The protocol provides a standard interface for LLM capabilities:

- **Prompt Template Engine**: Standardized prompt construction
- **Response Parsing**: Common formats for LLM outputs
- **Context Management**: Efficient handling of conversation history
- **Tool Invocation**: Standardized patterns for function calling
- **Reasoning Frameworks**: Support for chain-of-thought and other reasoning methods

#### 4.5.2 Multi-vendor Support

- Adapter patterns for different LLM APIs
- Capability-based routing to the appropriate model
- Fallback mechanisms when primary models are unavailable
- Load balancing across equivalent models
- Cross-vendor model composition for specialized tasks

#### 4.5.3 Multimodal Integration

- Standardized handling of mixed-media inputs and outputs
- Coordinated processing of multimodal content
- Cross-modal reasoning and reference resolution
- Synchronized presentation of multimodal responses
- Accessibility considerations for all modalities

### 4.6 Error Handling

#### 4.6.1 Error Categories

- **Protocol Errors**: Malformed messages, version mismatches
- **Security Errors**: Authentication failures, authorization violations
- **Operational Errors**: Timeouts, resource exhaustion
- **Semantic Errors**: Invalid requests, constraint violations
- **Domain-Specific Errors**: Context-specific failure modes

#### 4.6.2 Error Responses

```json
{
  "error": {
    "code": "error-code",
    "category": "protocol|security|operational|semantic|domain",
    "message": "human-readable-error",
    "details": {},
    "recovery_hints": {
      "retry_strategy": "immediate|exponential-backoff|manual",
      "alternative_approaches": [],
      "human_intervention_required": true|false
    },
    "diagnostic_info": {}
  }
}
```

#### 4.6.3 Recovery Mechanisms

- Automatic retry with exponential backoff
- Circuit breakers for persistent failures
- Degraded mode operation when full functionality is unavailable
- Escalation to human operators for critical failures
- Synchronization mechanisms for recovery from inconsistent states

### 4.7 Scale and Performance

#### 4.7.1 Scalability Features

- Support for millions of concurrent agent interactions
- Horizontal scaling of protocol components
- Sharding strategies for high-volume deployments
- Caching mechanisms for frequent data access patterns
- Rate limiting and throttling to prevent abuse

#### 4.7.2 Performance Optimizations

- Binary protocol options for high-efficiency communication
- Compression for bandwidth-constrained environments
- Batching of related messages to reduce overhead
- Predictive pre-fetching for anticipated data needs
- Differential updates to minimize data transfer

## 4.8 Model Context Protocol (MCP) Integration

The Multi-Agent Communication Protocol incorporates and extends the Model Context Protocol (MCP) developed by Anthropic to enable standardized access to data sources and tools across multiple agent systems.

#### 4.8.1 MCP Compatibility Layer

The protocol provides a dedicated compatibility layer for MCP:

- **Native MCP Support**: Agents can directly connect to MCP-compatible servers without adaptation
- **MCP Client Capabilities**: Agents can act as MCP clients to access external data sources 
- **MCP Server Capabilities**: Agents can expose their data through MCP server interfaces
- **Cross-Protocol Translation**: Automatic conversion between MACP and MCP message formats
- **Unified Authentication**: MCP authentication tokens are recognized within the MACP security framework

#### 4.8.2 MCP Resource Types

The protocol supports all MCP resource types, including:

- **Message Histories**: Conversations between humans and LLMs
- **Documents**: Text-based content with optional metadata
- **Structured Data**: JSON, tables, and other structured formats
- **Function Definitions**: Tool specifications exposable to LLMs
- **Function Execution**: Ability to invoke tools across agent boundaries
- **Vector Search**: Semantic retrieval capabilities across data sources

#### 4.8.3 MCP Protocol Flow Integration

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Agent A   │     │ Translation │     │   Agent B   │
│  (MCP Client)│◄────┤    Layer    ├────►│(MACP Native)│
└─────────────┘     └─────────────┘     └─────────────┘
       │                                        │
       ▼                                        ▼
┌─────────────┐                        ┌─────────────┐
│  External   │                        │  Internal   │
│ MCP Servers │                        │ MACP System │
└─────────────┘                        └─────────────┘
```

#### 4.8.4 Joint Schema Definition

The MACP schema incorporates and extends the MCP schema (defined in the [MCP specification](https://spec.modelcontextprotocol.io/)) to allow:

- **Schema Inheritance**: MACP types can extend MCP base types
- **Plugin Architecture**: MCP servers can be registered as MACP capabilities
- **Capability Discovery**: MCP resources are advertised in the MACP capability registry
- **Cross-Protocol References**: Resources can be referenced across protocol boundaries

#### 4.8.5 Implementation Guidelines

When implementing MACP with MCP support:

- Use the MCP SDKs (TypeScript, Python, Java, Kotlin) as building blocks
- Wrap MCP client/server implementations with MACP protocol adaptors
- Leverage MCP's standard data formats for inter-agent communication
- Extend MCP authorization models with MACP's multi-agent authorization framework
- Register MCP servers in MACP service discovery mechanisms

## 5. Security Considerations

Implementation of this protocol requires careful attention to:
- Supply chain security for all components
- Credential management and rotation
- Vulnerability management and patching
- Threat modeling for each integration point
- Independent security audits

## 6. Implementation Guidelines

### 6.1 Minimum Viable Implementation

A minimal implementation should include:
- Basic authentication mechanisms
- Simple authorization flows
- Core message formats
- Privacy consent mechanisms
- Simplified explanation capabilities
- MCP compatibility for basic data access

### 6.2 Reference Implementation

A reference implementation should be developed to:
- Validate the protocol specification
- Provide a starting point for adopters
- Demonstrate interoperability
- Establish best practices
- Show MCP integration patterns

### 6.3 MCP Integration Pathway

To incorporate MCP support into an existing system:

1. **Phase 1**: Implement MCP client capabilities to access external data sources
2. **Phase 2**: Expose internal data resources as MCP servers
3. **Phase 3**: Incorporate full MACP message envelope with MCP payload support
4. **Phase 4**: Implement cross-protocol authentication and authorization
5. **Phase 5**: Deploy the complete MACP stack with MCP compatibility

## 7. Open Questions and Issues

The following questions remain open for discussion:
- How to establish trust between agents from different vendors?
- What standardization bodies should oversee protocol development?
- How to ensure backward compatibility as the protocol evolves?
- What certification process should be established for compliance?
- How to handle situations where different jurisdictions have conflicting requirements?

## 8. Next Steps

- Establish a working group for protocol refinement
- Develop proof-of-concept implementations
- Engage with potential adopters for feedback
- Create conformance testing tools
- Build a community of practice around the protocol

## 9. References

1. Model Context Protocol Specification, Anthropic, https://spec.modelcontextprotocol.io/
2. Model Context Protocol GitHub Repository, https://github.com/modelcontextprotocol
3. Anthropic Documentation on MCP, https://docs.anthropic.com/en/docs/agents-and-tools/mcp

## 10. Appendices

### Appendix A: Glossary

- **Intelligent Assistant (IA)**: An AI system capable of performing complex tasks on behalf of human operators
- **Multi-Agent System**: A collection of IAs working together to achieve common goals
- **Authorization**: Permission granted to perform specific actions
- **Authentication**: Verification of identity
- **Explainability**: The ability to provide human-understandable explanations for actions and decisions
