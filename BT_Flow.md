## Orchestrations and Data Flow in the System

### Introduction

Five key orchestration components: 

- **New Contact**
- **Existing Contact**
- **Synchronize**
- **Pour**
- **Error Handling**

### New Contact Orchestration

**Input**

- XML files added to the NewContacts directory on the MS Server.
- No required data transformations.

**Outputs**

1. **NewContact_ADAdapter**

   - External service (WCF).
   - Connects to `Edu.UHC.Biztalk.Shared.Subscriber_www_uhc_edu_biztalk`.
   - Interface: `Edu.UHC.Biztalk.Shared.SubscriberInterface`.
   - Sends to `Edu.UHC.Synchronizer.SubscriberInterface.NewContact_ADInsertContact_IN`.

2. **NewContact_HashProfileUpdate**

   - SQL query update.
   - Updates `UHC_CORE` DB via `http://Edu.UHC.Synchronizer/ProfileHashUpdateInsert`.
   - Result: `ProfileHashUpdateInsert -> SynchronizationResult`.

3. **NewContact_SiebelContactUpdate**

   - External service (WCF).
   - Connects to Salesforce via `Service_Response as NewContact_SalesforceContact_IN`.
   - Result: `Service_Response -> SynchronizationResult`.

4. **NewContact_DefaultGroupsQuery**

   - SQL query.
   - Retrieves from `USER_SYNC` database.
   - Response: `GetDefaultGroupsResponse`.

5. **Synchronize Orch**

   - Watches `D:\WORKS\BTSynchronizer\SynchronizeIN\` for XML documents.
   - Conditionally acts based on XML content (POUR or UNPOUR).

6. **NewContact_UpdateSyncTime**

   - Updates sync time for received XML.
   - SQL query updates `UHC_CORE` database.
   - Updates `Edu.UHC.Synchronizer.SiebelLastUpdatePollInterface`.
   - Result: `UpdateSyncTime_IN -> SynchronizationResult`.

### Existing Contact Orchestration

**Input**

- Watches XML files in `D:\WORKS\BTSynchronizer\ExistingContacts\*.xml`.

**Outputs**

1. **ExistingContact_HashProfileUpdate**

   - SQL query update.
   - Updates `UHC_CORE` DB.
   - Result: `ProfileHashUpdateInsert -> HashProfileInsertUpdate_IN -> SynchronizationResult`.

2. **BTSYNC_SynchronizeOrch**

   - Standard behavior as other main orchestrations.

3. **ExistingContact_UpdateSyncTime**

   - Updates sync time from received XML.
   - SQL query updates `UHC_CORE`.

### SynchronizeOrch

**Input**

- Monitors `D:\WORKS\BTSynchronizer\SynchronizeIN\` for XML files.

**Outputs**

1. **Subscriber_UPR**

   - External service (WCF) - `BTSubscriberWSSSO/UPRSubscriber.svc`.
   - Data transformation: `Synchorizer_Subscriber_IN -> SynchronizationResult`.

2. **Subscriber_AD**

   - External service (WCF) - `BTSubscriberWSSSO/OktaSubscriber.svc`.
   - Data transformation: `Synchronizer_Subscriber_IN -> SynchronizationResult`.

3. **Synchronize_EmailLink**

   - SQL query update.
   - Updates `UHC_CORE` DB.
   - Result: `Edu.UHC.Synchronizer.PwdLinkInterface.Synchronize_PwdLink_IN -> SynchronizationResult`.

4. **Synchronize_Welcome_EmailPort**

   - Simple configuration with Pass-Thru.

5. **Synchronize_BatchProcess**

   - Watches `./BatchResult` directory.
   - Files named by MessageID (%MessageID%.xml).

### PourProcess

**Input**

- SyncResponse input.

**Outputs**

1. **PourResultEmailPort**

   - Uses Pass-Thru Transmit.
   - Configuration details pending.

### ErrorHandleOrch

**Input**

- No specific input required.

**Outputs**

1. **ErrHandleEmailPort**

   - Uses Pass-Thru Transmit.
   - Detailed configuration information pending.
