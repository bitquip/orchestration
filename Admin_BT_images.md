## Bindings:

(Receive and Send Ports configured for a particular Orch.)

**Note:**
---
Simply put, orchestrations contain everything to receive messages from some place--can be a file being watched for changes, a service making a call, does not matter, just has to be configured to some message, it then sends messages to any other receiving port to chain actions together, then typically do something at the end to get to a desired result--like finishing the process of adding a new contact. 

Any transformations to the message coming in are configured via the BT Admin console, typically updating a batch count or something, then, sometimes based on a conditional, an outbound message is sent. Again, just like receive messages, specific send messages are simply associated to a specific port via BT Admin Console, and that port has the configuration for producing the desired result based on the outbound message. These messages can be routed however, via configuration in the Biztalk Administration Console. ![image](https://github.com/bitquip/orchestration/assets/106825912/c7231970-bccb-4312-817d-a4c2c35e204b)


## NewContactOrch
![image](https://github.com/bitquip/orchestration/assets/106825912/4f34c996-4c8d-4bb8-83d9-74b4822e7e33)

## ExistingContactOrch
![image](https://github.com/bitquip/orchestration/assets/106825912/6d9e9311-dbdb-44a5-b04d-582f422be982)

## SynchronizeOrch
![image](https://github.com/bitquip/orchestration/assets/106825912/5ee5a3e8-33e1-4ced-bc80-d9b577567aa4)

## PourProcessOrch
![image](https://github.com/bitquip/orchestration/assets/106825912/31793698-d387-45a4-a14a-478140c3535c)

## ErrorHandleOrch
![image](https://github.com/bitquip/orchestration/assets/106825912/eb7dcff6-b5b9-47da-ad61-1a9dbee6ab61)

## Receive Ports
![image](https://github.com/bitquip/orchestration/assets/106825912/6fede696-33f1-4489-811a-7eacfc0de83f)

## Send Ports
![image](https://github.com/bitquip/orchestration/assets/106825912/b345c260-95ce-4e05-8397-40b9928bed89)


