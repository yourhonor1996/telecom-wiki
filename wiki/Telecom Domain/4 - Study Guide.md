# SS7-over-IP Networks Using SIGTRAN: Comprehensive Study Guide

## Quiz: Short-Answer Questions

1. What are the three main types of network elements in a traditional SS7 network architecture, and what is the primary function of each?
2. Explain the concept of "out-of-band signaling" in SS7 and its advantages over "in-band signaling."
3. Describe the primary purpose of the SIGTRAN protocol suite in the context of SS7-over-IP networks.
4. What is SCTP, and what are its key advantages over TCP in a signaling context?
5. Differentiate between M2PA and M3UA protocols in terms of their primary application and key functionality within a SIGTRAN network.
6. How does the Tekelec EAGLE 5 ISS function as a Signaling Gateway, and what are its key benefits in an SS7-over-IP solution?
7. Explain the concept of "Transaction Units (TU)" and "Transactions per Second (TPS)" in the context of IP signaling capacity, and why Tekelec introduced the TU model.
8. What is the significance of "multihoming" in SCTP associations, particularly for M3UA and SUA connections?
9. Describe the two main congestion management options for the IPGWx application. What are the advantages and disadvantages of the "Match MTP3 Congestion Procedures" option?
10. What are the general recommendations for LAN/WAN considerations when dedicating the environment to SS7-over-IP traffic?

## Answer Key: Short-Answer Questions

1. The three main types of network elements in a traditional SS7 network are Signaling Switching Points (SSPs), Signal Transfer Points (STPs), and Signaling Control Points (SCPs). SSPs originate or terminate signaling messages, like central office switches. STPs route signaling messages based on their destination point code. SCPs provide a central location for data storage, enabling advanced services and features.
2. Out-of-band signaling means that signaling information does not travel over the same path as the voice or data traffic it supports. Its advantages include faster data transport, the ability to signal at any point during a call, and enabling signaling to network elements without a direct trunk connection. Traditional in-band signaling sends signals over the same channel as the conversation.
3. The SIGTRAN protocol suite serves as an enabler for SS7-over-IP networks by providing a reliable and efficient means to transport SS7 signaling messages over an IP network. It allows traditional SS7 networks to integrate with IP-enabled or all-IP devices, addressing limitations of traditional SS7 such as bandwidth and scalability.
4. SCTP (Stream Control Transmission Protocol) is a new reliable transport protocol that operates on top of a connectionless packet network like IP, at the same layer as TCP. It offers acknowledged, error-free, non-duplicated user data transport, and provides transport-related security features like resistance against blind denial-of-service attacks and masquerades, addressing deficiencies in TCP for signaling applications.
5. M2PA (MTP2 User Peer-to-Peer Adaptation Layer) is primarily used to replace B-, C-, and D-links, connecting circuit-switched nodes to IP networks while ensuring in-sequence message delivery for time-sensitive call data. M3UA (MTP3 User Adaptation Layer) seamlessly transports SS7 MTP3 user part messages (like ISUP and SCCP) over IP using SCTP, typically used for A-links, and allows IP endpoints to be addressed by unique SS7 point codes without traditional linkset restrictions.
6. The Tekelec EAGLE 5 ISS functions as a Signaling Gateway by centralizing signaling routing and bridging legacy circuit-switched (TDM) and packet networks. Its key benefits include decreased network congestion through dynamic bandwidth sharing, reduced transport costs by replacing SS7 links with IP connectivity (40% to 70% savings), and enabling a smooth migration to next-generation packet-based architectures without requiring expensive equipment replacement or reconfiguration.
7. A "transaction" in SS7 signaling is typically one MSU transmitted and one MSU received, assuming simultaneous worst-case traffic. A "transaction unit (TU)" indicates the relative cost of an IP signaling transaction, with a base of 1.0, allowing for varying costs based on feature sets. "Transactions per Second (TPS)" are calculated using the total TU value and the Advertised Card capacity. Tekelec introduced the TU model to offer customers flexibility in using application or card capacity, rather than penalizing them with worst-case scenario engineering.
8. Multihoming in SCTP associations provides network-level resilience by offering information on alternate paths to a signaling end point for a single association. For M3UA and SUA connections, multihoming is crucial because it is the only mechanism for achieving lossless handover in the event of a path failure, ensuring continuous communication even if one network path is interrupted.
9. The two main IPGWx congestion management options are discarding new messages (matching MTP3 congestion) or failing a connection. The "Match MTP3 Congestion Procedures" option's advantages include simplicity, prevention of missequencing, and notifying the originator of discarded MSUs. The primary disadvantage is that some MSUs may be discarded that could have otherwise been transmitted, similar to link congestion.
10. General recommendations for LAN/WAN in SS7-over-IP include keeping the number of nodes per LAN subnet as low as possible to optimize performance and reduce contention. It's crucial to dedicate sufficient bandwidth specifically to IP Signaling traffic and severely limit the number of non-SS7-over-IP nodes to ensure predictable network behavior. Additionally, planning for and allocating LAN capacity to handle worst-case scenarios is essential for network stability.

## Essay Format Questions

1. Compare and contrast the limitations of traditional SS7 networks with the advantages offered by SS7-over-IP (SIGTRAN) networks, particularly focusing on scalability, bandwidth, and cost-effectiveness.
2. Elaborate on the roles and interactions of the core SIGTRAN protocols (SCTP, M2PA, M3UA, SUA) within an SS7-over-IP network. Discuss how each protocol contributes to the overall functionality and reliability of the signaling transport.
3. Discuss the critical aspects of network "dimensioning" for an SS7-over-IP network. Include details on how bandwidth, throughput, transaction units, and TPS are considered, and explain the factors affecting Advertised Capacity and how to achieve it.
4. Analyze the different redundancy and link engineering strategies employed in SS7-over-IP networks, specifically comparing and contrasting unihoming and multihoming. Explain the benefits and considerations of each approach for ensuring high availability.
5. Describe the comprehensive transition planning guidelines for migrating to an SS7-over-IP network, from high-level network design to implementation and testing. What key information needs to be collected and analyzed throughout this process?

## Glossary of Key Terms

- **A-Link:** An SS7 signaling link type that interconnects a Signaling Transfer Point (STP) and a Signaling End Point (SSP or SCP), used for access to the signaling network.
- **Advertised Capacity:** The maximum TPS (Transactions per Second) that an IP signaling card or application can sustain without experiencing congestion. It represents the theoretical maximum bandwidth.
- **Application Server Process (ASP):** A process instance of an Application Server, serving as an active or standby process (e.g., MGCs, IP SCPs, IP HLRs). It contains an SCTP endpoint and processes signaling traffic.
- **Association (SCTP):** A connection established between two SCTP endpoints for the transmission of user messages, providing reliable, acknowledged, and error-free data transport.
- **Bandwidth:** The maximum amount of data that can theoretically pass through a network at any given time.
- **C-Link:** An SS7 signaling link type that interconnects mated Signal Transfer Points (STPs), used primarily for enhancing network reliability in case of link unavailability.
- **CDMA (Code Division Multiple Access):** A digital cellular technology that allows multiple users to share the same frequency band simultaneously by coding each user's information with a unique code.
- **Congestion Window Minimum (CWMIN):** An SCTP parameter that sets the minimum size of the congestion window, determining the number of packets that can be sent without receiving corresponding acknowledgment packets.
- **D-Link:** An SS7 signaling link type that interconnects mated pairs of STPs at different hierarchical levels, used for carrying signaling messages between networks.
- **Destination Point Code (DPC):** The point code of the signaling point to which an MSU (Message Signaling Unit) is routed in the SS7 network.
- **Dimensioning:** The process of evaluating network needs and usage patterns to maximize network efficiency, especially important for SS7-over-IP networks to ensure scalability, capacity, redundancy, and proper traffic management.
- **E-Link (Extended Link):** An optional SS7 signaling link type that provides backup connectivity from an SSP to a second STP pair, enhancing reliability if the primary STPs are unreachable via A-links.
- **EAGLE 5 ISS (Integrated Signaling System):** A Tekelec (now Oracle) product that serves as a robust SS7-over-IP solution, acting as a Signaling Gateway to bridge legacy circuit-switched and packet networks.
- **Erlang:** A unit of telecommunications traffic measurement, often used to express the utilization of a link or resource (e.g., 0.4 erlang means 40% utilization).
- **ESME (External Short Message Entity):** An entity that connects to a Message Centre (MC) to send and receive short messages using the SMPP protocol. Examples include WAP Proxy Servers or E-Mail Gateways.
- **F-Link (Fully Associated Link):** An SS7 signaling link type that directly connects two signaling end points, bypassing STPs. Generally not deployed between networks due to security concerns.
- **Global Title Translation (GTT):** A capability of SCCP that allows incremental routing by enabling a STP to examine a portion of a message and determine its routing, freeing originating signaling points from needing to know every potential destination.
- **GSM (Global System for Mobile Communications):** A digital mobile telephony system, and one of the initial 2G technologies based on TDMA, providing mobile voice services.
- **HLR (Home Location Register):** A database in a mobile network that contains subscriber information and details about their services, often accessed via SCCP.
- **IMF (Integrated Message Feeder):** A component of Tekelec's Integrated Application Solution (IAS) that provides IP traffic monitoring and network management tools.
- **IPGWx Application:** A Tekelec (now Oracle) application that implements M3UA and SUA protocols, primarily used for A-links, providing user part support (SCCP, ISUP) over IP to network elements like SSPs, MSCs, and HLRs.
- **IPLIMx Application:** A Tekelec (now Oracle) application that implements the M2PA protocol, primarily used for B-, C-, and D-links, connecting SS7 and IP networks.
- **IPSP (IP Server Process):** An IP-based signaling end point.
- **ISUP (ISDN User Part):** An SS7 user part protocol that defines messages and procedures for establishing and tearing down voice and data calls over the Public Switched Network (PSN), and managing the trunk network.
- **Jitter:** The variation in packet delay or Round Trip Time (RTT) in a network, which can impact real-time communication performance.
- **LAN (Local Area Network):** A network controlled and configured by a company for local distribution of packets, typically within a limited geographical area.
- **Linkset:** A group of SS7 signaling links connecting two adjacent signaling points.
- **M2PA (MTP2 User Peer-to-Peer Adaptation Layer):** A SIGTRAN protocol (RFC 4165) that facilitates the integration of SS7 and IP networks by allowing nodes to access IP-based databases and other IP nodes using SS7 signaling, primarily replacing B-, C-, and D-links.
- **M3UA (MTP3 User Adaptation Layer):** A SIGTRAN protocol (RFC 4666) that seamlessly transports SS7 MTP3 user part signaling messages (e.g., ISUP, SCCP) over IP using SCTP, typically used for A-links.
- **Message Centre (MC):** A generic term in SMPP referring to systems like SMSCs, GSM USSD Servers, or Cell Broadcast Centres.
- **Message Signal Unit (MSU):** The basic SS7 message containing the signaling information (e.g., routing label, service information octet) transmitted between signaling points.
- **Mobile Switching Center (MSC):** A central component in a mobile network that performs call switching functions for mobile subscribers.
- **MSU/s (Message Signal Units per second):** A measure of message throughput in an SS7 or SIGTRAN network.
- **MTP (Message Transfer Part):** The lower layers (Levels 1-3) of the SS7 protocol stack, responsible for reliable and efficient transfer of signaling messages between signaling points.
- **Multihoming:** An SCTP feature where an association is assigned with multiple IP addresses, providing network-level resilience by offering alternate paths to a signaling end point.
- **Originating Point Code (OPC):** The point code of the signaling point that originated a particular MSU.
- **PDU (Protocol Data Unit):** A unit of data at any layer of a network protocol stack. In SMPP, operations are typically request and response PDUs.
- **PSTN (Public Switched Telephone Network):** The traditional circuit-switched telephone network.
- **QoS (Quality of Service):** A set of parameters (bandwidth, delay, packet loss, jitter) defining the performance characteristics of a network connection.
- **Reliable Transport:** A transport mechanism that ensures acknowledged, error-free, and non-duplicated delivery of user data.
- **Retransmission Mode (Linear/RFC):** Methods for managing retransmissions of packets in SCTP. Linear mode (LIN) uses a constant RTO, while RFC mode uses a dynamic, moving average RTO.
- **Routing Key:** In M3UA, a set of SS7 parameters (e.g., DPC, OPC, SI, SSN) that uniquely defines a range of signaling traffic configured to be handled by a particular Application Server.
- **Routing Label:** The portion of an MSU that identifies the message originator (OPC), intended destination (DPC), and a Signaling Link Selection (SLS) field for traffic distribution.
- **RTT (Round Trip Time):** The time it takes for a signal to travel from a sender to a receiver and back again.
- **Scalability:** The ability of a system to handle a growing amount of work by adding resources. In SS7-over-IP, it refers to increasing capacity as needed.
- **SCCP (Signaling Connection Control Part):** An SS7 user part that provides connectionless and connection-oriented network services, including global title translation and routing to subsystems.
- **SCP (Signaling Control Point):** An SS7 network element that provides a central database for services and features (e.g., 800 number translation, calling card verification).
- **SCTP (Stream Control Transmission Protocol):** A reliable transport protocol (RFC 2960) that operates on top of IP, designed for signaling applications, offering advantages like multihoming and multi-streaming.
- **SEP (Signaling End Point):** A general term for a node in an SS7 network that originates or terminates signaling messages (e.g., SSP, SCP).
- **Signaling Gateway (SG):** A network element that interworks signaling between TDM (SS7) and IP networks, often performing translation and relay of messages.
- **Signaling Link Selection (SLS):** A value within the MSU's routing label used to identify the linkset and specific link over which a message should be transported, aiding in load sharing.
- **Signal Unit (SU):** The basic unit of information exchanged over an SS7 signaling link. Types include Message Signal Unit (MSU), Link Status Signal Unit (LSSU), and Fill In Signal Unit (FISU).
- **SMPP (Short Message Peer-to-Peer Protocol):** An open, industry-standard protocol (managed by the SMS Forum) for transferring short message data between External Short Message Entities (ESMEs), Routing Entities (REs), and Message Centres (MCs).
- **SMSC (Short Message Service Center):** A Message Centre that stores and forwards short messages in a mobile network.
- **SSP (Signaling Switching Point):** An SS7 network element, typically a central office switch, that originates or terminates calls and SS7 messages.
- **SS7 (Signaling System 7):** An architecture for out-of-band signaling in telecommunications networks, supporting call establishment, billing, routing, and information exchange in the PSTN.
- **STP (Signal Transfer Point):** A packet switch in an SS7 network that routes signaling messages based on destination point codes.
- **SUA (SCCP User Adaptation Layer):** A SIGTRAN protocol (RFC 3868) that transports SS7 SCCP signaling messages over IP using SCTP, typically used when both source and destination are IP-based.
- **TCAP (Transaction Capabilities Application Part):** An SS7 user part that defines messages and protocols for communication between applications (subsystems) in network nodes, used for database services (e.g., 800 number queries, calling card).
- **TDM (Time Division Multiplexing):** A technology that transmits multiple signals simultaneously over a single transmission path by dividing the signal into time slots.
- **Throughput:** The actual amount of data transmitted successfully over a network link in a given time, always lower than bandwidth due to various network conditions.
- **Timer T7 (Excess Delay in ACK):** An M2PA timer configured to account for expected network latency, important for ensuring timely retransmissions.
- **TLVs (Tagged Length Values):** A special composite field in SMPP PDUs comprising a Tag (identifying the parameter), Length (indicating value field length), and Value (actual data). Used for optional parameters and extensibility.
- **TPS (Transactions per Second / Transaction Units per Second):** A measure of how quickly a network can transmit and receive data, often reflecting the maximum of receive and transmit rates in worst-case scenarios.
- **Transaction Unit (TU):** A relative cost indicator for an IP signaling transaction, allowing for different transaction complexities to be factored into capacity calculations.
- **UDHI (User Data Header Indicator):** A flag in SMPP's esm_class field, indicating that GSM User Data Header information is encoded in the short message user data.
- **Unihoming:** A redundancy method where one signaling link per linkset is hosted by IPLIMx cards, assigned to a single-homed SCTP association over one network path. Less resilient than multihoming for path failures.
- **USSD (Unstructured Supplementary Services Data):** A GSM service that allows real-time interactive communication between a mobile phone and an application, often used for menu-based services.
- **WAN (Wide Area Network):** A network controlled and configured by a common carrier service for long-distance delivery of packets.