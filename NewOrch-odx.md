## NewContactOrch Service Description

1. Service Information:

   | Attribute | Value                           |
   | --------- | ------------------------------- |
   | Name      | NewContactOrch                 |
   | BPEL Exportable | false                      |
---

2. Ports:

   | Port Name             | Port Type                      | Delivery Notification         |
   | --------------------- | ------------------------------ | ----------------------------- |
   | SiebelContactsPORT    | SiebelPollPORTTYPE             | Not specified                |
   | UpdateSyncTimePORT    | NewContactSyncTimePORTTYPE     | Transmitted                  |
   | ADInsertContactPORT   | [Corresponding Port Type]      | Transmitted                  |
   | SiebelUpdateContactPORT | [Corresponding Port Type]     | Transmitted                  |
   | HashProfileInsertPORT | [Corresponding Port Type]      | Transmitted                  |
   | GetDefaultGroupsPORT  | [Corresponding Port Type]      | Transmitted                  |
   | SynchronizeOrchPORT   | [Corresponding Port Type]      | Transmitted                  |
---

3. Message Types:

   - SynchronizationRequest
   - SynchronizationResult
   - System.Exception (as ErrorMessageVar)
---

4. Scope: synchronized
   - Main logic
     
5. Actions and Logic:

<details>
  <summary>
     Enumerated steps:
     
  </summary>
  
  | Step | Action                                         |
   | ---- | ---------------------------------------------- |
   | 1    | Receive `NewContactMSG` from `SiebelContactsPORT.GetSiebelContacts` |
   | 2    | Create exception variable `ErrorMessageVar` of type `System.Exception` |
   | 3    | Conditional logic based on `NewContactMSG` content |
   | 4    | If `BatchUpdateTime` property of `NewContactMSG.Header` is empty or null |
   | 5    | Send `NewContactMSG` to `ADInsertContactPORT` to insert the contact |
   | 6    | Receive response in `ADInsertContactResponseMSG` |
   | 7    | Based on `Success` property in `ADInsertContactResponseMSG` |
   | 8    | If successful, construct `SiebelUpdateMSG` by transforming data from `NewContactMSG` and `ADInsertContactResponseMSG` |
   | 9    | Send `SiebelUpdateMSG` to `HashProfileInsertPORT` for profile insertion |
   | 10   | Receive response in `InsertUpdateProfileResponseMSG` |
   | 11   | Based on `Success` property in `InsertUpdateProfileResponseMSG` |
   | 12   | If successful, send `SiebelUpdateMSG` to `SiebelUpdateContactPORT` for updating the contact in Siebel |
   | 13   | Receive response in `UpdateContactResponseMSG` |
   | 14   | Based on `Success` property in `UpdateContactResponseMSG` |
   | 15   | If successful, send `NewContactMSG` to `GetDefaultGroupsPORT` to query default security groups |
   | 16   | Receive response in `DefaultSecurityGroupsMSG` |
   | 17   | Construct `FinalPublishedSyncMSG` by transforming data from `NewContactMSG`, `DefaultSecurityGroupsMSG`, and `SiebelUpdateMSG` |
   | 18   | Send `FinalPublishedSyncMSG` to `SynchronizeOrchPORT` |
   | 19   | Handle exceptions within the `exceptions` block |
   | 20   | Catch `DeliveryFailureException` and log the error to the EventLog |
   | 21   | Catch `System.Exception` and process the error message using `Edu.UHC.Synchronizer.Core.ErrorHandleOrch` |
   | 22   | For unknown exceptions, suspend and log a message to the Debug window |
</details>

![image](https://github.com/hunter-hartline_vizient/Orchestration/assets/138223562/8c01c5a4-918b-4654-9ad6-384bb6bce326)


![image](https://github.com/hunter-hartline_vizient/Orchestration/assets/138223562/653573d3-c859-40bb-8f94-6220fc8ffcb0)

6. Relationships:

   | Source                 | Target                         | Relationship Description                               |
   | ---------------------- | ------------------------------ | ----------------------------------------------------- |
   | SiebelContactsPORT     | GetSiebelContacts              | Receives `NewContactMSG` message                      |
   | ADInsertContactPORT    | ADInsertContact                | Sends `NewContactMSG` to insert the contact           |
   | HashProfileInsertPORT  | HashProfileInsert              | Sends `SiebelUpdateMSG` for profile insertion         |
   | SiebelUpdateContactPORT| SiebelUpdateContact            | Sends `SiebelUpdateMSG` to update the contact in Siebel|
   | GetDefaultGroupsPORT   | GetDefaultGroups               | Sends `NewContactMSG` to query default security groups|
   | SynchronizeOrchPORT    | SynchronizeOrch                | Sends `FinalPublishedSyncMSG` for synchronization     |
   | `exceptions` block     | EventLog                       | Logs `DeliveryFailureException` error                 |
   | `exceptions` block     | Edu.UHC.Synchronizer.Core.ErrorHandleOrch | Handles `System.Exception` error           |
