
# External Services for BizTalk Orchestrations

**Biztalk communicates with three known services.**

**Often there is an initial check on `Request.ProfileAction`:**

**e.g.) OktaService and UPRService first check what ACTION is to take place.**

`SubscriberAction.INSERT`

`SubscriberAction.UPDATE`

`SubscriberAction.DELETE`

`SubscriberAction.DISABLE`

`SubscriberAction.READ`

For OktaService, the `Request.ProfileAction` is used to determine if the user is to be created or updated in Okta.

For UPRService, the `Request.ProfileAction` is used to determine if any products are to be added, removed, updated for a particular user.


## New Contact Orchestration
---

### BizTalk Okta Subscriber Service
[Impl.OKTASubscriber](https://dev.azure.com/Vizientinc/SSO/_git/BizTalkSubscriberService?path=/Impl.OKTASubscriber/OKTASubscriber.cs&_a=contents&version=GBmaster)

**Service is responsible for creating Okta account and initializing user name as email with a conditional check on `SourceSite` property for `Apexus`, adding `.APEXUS` to the email domain if needed.**

**Has several methods used for managing account in ActiveDirectory and Okta**

**Has an external reference to AuthorizationManager -- `tempUri url shows up here (?)`**

#### **This service is also used for SEND PORT `SubscriberAD` as part of the Synchronize Orchestration**


---

### BiztTalk Salesforce Operation Service
[SalesforceOperation Service](https://dev.azure.com/Vizientinc/SSO/_git/AthenaDataService?path=/AthenaDataService/SalesforceOperation.svc.cs&_a=contents&version=GBmaster)

**Contains methods for creating and updating Salesforce contacts.**

**References Metis stored procedures in order to invoke various processes for creating, deleting, modifying Salesforce Contacts (ProfileInfo)**

**For each method in this service, there is a corresponding Stored Procedure on Metis database.**

`Metis.InsertContact`

`Metis.InsertActivity`

`Metis.UpdateContact`

`Metis.UpdateActivity`

`Metis.UpsertMeetingParticipation`

`Metis.UpsertAsset`

`Metis.UpsertMeeting`


---

### BizTalk UPR Subscriber Service

[Impl.UPRSubscriber](https://dev.azure.com/Vizientinc/SSO/_git/BizTalkSubscriberService?path=/Impl.UPRSubscriber/UPRSubscriber.cs&_a=contents&version=GBmaster)


