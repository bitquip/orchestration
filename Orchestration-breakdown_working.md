New Contact Orchestration

	Input/Receive:
	
		- Entry to this orchestration via *any xml files added to NewContacts directory on MS Server.
		- No transforms
	
	Outputs/Sends:
	
		- NewContact_ADAdapter
		
			- this one is a little bit confusing. revisit later.
		
			- External service (WCF)
		
			- Edu.UHC.Biztalk.Shared.Subscriber_www_uhc_edu_biztalk,Edu.UHC.Biztalk.Shared.SubscriberInterface, Edu.UHC.Synchronizer.SubscriberInterface.NewContact_ADInserContact_IN,Edu.UHC.Synchronizer.SubscriberInterface Edu.UHC.Synchronizer.Core.SynchronizationResult,Edu.UHC.Synchronizer.Core
			
		- NewContact_HashProfileUpdate
		
			- SQL query
			
			- UHC_CORE DB
		
			- http://Edu.UHC.Synchronizer/ProfileHashUpdateInsert
			
			- ProfileHashUpdateInsert ---> SynchronizationResult
			
		- NewContact_SiebelContactUpdate
		
			- External service (WCF)
			- Service_Response as NewContact_SalesforceContact_IN ---> SynchronizationResult
			
			
		- NewContact_DefaultGroupsQuery
		
			- SQL Query
			
			- GetDefaultGroups at UHC.Synchronizer
			
			- USER_SYNC database
			
			- GetDefaultGroupsResponse is returned
			
		- Synchronize Orch
		
			- Do NOT confuse with the SyncOrch orchestration (the bigger synchronization piece)
			- But they do the same thing? Hmm. It is different, due to other processes included with the bigger piece
		
			- File watch 
				
				- D:\WORKS\BTSynchronizer\SynchronizeIN\
			
			- Has a conditional check on xml document being passed
			
				- if POUR on bts. then OR if UNPOUR then AND				
			
		- NewContact_UpdateSyncTime
		
			- Receives xml document.
			
			- SQL query
			
			- UHC_CORE database
			
			- NewContact_SetSyncTime
			- NewContact_SetSyncTimeResponse

			- Edu.UHC.Synchronizer.SiebelLastUpdatePollInterface.NewContact_SetSyncTime			
			- UpdateSyncTime_IN ---> SynchronizationResult
				
				
------------------------------------------		

Existing Contact Orchestration
	
	Input/Receive Port
	
		- File watch
	
			- D:\WORKS\BTSynchronizer\ExistingContacts\*.xml
		
	Outputs/Send Ports
	
		- ExistingContact_HashProfileUpdate
		
			- SQL query/update
			
			- UHC_CORE database
			
			- ProfileHashUpdateInsertResponse
			
			- ProfileHashUpdateInsert -> HashProfileInsertUpdate_IN ---> SynchronizationResult
			
		
		- BTSYNC_SynchronizeOrch
		
			- same as in all other main Orchestrations.
			
		- ExistingContact_UpdateSyncTime
		
			- Takes an xml document (same as all others -- the result of receive port being SiebelContacts)
			- SQL query/update
			
			- UHC_CORE
			- 
			
			
		
---------------------------------------------

SynchronizeOrch

	Input/Receive
		
		- 
		- File watch.
		
			- D:\WORKS\BTSynchronizer\SynchronizeIN\
			
			- Same as BTSYNC_SynchronizeOrch

	Outputs/Sends
	
		- Subscriber_UPR
		
			- External service (WCF)
				- BTSubscriberWSSSO/UPRSubscriber.svc
			
			- Transforms: 
				- Synchorizer_Subscriber_IN ---> SynchronizationResult
		
		- Subscriber_AD
		
			- External service (WCF)
				- BTSubscriberWSSSO/OktaSubscriber.svc
			
			- Transforms:
				- Synchronizer_Subscriber_IN ---> SynchronizationResult
				
		
		- Synchronize_EmailLink
		
			- SQL query/update
			
			- UHC_CORE database
			
				- 	Edu.UHC.Synchronizer.PwdLinkInterface.UPSERTPWDLINK
				- Edu.UHC.Synchronizer.PwdLinkInterface.Synchronize_PwdLink_IN ---> SynchronizationResult
		
		
		- Synchronize_Welcome_EmailPort (name is long and strange on this one, recheck)!
		
			- Bare configuration
			- Has Pass-Thru configuration
		
		
		- Synchronize_BatchProcess
		
			- File watch
			
				- ./BatchResult
				
				- Filename is MessageID (%MessageID%.xml)
				
				- That is all I have found so far. Will add to it.


--------------------------------------------------

PourProcess

	Input/Receive
	
		- SyncResponse
		
	Outputs/Sends
	
		- PourResultEmailPort
		
			- Pass Thru Transmit
			
				- Only configuration known so far
				

---------------------------------------------------

ErrorHandleOrch

	Input/Receive
	
		- NONE
		
	Outputs/Sends
	
		- ErrHandleEmailPort
		
			- Pass Thru Transmit
			
			- All that is known for now
			
-------------------------------------------------------
