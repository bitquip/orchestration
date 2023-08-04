New Contact Orchestration:
Input/Receive:

Entry through any XML files added to the NewContacts directory on the MS Server.
No transformations.
Outputs/Sends:

NewContact_ADAdapter:

External service (WCF)
Edu.UHC.Biztalk.Shared.Subscriber_www_uhc_edu_biztalk, Edu.UHC.Biztalk.Shared.SubscriberInterface, Edu.UHC.Synchronizer.SubscriberInterface.NewContact_ADInserContact_IN, Edu.UHC.Synchronizer.SubscriberInterface Edu.UHC.Synchronizer.Core.SynchronizationResult, Edu.UHC.Synchronizer.Core.
NewContact_HashProfileUpdate:

SQL query
UHC_CORE DB
http://Edu.UHC.Synchronizer/ProfileHashUpdateInsert
ProfileHashUpdateInsert -> SynchronizationResult.
NewContact_SiebelContactUpdate:

External service (WCF)
Service_Response as NewContact_SalesforceContact_IN -> SynchronizationResult.
NewContact_DefaultGroupsQuery:

SQL Query
GetDefaultGroups at UHC.Synchronizer
USER_SYNC database
GetDefaultGroupsResponse is returned.
Synchronize Orch:

File watch
Conditional check on XML document (POUR or UNPOUR)
NewContact_UpdateSyncTime:

Receives XML document
SQL query
UHC_CORE database
NewContact_SetSyncTime, NewContact_SetSyncTimeResponse
Edu.UHC.Synchronizer.SiebelLastUpdatePollInterface.NewContact_SetSyncTime
UpdateSyncTime_IN -> SynchronizationResult.
Existing Contact Orchestration:
Input/Receive Port:

File watch for XML files in D:\WORKS\BTSynchronizer\ExistingContacts*.xml.
Outputs/Send Ports:

ExistingContact_HashProfileUpdate:

SQL query/update
UHC_CORE database
ProfileHashUpdateInsertResponse
ProfileHashUpdateInsert -> HashProfileInsertUpdate_IN -> SynchronizationResult.
BTSYNC_SynchronizeOrch:

Similar to other main Orchestrations.
ExistingContact_UpdateSyncTime:

Takes an XML document
SQL query/update
UHC_CORE.
SynchronizeOrch:
Input/Receive:

File watch in D:\WORKS\BTSynchronizer\SynchronizeIN.
Outputs/Sends:

Subscriber_UPR:

External service (WCF)
Transforms: Synchorizer_Subscriber_IN -> SynchronizationResult.
Subscriber_AD:

External service (WCF)
Transforms: Synchronizer_Subscriber_IN -> SynchronizationResult.
Synchronize_EmailLink:

SQL query/update
UHC_CORE database
Edu.UHC.Synchronizer.PwdLinkInterface.UPSERTPWDLINK
Edu.UHC.Synchronizer.PwdLinkInterface.Synchronize_PwdLink_IN -> SynchronizationResult.
Synchronize_Welcome_EmailPort:

Bare configuration
Pass-Thru configuration.
Synchronize_BatchProcess:

File watch in ./BatchResult
Filename is MessageID (%MessageID%.xml).
PourProcess:
Input/Receive:

SyncResponse.
Outputs/Sends:

PourResultEmailPort:
Pass-Thru Transmit.
ErrorHandleOrch:
Input/Receive:

NONE.
Outputs/Sends:

ErrHandleEmailPort:
Pass-Thru Transmit.
