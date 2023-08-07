---
marp: false
---

## Port Types:

**A port type defines the communication capabilities and the structure of messages that can be exchanged between BizTalk and external systems. It acts as an interface or contract for communication. A port type is defined using Web Services Description Language (WSDL) and is based on the Web Services Description Language (WSDL) standard. It specifies the operations (methods) that can be performed and the types of messages that can be sent or received.**

## Ports:

**A port is an instance of a port type, representing a specific endpoint for communication within the BizTalk environment. It defines how BizTalk communicates with external systems or applications. Ports are associated with adapters, which are responsible for handling the actual communication protocols like HTTP, SOAP, FILE, etc. BizTalk supports various adapters to interact with different systems.**

***There are two main types of ports in BizTalk:***
#### a. Receive Ports: 

**A receive port is used to receive messages from external systems. It listens for incoming messages and routes them to BizTalk for processing. These incoming messages are then made available to orchestrations or send ports for further processing.**

#### b. Send Ports:

**A send port is used to send messages from BizTalk to external systems. It takes messages from orchestrations or receive ports and sends them out using the configured adapter to the specified external endpoint.**

## Messages:

**Messages are the data packets exchanged between different systems in BizTalk. They carry the information needed for integration between applications. Messages can be of different types: XML messages, flat files, EDI (Electronic Data Interchange) messages, etc. BizTalk allows you to transform, validate, and manipulate these messages during their journey from the source system to the destination system.**

## Schemas:

**In BizTalk, schemas define the structure and format of the messages that are exchanged between systems. They act as a blueprint for interpreting the message contents. Schemas define the rules for data validation, data transformation, and message serialization/deserialization. When a message is received or sent, BizTalk uses the appropriate schema to validate and process the message.**

---