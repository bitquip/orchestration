## Summary
---

**Service: SynchronizeOrch**

The service "SynchronizeOrch" orchestrates the synchronization process. It receives a message of type "SynchronizationRequest" from the "GetSynchronizationRequestPORTTYPE" port. The main processing logic is as follows:

1. Activation and Preparation:
   - The service sets a boolean variable "CheckHCO" to true and initializes several helper variables.
   - It transforms the received "RCVSynchronizationRequestMSG" to construct a "BatchResponseSummaryMSG" for further processing.

2. Synchronization Logic and Exception Handling:
   - The service checks if the "RCVSynchronizationRequestMSG" contains a non-empty "ADUserGUID." If true, it proceeds with the synchronization process; otherwise, it marks the synchronization as failed due to a missing user GUID.
   - If the "ADUserGUID" exists, the service updates subscriber information by sending the request to "UPRSubscriberPORT" and "ADSubscriberPORT" for respective updates.
   - After each update, it receives a "SubscriberResponseMSG" to determine the success of the update.
   - The service then executes a "SynchronizerSubscriberRules" policy with the transformed "RCVSynchronizationRequestMSG" to apply additional synchronization rules.
   - It sets up a loop to handle multiple email targets based on "SubscriberRules.EmailTargetCount."
   - For each active "CurrentEmailTarget," the service transforms the "RCVSynchronizationRequestMSG" to create an "EmailLinkMSG" for creating email links.
   - It sends the "EmailLinkMSG" to "EmailLinkPORT" and receives a "CreateLinkResponse" to confirm successful link creation.
   - If the link creation is successful, the service constructs a "NewContactWelcomeEmailMSG" with relevant email details and sends it to "WelcomeEmailPORT" for sending the welcome email.
   - If the link creation fails, the service throws a "SyncException" with details from the "CreateLinkResponse" to handle the error.
   - The loop continues until all active email targets are processed.
   - The service then constructs a "BatchResponseSummaryCopyMSG" and sets its properties for further use.
   - If the "AppSource" in "BatchResponseSummaryCopyMSG" is "POUR" or "UNPOUR," the service sends it to "BatchProcessPORT" for processing.

3. Exception Handling:
   - The service includes exception handling to catch specific errors during the process.
   - If a "DeliveryFailureException" occurs, it writes the error description to the debug logs.
   - If a general "System.Exception" occurs, the service handles the error and terminates the orchestration.
   - Any other exceptions will suspend the orchestration with a message "General Exception from Synchronize Orch Suspended."


## Module: Edu.UHC.Synchronizer.Core

### Message Types:

| Message Type             | Body                     |
| -----------------------  | -----------------------  |
| EMailMPMMessageType      | SynchronizationBusinessRulesHelper.RawString EmailBody |

### Port Types:

| Port Type                 | Operation              | Message Type                   |
| ------------------------  | ---------------------- | ------------------------------ |
| PublishedSyncRequestPORTTYPE | oneway Operation_1   | SynchronizationRequest         |
| WelcomeEmailPORTTYPE       | oneway SendMail       | EMailMPMMessageType            |
| GetSynchronizationRequestPORTTYPE | oneway ReceiveSyncMsg | SynchronizationRequest     |
| SubscriberPORTTYPE         | requestresponse PublishToSubscribers | SynchronizationRequest, SynchronizationResult |
| EmailLinkPORTTYPE          | requestresponse CreateLink | SynchronizationRequest, SynchronizationResult |
| SiebelGetContactPORTTYPE   | requestresponse ContactInfoMessage | SynchronizationRequest, SynchronizationRequest |
| CheckHCOBRPORTTYPE         | requestresponse CheckHCOReaderBusinessRule | SynchronizationRequest, SynchronizationResult |
| BatchProcessPORTTYPE       | oneway SendSyncResult | SyncResponseSummary            |
| UPRSubscriberPORTTYPE      | requestresponse UpdateUPR | SynchronizationRequest, SynchronizationResult |
| ADSubscriberPORTTYPE       | requestresponse UpdateAD | SynchronizationRequest, SynchronizationResult |
| SubscribersSendPORTTYPE    | oneway PublishToSubscribers | SynchronizationRequest |
| SubscribersRLINKTYPE       | Subscribers { SubscribersSendPORTTYPE } |                                |

### Service: SynchronizeOrch

#### Ports:

| Port Name                 | Type                    | Binding                      |
| ------------------------  | ----------------------- | ---------------------------- |
| SynchronizeOrchPORT       | GetSynchronizationRequestPORTTYPE | Logical Binding         |
| BatchProcessPORT          | BatchProcessPORTTYPE    | Logical Binding              |
| UPRSubscriberPORT         | UPRSubscriberPORTTYPE   | Logical Binding              |
| ADSubscriberPORT          | ADSubscriberPORTTYPE    | Logical Binding              |
| EmailLinkPORT             | EmailLinkPORTTYPE       | DeliveryNotification Transmitted, Physical Binding (Microsoft.BizTalk.DefaultPipelines.PassThruTransmit) |
| WelcomeEmailPORT          | WelcomeEmailPORTTYPE    | DeliveryNotification Transmitted, Physical Binding (Microsoft.BizTalk.DefaultPipelines.PassThruTransmit) |

#### Messages:

| Message Type              | Variables/Helpers       |
| ------------------------  | ----------------------- |
| SynchronizerInteger       | CurrentIndex, LastIndex |
| EMailMPMMessageType       | NewContactWelcomeEmailMSG |
| SynchronizationResult     | SubscriberResponseMSG, CreateLinkResponse, HCOReaderResponseMSG |
| SynchronizationRequest    | EmailLinkMSG, RCVSynchronizationRequestMSG |
| SyncResponseSummary       | BatchResponseSummaryMSG, BatchResponseSummaryCopyMSG |

#### Variables and Helpers:

| Variable/Helper                 | Type/Class                    |
| ------------------------------- | ------------------------------ |
| CheckHCO                        | System.Boolean                |
| ErrorMessageVar                 | System.Exception              |
| SubscriberRules                 | SynchronizationBusinessRulesHelper.SychronizationSubscriberHelper |
| CurrentSubscriber               | SynchronizationBusinessRulesHelper.SubscriberCandidate |
| CurrentEmailTarget              | SynchronizationBusinessRulesHelper.EmailInfo |
| SyncResponseMessageString       | System.String                 |
| SyncResponseExceptionMessageString | System.String               |
| SyncResponseSuccessString       | System.String                 |
| varXMLDocSyncResponsesummary     | System.Xml.XmlDocument        |

#### Scope: Synchronized

The body contains the main processing logic.

#### Actions and Logic:

---

#### Table:

| Step | Action                                                   | Inputs/Conditions                                             | Outputs/Results                        |
|------|----------------------------------------------------------|---------------------------------------------------------------|---------------------------------------|
| 1    | Activate receive                                         | (SynchronizeOrchPORT.ReceiveSyncMsg, RCVSynchronizationRequestMSG) |                                       |
| 2    | Set CheckHCO to true.                                    |                                                               |                                       |
| 3    | Create instances                                        |                                                               | - ErrorMessageVar<br>- SubscriberRules<br>- CurrentEmailTarget<br>- Initialize string variables |
| 4    | Transform RCVSynchronizationRequestMSG to construct BatchResponseSummaryMSG. | RCVSynchronizationRequestMSG                             | BatchResponseSummaryMSG              |
| 5    | Check if RCVSynchronizationRequestMSG.UserProductEnrollment.UserProfile.UserProfileInfo.ADUserGUID is not empty. | RCVSynchronizationRequestMSG                             |                                       |
| 6    | If true:                                                 |                                                               |                                       |
| 7    | Send RCVSynchronizationRequestMSG to UPRSubscriberPORT.UpdateUPR. | RCVSynchronizationRequestMSG                             |                                       |
| 8    | Receive SubscriberResponseMSG.                           |                                                               | SubscriberResponseMSG                 |
| 9    | If SubscriberResponseMSG.Success >= 0:                  | SubscriberResponseMSG                                        |                                       |
| 10   | Send RCVSynchronizationRequestMSG to ADSubscriberPORT.UpdateAD. | RCVSynchronizationRequestMSG                             |                                       |
| 11   | Receive SubscriberResponseMSG.                           |                                                               | SubscriberResponseMSG                 |
| 12   | If SubscriberResponseMSG.Success >= 0, set SyncResponseSuccessString to "Done". | SubscriberResponseMSG                                        | SyncResponseSuccessString             |
| 13   | Otherwise, set SyncResponseMessageString to SubscriberResponseMSG.ErrorMessage and SyncResponseSuccessString to "Failed". | SubscriberResponseMSG                                        | SyncResponseMessageString, SyncResponseSuccessString |
| 14   | Execute SynchronizerSubscriberRules policy with RCVSynchronizationRequestMSG. | RCVSynchronizationRequestMSG                             |                                       |
| 15   | Update RCVSynchronizationRequestMSG with the transformed content. | RCVSynchronizationRequestMSG                             |                                       |
| 16   | Initialize CurrentIndex and LastIndex.                  |                                                               |                                       |
| 17   | Transform CurrentIndex and LastIndex with RCVSynchronizationRequestMSG to set CurrentEmailTarget. | RCVSynchronizationRequestMSG, CurrentIndex, LastIndex | CurrentEmailTarget                    |
| 18   | While CurrentIndex.Var < SubscriberRules.EmailTargetCount: | CurrentIndex, SubscriberRules                               |                                       |
| 19   | If CurrentEmailTarget.IsActive:                          | CurrentEmailTarget                                          |                                       |
| 20   | Transform EmailLinkMSG with RCVSynchronizationRequestMSG. | RCVSynchronizationRequestMSG                             | EmailLinkMSG                          |
| 21   | Send EmailLinkMSG to EmailLinkPORT.CreateLink.           | EmailLinkMSG                                                |                                       |
| 22   | Receive CreateLinkResponse.                              |                                                               | CreateLinkResponse                    |
| 23   | If CreateLinkResponse.Success >= 0:                     | CreateLinkResponse                                          |                                       |
| 24   | Construct NewContactWelcomeEmailMSG with relevant email details. | CreateLinkResponse, SubscriberRules                         | NewContactWelcomeEmailMSG              |
| 25   | If SubscriberRules.SendTestEmailOnly, set WelcomeEmailPORT.Address to mailto: + SubscriberRules.TestEmailAddress.    | SubscriberRules                                             | WelcomeEmailPORT.Address               |
|      | Else, set WelcomeEmailPORT.Address to mailto: + CurrentEmailTarget.EmailAddress. | CurrentEmailTarget                                          | WelcomeEmailPORT.Address               |
| 26   | Send NewContactWelcomeEmailMSG to WelcomeEmailPORT.SendMail. | NewContactWelcomeEmailMSG                                   |                                       |
| 27   | Otherwise, throw a SyncException with CreateLinkResponse.Success, CreateLinkResponse.ErrorType, CreateLinkResponse.ErrorSource, and CreateLinkResponse.ErrorMessage. | CreateLinkResponse                                          | SyncException                         |
| 28   | Transform CurrentIndex and LastIndex to update CurrentEmailTarget. | CurrentIndex, LastIndex                                      | CurrentEmailTarget                    |
| 29   | End the loop.                                           |                                                               |                                       |
| 30   | Otherwise, set SyncResponseMessageString to "User GUID is missing" and SyncResponseSuccessString to "Failed". |                                                               | SyncResponseMessageString, SyncResponseSuccessString |
| 31   | Construct BatchResponseSummaryCopyMSG and set its Message and Success properties. |                                                               | BatchResponseSummaryCopyMSG           |
| 32   | If BatchResponseSummaryCopyMSG.AppSource is "POUR" or "UNPOUR", send BatchResponseSummaryCopyMSG to BatchProcessPORT.SendSyncResult. | BatchResponseSummaryCopyMSG                                  |                                       |


#### List:

1. Activate receive (SynchronizeOrchPORT.ReceiveSyncMsg, RCVSynchronizationRequestMSG).
2. Set `CheckHCO` to true.
3. Create instances of `ErrorMessageVar`, `SubscriberRules`, `CurrentEmailTarget`, and initialize string variables.
4. Transform `RCVSynchronizationRequestMSG` to construct `BatchResponseSummaryMSG`.
5. Check if `RCVSynchronizationRequestMSG.UserProductEnrollment.UserProfile.UserProfileInfo.ADUserGUID` is not empty.
   - If true:
     1. Send `RCVSynchronizationRequestMSG` to `UPRSubscriberPORT.UpdateUPR`.
     2. Receive `SubscriberResponseMSG`.
     3. If `SubscriberResponseMSG.Success >= 0`:
        - Send `RCVSynchronizationRequestMSG` to `ADSubscriberPORT.UpdateAD`.
        - Receive `SubscriberResponseMSG`.
        - If `SubscriberResponseMSG.Success >= 0`, set `SyncResponseSuccessString` to "Done".
        - Otherwise, set `SyncResponseMessageString` to `SubscriberResponseMSG.ErrorMessage` and `SyncResponseSuccessString` to "Failed".
     4. Execute `SynchronizerSubscriberRules` policy with `RCVSynchronizationRequestMSG`.
     5. Update `RCVSynchronizationRequestMSG` with the transformed content.
     6. Initialize `CurrentIndex` and `LastIndex`.
     7. Transform `CurrentIndex` and `LastIndex` with `RCVSynchronizationRequestMSG` to set `CurrentEmailTarget`.
     8. While `CurrentIndex.Var < SubscriberRules.EmailTargetCount`:
        - If `CurrentEmailTarget.IsActive`:
          1. Transform `EmailLinkMSG` with `RCVSynchronizationRequestMSG`.
          2. Send `EmailLinkMSG` to `EmailLinkPORT.CreateLink`.
          3. Receive `CreateLinkResponse`.
          4. If `CreateLinkResponse.Success >= 0`:
             - Construct `NewContactWelcomeEmailMSG` with relevant email details.
             - If `SubscriberRules.SendTestEmailOnly`, set `WelcomeEmailPORT.Address` to `mailto:` + `SubscriberRules.TestEmailAddress`.
             - Else, set `WelcomeEmailPORT.Address` to `mailto:` + `CurrentEmailTarget.EmailAddress`.
             - Send `NewContactWelcomeEmailMSG` to `WelcomeEmailPORT.SendMail`.
          5. Otherwise, throw a `SyncException` with `CreateLinkResponse.Success`, `CreateLinkResponse.ErrorType`, `CreateLinkResponse.ErrorSource`, and `CreateLinkResponse.ErrorMessage`.
        - Transform `CurrentIndex` and `LastIndex` to update `CurrentEmailTarget`.
     9. End the loop.
   - Otherwise, set `SyncResponseMessageString` to "User GUID is missing" and `SyncResponseSuccessString` to "Failed".
6. Construct `BatchResponseSummaryCopyMSG` and set its `Message` and `Success` properties.
7. If `BatchResponseSummaryCopyMSG.AppSource` is "POUR" or "UNPOUR", send `BatchResponseSummaryCopyMSG` to `BatchProcessPORT.SendSyncResult`.

#### Exception Handling:

- Catch `DeliveryFailureException` and write the error description to debug.
- Catch `System.Exception`, handle the error, and terminate the orchestration.
- Catch any other exceptions and suspend the orchestration with a message "General Exception from Synchronize Orch Suspended".

![image](https://github.com/hunter-hartline_vizient/Orchestration/assets/138223562/7fa8dc04-6e54-42f6-85a5-5433338bc5ae)
