# ErrorHandlingOrch.odx

![image](https://github.com/bitquip/orchestration/assets/106825912/318c1523-d485-4943-9387-90ef7878653b)

## Module Metadata

| Property          | Value                       |
|-------------------|-----------------------------|
| MajorVersion      | 1                           |
| MinorVersion      | 3                           |
| Core              |                             |
| ScheduleModel     |                             |
| Module Name       | Edu.UHC.Synchronizer.Core   |

The module metadata provides information about the BizTalk module. It specifies the version, core components, schedule model (if applicable), and the name of the module, which is "Edu.UHC.Synchronizer.Core."

## PortType: ErrHandleEmailPortType

| Property          | Value                       |
|-------------------|-----------------------------|
| Synchronous       | False                       |
| TypeModifier      | Internal                    |
| PortType Name     | ErrHandleEmailPortType      |

The `ErrHandleEmailPortType` port type is declared as internal and non-synchronous. It contains one operation called `SendMail` that takes a message of type `EMailMPMMessageType` and is a one-way operation (oneway).

## Service: ErrorHandleOrch

| Property          | Value                       |
|-------------------|-----------------------------|
| TypeModifier      | Internal                    |
| Service Name      | ErrorHandleOrch            |

The `ErrorHandleOrch` service is declared as internal. It is a service that represents an orchestration and handles error processing.

## Messages:

1. `SynchronizationException`: Represents a custom message type called `SynchronizationException`. This message is used to hold synchronization exceptions.
2. `EMailMPMMessageType`: Represents a custom message type called `EMailMPMMessageType`. This message type is used for sending emails with specific properties.
3. `FormattedEmailMSG`: Represents a message of type `EMailMPMMessageType`. This message is used to store the formatted email content before sending it.
4. `SyncException`: Represents a message of type `SynchronizationException`. This message is used to hold synchronization exceptions.
   
## Variables:

| Variable Name     | Type                                              | Purpose                                      |
|-------------------|---------------------------------------------------|----------------------------------------------|
| SubscriberRules   | SynchronizationBusinessRulesHelper.SychronizationSubscriberHelper | An instance of a helper class that contains synchronization business rules. |
| ExceptionMessage  | System.Exception                                 | Holds the exception message for synchronization exceptions.                    |

The variables declared within the orchestration are as follows:
1. `SubscriberRules`: An instance of the `SynchronizationBusinessRulesHelper.SychronizationSubscriberHelper` class, which contains synchronization business rules for handling emails.
2. `ExceptionMessage`: A variable of type `System.Exception`, used to hold the exception message for synchronization exceptions.

## Orchestration Flow:

1. **Construct SyncException Message**:

   The orchestration starts by constructing a `SyncException` message. This message is created from the `ExceptionMessage` variable using a construct statement.

2. **Execute Business Rules**:

   The orchestration enters a scope to execute business rules. Within this scope, a policy named "SynchronizerExceptionRules" is executed, passing the `SyncException` message and the `SubscriberRules` instance as parameters. The business rules process the exception and update the `SyncException` message accordingly.

3. **Construct FormattedEmailMSG**:

   After executing the business rules, the orchestration constructs the `FormattedEmailMSG` message. The message properties, such as email body, content type, subject, SMTP host, and authentication settings, are set based on the values provided by the `SubscriberRules` instance.

4. **Send Email**:

   Finally, the orchestration sends the `FormattedEmailMSG` message to the `ErrHandleEmailPORT` using the `SendMail` operation.

## Port Declaration: ErrHandleEmailPORT

| Property          | Value                       |
|-------------------|-----------------------------|
| Port Modifier     | Uses                        |
| Orientation       | Left                        |
| Port Name         | ErrHandleEmailPORT          |
| Type              | Edu.UHC.Synchronizer.Core.ErrHandleEmailPortType |

The `ErrHandleEmailPORT` is declared as a "Uses" port, meaning that the orchestration consumes this port to send emails. The port type associated with this port is `ErrHandleEmailPortType`, which is defined in the module's port types.

## Physical Binding Attribute

| Property          | Value                       |
|-------------------|-----------------------------|
| InPipeline        | Microsoft.BizTalk.DefaultPipelines.XMLReceive |
| OutPipeline       | Microsoft.BizTalk.DefaultPipelines.PassThruTransmit |
| TransportType     | HTTP                        |
| URI               | http://tempURI              |
| IsDynamic         | True                        |

The `ErrHandleEmailPORT` is bound to a physical port using the `PhysicalBindingAttribute`. It specifies the inbound and outbound pipelines, transport type (HTTP), URI, and whether the binding is dynamic (True). Note that the URI is set to "http://tempURI," indicating that it is a placeholder and should be dynamically resolved at runtime.

The orchestration handles synchronization exceptions, executes business rules, constructs email messages, and sends formatted emails to the designated email port. The binding to the physical port allows the orchestration to send emails using the specified transport (HTTP).
