**Module: Edu.UHC.Synchronizer.Core**

| PortType                    | Type         | Description                                       |
|---------------------------- |--------------|---------------------------------------------------|
| SiebelContactsPORTTYPE      | oneway       | GetSiebelContacts { SynchronizationRequest }      |
| GetExistingContactsPORTTYPE | requestresponse | GetExistingContacts { SynchronizationPollTick, SynchronizationRequest } |
| STUBSENDPORTTYPE            | oneway       | Operation_1 { SynchronizationRequest }            |
| HashPorfileUpdatePORTTYPE   | requestresponse | UpdateHash { SynchronizationRequest, SynchronizationResult } |
| LastSyncUpdatePORTTYPE      | requestresponse | UpdateSyncTime { SynchronizationRequest, SynchronizationResult } |
| CallSynchronizeOrchPORTTYPE | oneway       | CallSynchronizeOrch { SynchronizationRequest }   |

**Service: ExistingContactOrch**

| Port                       | Type             | Description                                           |
|--------------------------- |------------------|-------------------------------------------------------|
| uses HashPorfileUpdatePORTTYPE | LogicalBinding  | Transmitted DeliveryNotification                     |
| uses LastSyncUpdatePORTTYPE    | LogicalBinding  | Transmitted DeliveryNotification                     |
| implements SiebelContactsPORTTYPE | LogicalBinding  |                                                       |
| uses CallSynchronizeOrchPORTTYPE | LogicalBinding  | Transmitted DeliveryNotification                     |

**Messages:**

- ExistingContactsMSG (SynchronizationRequest)
- ProfileHashUpdateResponseMSG (SynchronizationResult)
- LastSyncUpdateResponseMSG (SynchronizationResult)
- PublishedMSG (SynchronizationRequest)

**Variables:**

- ErrorMessageVar (System.Exception)

**Service Body:**

Defines a service called `ExistingContactOrch`. It contains a main scope named `synchronized`, which handles the processing logic. The service has several ports, each serving different purposes.


![image](https://github.com/bitquip/orchestration/assets/106825912/1e07dfcc-d5df-4db0-a454-0f843e5fa0f4)


1. **Activation and Receiving**: The service begins with the `activate receive` statement, which listens for incoming messages on the `SiebelContactsPORT.GetSiebelContacts` port. The received message is stored in the `ExistingContactsMSG` variable. An `ErrorMessageVar` of type `System.Exception` is initialized to handle exceptions.

2. **Processing Based on Batch Update Time**: The code checks if the `BatchUpdateTime` in the received message is empty or not. If empty, it implies that there are new contacts to process.

3. **Hash Profile Update**: If there are new contacts, the service sends the `ExistingContactsMSG` to the `HashPorfileUpdatePORT` using the `send` statement. It then waits for the response in the `ProfileHashUpdateResponseMSG` variable using the `receive` statement.

4. **Conditional Actions on Hash Profile Update Response**: Depending on the `Success` value in the `ProfileHashUpdateResponseMSG`, the service performs different actions. If `Success` is greater than 0, indicating either an update or insertion, the service constructs a new `PublishedMSG` by transforming the `ExistingContactsMSG` using the `Edu.UHC.Synchronizer.Core.ExistingContactPublishedTFORM` transformation. The constructed message is then sent to the `SynchronizeOrchPORT` using the `send` statement.

5. **Handling Errors in Hash Profile Update**: If the `Success` value in `ProfileHashUpdateResponseMSG` is not greater than 0, it means there was an error. The service throws a custom exception of type `SyncException` with information about the error.

6. **Handling Non-Empty Batch Update Time**: If the `BatchUpdateTime` in the received message is not empty, it indicates that there are no new contacts to process. The service sends the `ExistingContactsMSG` to the `UpdateSyncTimePORT` using the `send` statement. It then waits for the response in the `LastSyncUpdateResponseMSG` variable using the `receive` statement.

7. **Handling Errors in Last Sync Update**: If there was an error in the response from `UpdateSyncTimePORT`, the service throws a custom exception of type `SyncException`.

8. **Exception Handling**: The service has an `exceptions` section that handles different types of exceptions. It catches `DeliveryFailureException`, `System.Exception`, and generic exceptions. If a `DeliveryFailureException` is caught, it writes an entry to the event log. If a `System.Exception` is caught, it stores the exception in the `ErrorMessageVar` and then executes the `Edu.UHC.Synchronizer.Core.ErrorHandleOrch` orchestration. In both cases, it terminates the service instance with an appropriate error message. If a generic exception is caught, it suspends the service with a message.

