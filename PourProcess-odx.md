## PourProcess Service Description

1. Port Types:

   | Port Type             | Operation                   | Message Type      |
   | --------------------- | ---------------------------- | ----------------- |
   | SyncResponsePORTTYPE  | oneway GetSyncResponse      | SyncResponseSummary |
   | PourResultEmailPORTTYPE | oneway SendResultEmail    | EMailMPMMessageType |

2. Correlation Type:

   | Correlation Type    | Property         |
   | ------------------- | -----------------|
   | BatchIDCORRTYPE     | BatchID           |

3. Service Information:

   | Attribute | Value                           |
   | --------- | ------------------------------- |
   | Name      | PourProcess                    |
   | BPEL Exportable | false                      |

4. Ports:

   | Port Name              | Port Type                      | Binding                             |
   | ---------------------- | ------------------------------ | ----------------------------------- |
   | SyncResponsePORT       | SyncResponsePORTTYPE           | Logical Binding                     |
   | PourResultEmailPORT    | PourResultEmailPORTTYPE        | Physical Binding (Microsoft.BizTalk.DefaultPipelines.PassThruTransmit) |

5. Variables and Helpers:

   | Variable / Helper          | Type / Class                    |
   | -------------------------- | ------------------------------  |
   | BatchID                    | System.Int32                    |
   | PEmailRules                | SynchronizationBusinessRulesHelper.SychronizationSubscriberHelper |

6. Scope: Body

   The body contains the main processing logic.

7. Actions and Logic:

   | Step | Action                                         |
   | ---- | ---------------------------------------------- |
   | 1    | Receive `SyncResponseSummary` message from `SyncResponsePORT.GetSyncResponse` with correlation `BatchIDCORR` |
   | 2    | Set `BatchCount` to 1                          |
   | 3    | Create a new instance of `PEmailRules`        |
   | 4    | Execute `PourEmailRules` policy using `PourLogResponseMSG` as the input |
   | 5    | Construct `PourLogResponseMSG` with the result from the policy execution |
   | 6    | Save log information to a file using `PEmailRules.SaveResultFile` |
   | 7    | Enter a loop while `PourLogResponseMSG.BatchCount > BatchCount` |
   | 8    | Within the loop, listen for a response and update `BatchCount` accordingly |
   | 9    | After the loop, save final log information to a file |
   | 10   | Construct `BatchCompletedEmailMSG` with relevant email details |
   | 11   | Send the `BatchCompletedEmailMSG` to `PourResultEmailPORT` |


![image](https://github.com/hunter-hartline_vizient/Orchestration/assets/138223562/ba0dab99-4d2e-42c3-82f8-a21f31d63a36)


9. Relationships:

   | Source                | Target                         | Relationship Description                         |
   | --------------------- | ------------------------------ | -----------------------------------------------   |
   | SyncResponsePORT      | GetSyncResponse                | Receives `SyncResponseSummary` message with `BatchIDCORR` correlation |
   | PourResultEmailPORT   | SendResultEmail                | Sends `BatchCompletedEmailMSG` for email notification |

![image](https://github.com/hunter-hartline_vizient/Orchestration/assets/138223562/71c988cb-20de-431d-b674-993d6afa3b05)
