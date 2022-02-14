[comment]: # "Auto-generated SOAR connector documentation"
# Fireeye ETP

Publisher: Robert Drouin  
Connector Version: 2\.0\.1  
Product Vendor: Fireeye  
Product Name: Fireeye Email Threat Prevention  
Product Version Supported (regex): "\.\*"  
Minimum Product Version: 5\.1\.0  

Cloud Edition provides RESTful APIs for custom integration\. The APIs are provided for Advanced Threats, Email Trace, and Quarantine functionalities

[comment]: # " File: README.md"
[comment]: # "  Copyright (c) Robert Drouin, 2021-2022"
[comment]: # ""
[comment]: # "  Licensed under the Apache License, Version 2.0 (the 'License');"
[comment]: # "  you may not use this file except in compliance with the License."
[comment]: # "  You may obtain a copy of the License at"
[comment]: # "      http://www.apache.org/licenses/LICENSE-2.0"
[comment]: # "  Unless required by applicable law or agreed to in writing, software distributed under"
[comment]: # "  the License is distributed on an 'AS IS' BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND,"
[comment]: # "  either express or implied. See the License for the specific language governing permissions"
[comment]: # "  and limitations under the License."
[comment]: # ""
**Permissions**

The following is a breakdown of the permission needed for the API user to be able to use the action
in this app. These permissions are controlled in the IAM section of the ETP server. For information
pertaining to setting up the API user refer to the API documentation located at
<https://docs.fireeye.com/docs/index.html#ETP> .

For any API access, the following entitlements are required:

-   iam.users.browse
-   iam.orgs.self.read

For accessing alerts APIs, the following additional entitlements are required:

-   etp.alerts.read

For accessing trace APIs, the following additional entitlements are required:

-   etp.email_trace.read

For accessing quarantine APIs, the following additional entitlements are required:

-   etp.quarantine.update
-   etp.quarantine.read
-   etp.quarantine.delete

  
  

**Actions**

It is important to know that all the actions in this app take affect on the Fireeye ETP side, except
for *remediating email* . For example, deleting a quarantined email is the action that deletes the
email in the Fireeye ETP quarantined section, not in the Office365. Office365 may have other actions
for quarantined emails from their own security suite

*Remediate Email action*

This action talks to the API for Office365 which will move/delete the email depending on the input
from the user. This action does not have an undo function and if the user selects action_overrite of
delete, this email will be permanently deleted in the Office365 environment and not recoverable. Use
cautiously!

## Port Information

The app uses HTTP/ HTTPS protocol for communicating with the Fireeye ETP server. Below are the
default ports used by the Splunk SOAR Connector.

| SERVICE NAME | TRANSPORT PROTOCOL | PORT |
|--------------|--------------------|------|
| http         | tcp                | 80   |
| https        | tcp                | 443  |


### Configuration Variables
The below configuration variables are required for this Connector to operate.  These variables are specified when configuring a Fireeye Email Threat Prevention asset in SOAR.

VARIABLE | REQUIRED | TYPE | DESCRIPTION
-------- | -------- | ---- | -----------
**base\_url** |  required  | string | The URL for your ETP instance
**api\_key** |  required  | password | API key

### Supported Actions  
[test connectivity](#action-test-connectivity) - Validate the asset configuration for connectivity using the supplied configuration  
[on poll](#action-on-poll) - Callback action for the on\_poll ingest functionality  
[list alerts](#action-list-alerts) - Get a list of alerts from the ETP instance  
[get alert](#action-get-alert) - Get details about a specific alert from the ETP instance  
[list email attributes](#action-list-email-attributes) - Get all the attributes from a list of email messages  
[get email attributes](#action-get-email-attributes) - Get the attributes of a particular message with the specified Email Security message ID  
[trace email](#action-trace-email) - Search for Email Message by specifying one or more filters  
[trace message](#action-trace-message) - Search for Email Message by specifying the Queue/Message\-ID of the Downstream MTA or the Original Message\-ID\. At least one parameter must be filled out\. All fields are filtered by the IN clause where applicable  
[download email](#action-download-email) - Download the email header as a text file and add it to the vault  
[download pcap](#action-download-pcap) - Downloads all the PCAP files of the alert for a specified alert ID and add the files to the vault  
[download malware files](#action-download-malware-files) - Download all malware files of the alert for a specified alert ID and add the files to the vault  
[download case files](#action-download-case-files) - Download all case files of the alert for a specified alert ID and add the files to the vault  
[remediate emails](#action-remediate-emails) - Enqueues the message IDs provided in the request for remediation from the user's Office365 mailbox  
[get quarantined email](#action-get-quarantined-email) - Download the email file present in the quarantine for the given Email Security message ID and add it to the vault  
[unquarantine email](#action-unquarantine-email) - Release the email file\(s\) present in the Quarantine within ETP  
[delete quarantined email](#action-delete-quarantined-email) - Delete the email file\(s\) present in quarantine for the given Email Security message ID  
[list quarantined emails](#action-list-quarantined-emails) - Get a list of quarantined emails from a given query filter  

## action: 'test connectivity'
Validate the asset configuration for connectivity using the supplied configuration

Type: **test**  
Read only: **True**

#### Action Parameters
No parameters are required for this action

#### Action Output
No Output  

## action: 'on poll'
Callback action for the on\_poll ingest functionality

Type: **ingest**  
Read only: **True**

<p>Ingest alerts from ETP into Phantom\. If 'start\_time' is not specified, the default is past 10 days\. If 'end\_time' is not specified, the default is past 10 days\.</p>

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**container\_id** |  optional  | Container IDs to size the ingestion to | string | 
**start\_time** |  optional  | Start of the time range, in epoch time \(milliseconds\) | numeric | 
**end\_time** |  optional  | End of the time range, in epoch time \(milliseconds\) | numeric | 
**container\_count** |  optional  | The maximum number of container records to query for | numeric | 
**artifact\_count** |  optional  | The maximum number of artifact records to query for | numeric | 

#### Action Output
No Output  

## action: 'list alerts'
Get a list of alerts from the ETP instance

Type: **investigate**  
Read only: **True**

<p>The email status allows filtering by specific statuses\. The valid values for email status are\:</p><p><ul><li>ACE\: Passthrough</li><li>quarantined</li><li>released</li><li>deleted</li><li>bcc\:dropped</li><li>delivered \(retroactive\)</li><li>dropped \(oob retroactive\)</li></ul></p><p>If the 'size' parameter value is greater than the mentioned range\(1\-200\), then the max value of range\(i\.e\: 200\) will be in consideration\.</p>

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**legacy\_id** |  optional  | Alert ID as shown in Email Security Web Portal | numeric |  `fireeyeetp legacy id` 
**message\_id** |  optional  | ETP Email message ID | string |  `fireeyeetp message id` 
**email\_status** |  optional  | Filter by ETP email status\. Comma\-separated list allowed\. See app documentation for a list of acceptable values | string | 
**num\_days** |  optional  | The number of days to get alerts for \(ETP Defaults to last 90 days\) | numeric | 
**size** |  optional  | Number of alerts to retrieve per response\. Valid range\: 1\-200 \(ETP Defaults to 20\) | numeric | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.legacy\_id | numeric |  `fireeyeetp legacy id` 
action\_result\.parameter\.message\_id | string |  `fireeyeetp message id` 
action\_result\.parameter\.email\_status | string | 
action\_result\.parameter\.num\_days | numeric | 
action\_result\.parameter\.size | numeric | 
action\_result\.data\.\*\.attributes\.alert\.alert\_type | string | 
action\_result\.data\.\*\.attributes\.alert\.malware\_md5 | string |  `md5`  `hash` 
action\_result\.data\.\*\.attributes\.alert\.product | string | 
action\_result\.data\.\*\.attributes\.alert\.timestamp | string | 
action\_result\.data\.\*\.attributes\.ati | string | 
action\_result\.data\.\*\.attributes\.email\.attachment | string | 
action\_result\.data\.\*\.attributes\.email\.etp\_message\_id | string |  `fireeyeetp message id` 
action\_result\.data\.\*\.attributes\.email\.headers\.cc | string |  `email` 
action\_result\.data\.\*\.attributes\.email\.headers\.from | string |  `email` 
action\_result\.data\.\*\.attributes\.email\.headers\.subject | string | 
action\_result\.data\.\*\.attributes\.email\.headers\.to | string |  `email` 
action\_result\.data\.\*\.attributes\.email\.smtp\.mail\_from | string |  `email` 
action\_result\.data\.\*\.attributes\.email\.smtp\.rcpt\_to | string |  `email` 
action\_result\.data\.\*\.attributes\.email\.source\_ip | string |  `ip` 
action\_result\.data\.\*\.attributes\.email\.status | string | 
action\_result\.data\.\*\.attributes\.email\.timestamp\.accepted | string | 
action\_result\.data\.\*\.attributes\.meta\.acknowledged | boolean | 
action\_result\.data\.\*\.attributes\.meta\.last\_malware | string | 
action\_result\.data\.\*\.attributes\.meta\.last\_modified\_on | string | 
action\_result\.data\.\*\.attributes\.meta\.legacy\_id | numeric |  `fireeyeetp legacy id` 
action\_result\.data\.\*\.attributes\.meta\.read | boolean | 
action\_result\.data\.\*\.attributes\.meta\.timestamp\.db\_insert\_time | string | 
action\_result\.data\.\*\.attributes\.meta\.timestamp\.es\_insert\_time | string | 
action\_result\.data\.\*\.id | string |  `fireeyeetp alert id` 
action\_result\.data\.\*\.links\.detail | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'get alert'
Get details about a specific alert from the ETP instance

Type: **investigate**  
Read only: **True**

<p>Get details about a specific alert by alert ID\.</p>

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**alert\_id** |  required  | Alert ID as shown in Email Security Web Portal | string |  `fireeyeetp alert id` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.alert\_id | string |  `fireeyeetp alert id` 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.ack | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.action | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.alert\_type | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.analysis | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.anomaly | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.cnc\_service\.cnc\_service\.\*\.address | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.cnc\_service\.cnc\_service\.\*\.channel | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.cnc\_service\.cnc\_service\.\*\.port | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.cnc\_service\.cnc\_service\.\*\.protocol | numeric | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.cnc\_service\.cnc\_service\.\*\.sname | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.cnc\_service\.cnc\_service\.\*\.type | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.malware\_detected\.malware\.\*\.application | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.malware\_detected\.malware\.\*\.domain | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.malware\_detected\.malware\.\*\.downloaded\_at | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.malware\_detected\.malware\.\*\.executed\_at | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.malware\_detected\.malware\.\*\.md5sum | string |  `md5`  `hash` 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.malware\_detected\.malware\.\*\.name | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.malware\_detected\.malware\.\*\.origid | numeric | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.malware\_detected\.malware\.\*\.original | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.malware\_detected\.malware\.\*\.profile | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.malware\_detected\.malware\.\*\.sha1 | string |  `sha1` 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.malware\_detected\.malware\.\*\.sha256 | string |  `sha256` 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.malware\_detected\.malware\.\*\.sha512 | string |  `sha512` 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.malware\_detected\.malware\.\*\.sid | numeric | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.malware\_detected\.malware\.\*\.stype | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.malware\_detected\.malware\.\*\.submitted\_at | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.malware\_detected\.malware\.\*\.type | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.analysis\.ftype | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.analysis\.mode | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.analysis\.product | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.analysis\.verison | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.application\.app\_name | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.malicious\_alert\.\*\.classtype | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.malicious\_alert\.\*\.display\_msg | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.os\.arch | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.os\.name | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.os\.sp | numeric | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.os\.verison | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.os\_monitor\.build | numeric | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.os\_monitor\.date | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.os\_monitor\.time | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.os\_monitor\.verision | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.osinfo | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.process\_information\.\*\.cmdline | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.process\_information\.\*\.fid\.ads | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.process\_information\.\*\.fid\.fid | numeric | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.process\_information\.\*\.filesize | numeric | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.process\_information\.\*\.md5sum | string |  `md5`  `hash` 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.process\_information\.\*\.mode | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.process\_information\.\*\.no\_extend | boolean | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.process\_information\.\*\.parentname | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.process\_information\.\*\.pid | numeric | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.process\_information\.\*\.ppid | numeric | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.process\_information\.\*\.sha1sum | string |  `sha1` 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.process\_information\.\*\.sha256sum | string |  `sha256` 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.process\_information\.\*\.timestamp | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.os\_changes\.process\_information\.\*\.value | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.explanation\.protocol | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.malware\_md5 | string |  `md5`  `hash` 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.name | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.product | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.severity | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.alert\.timestamp | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.ati | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.email\.attachment | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.email\.etp\_message\_id | string |  `fireeyeetp message id` 
action\_result\.data\.\*\.data\.\*\.attributes\.email\.headers\.cc | string |  `email` 
action\_result\.data\.\*\.data\.\*\.attributes\.email\.headers\.from | string |  `email` 
action\_result\.data\.\*\.data\.\*\.attributes\.email\.headers\.subject | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.email\.headers\.to | string |  `email` 
action\_result\.data\.\*\.data\.\*\.attributes\.email\.smtp\.mail\_from | string |  `email` 
action\_result\.data\.\*\.data\.\*\.attributes\.email\.smtp\.rcpt\_to | string |  `email` 
action\_result\.data\.\*\.data\.\*\.attributes\.email\.source\_ip | string |  `ip` 
action\_result\.data\.\*\.data\.\*\.attributes\.email\.status | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.email\.timestamp\.accepted | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.meta\.acknowledged | boolean | 
action\_result\.data\.\*\.data\.\*\.attributes\.meta\.last\_malware | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.meta\.last\_modified\_on | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.meta\.legacy\_id | numeric |  `fireeyeetp legacy id` 
action\_result\.data\.\*\.data\.\*\.attributes\.meta\.read | boolean | 
action\_result\.data\.\*\.data\.\*\.attributes\.meta\.timestamp\.db\_insert\_time | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.meta\.timestamp\.es\_insert\_time | string | 
action\_result\.data\.\*\.data\.\*\.id | string |  `fireeyeetp alert id` 
action\_result\.data\.\*\.data\.\*\.links\.detail | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'list email attributes'
Get all the attributes from a list of email messages

Type: **investigate**  
Read only: **True**

<p>Gets a list of messages that include specified message attributes that are accessible in the Email Security portal\.</p><p>If the 'size' parameter value is greater than the mentioned range\(1\-200\), then the max value of range\(i\.e\: 200\) will be in consideration\.</p>

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**size** |  optional  | The number of alerts to include in a response\. Valid range\: 1\-200 \(ETP Defaults to 20\) | numeric | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.size | numeric | 
action\_result\.data\.\*\.data\.\*\.attributes\.acceptedDateTime | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.countryCode | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.domain | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.downStreamMsgID | string |  `fireeyeetp downstream message id` 
action\_result\.data\.\*\.data\.\*\.attributes\.emailSize | numeric | 
action\_result\.data\.\*\.data\.\*\.attributes\.lastModifiedDateTime | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.originalMessageID | string |  `fireeyeetp original message id` 
action\_result\.data\.\*\.data\.\*\.attributes\.recipientHeader | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.recipientSMTP | string |  `email` 
action\_result\.data\.\*\.data\.\*\.attributes\.rejectionReason\.code | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.rejectionReason\.description | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.senderHeader | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.senderIP | string |  `ip` 
action\_result\.data\.\*\.data\.\*\.attributes\.senderSMTP | string |  `email` 
action\_result\.data\.\*\.data\.\*\.attributes\.status | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.subject | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.AS | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.AT | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.AV | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.ActionYARA | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.PV | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.YARA | string | 
action\_result\.data\.\*\.data\.\*\.id | string |  `fireeyeetp message id` 
action\_result\.data\.\*\.data\.\*\.included\.\*\.attributes\.name | string | 
action\_result\.data\.\*\.data\.\*\.included\.\*\.type | string |  `domain` 
action\_result\.data\.\*\.data\.\*\.type | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'get email attributes'
Get the attributes of a particular message with the specified Email Security message ID

Type: **investigate**  
Read only: **True**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**etp\_message\_id** |  required  | The ID of the Email Security message | string |  `fireeyeetp message id` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.etp\_message\_id | string |  `fireeyeetp message id` 
action\_result\.data\.\*\.data\.\*\.attributes\.acceptedDateTime | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.countryCode | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.domain | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.downStreamMsgID | string |  `fireeyeetp downstream message id` 
action\_result\.data\.\*\.data\.\*\.attributes\.emailSize | numeric | 
action\_result\.data\.\*\.data\.\*\.attributes\.lastModifiedDateTime | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.originalMessageID | string |  `fireeyeetp original message id` 
action\_result\.data\.\*\.data\.\*\.attributes\.recipientHeader | string |  `email` 
action\_result\.data\.\*\.data\.\*\.attributes\.recipientSMTP | string |  `email` 
action\_result\.data\.\*\.data\.\*\.attributes\.rejectionReason\.code | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.rejectionReason\.description | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.senderHeader | string |  `email` 
action\_result\.data\.\*\.data\.\*\.attributes\.senderIP | string |  `ip` 
action\_result\.data\.\*\.data\.\*\.attributes\.senderSMTP | string |  `email` 
action\_result\.data\.\*\.data\.\*\.attributes\.status | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.subject | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.AS | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.AT | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.AV | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.ActionYARA | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.PV | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.YARA | string | 
action\_result\.data\.\*\.data\.\*\.id | string |  `fireeyeetp message id` 
action\_result\.data\.\*\.data\.\*\.included\.\*\.attributes\.name | string | 
action\_result\.data\.\*\.data\.\*\.included\.\*\.type | string | 
action\_result\.data\.\*\.data\.\*\.type | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'trace email'
Search for Email Message by specifying one or more filters

Type: **investigate**  
Read only: **True**

<p>At least one parameter must be filled out\. All fields are filtered by the IN clause where applicable searching always an AND\.</p><p>The modified date always uses the greater than or equal to\. For example, if you want to search for the last 7 days, put a timestamp from 7 days ago\. IE\. 2017\-10\-03T00\:00\:00\.000Z\. Also, note that the DatetTime stamps need to be in UTC otherwise the results will be off\. ETP assumes all DateTime stamps are in UTC\.</p><p>The status field allows for the following values<ul><li>accepted</li><li>deleted</li><li>delivered</li><li>delivered \(retroactive\)</li><li>dropped</li><li>dropped oob</li><li>dropped \(oob retroactive\)</li><li>permanent failure</li><li>processing</li><li>quarantined</li><li>rejected</li><li>temporary failure</li></ul></p><p>The tags field allows for the following values<ul><li>auto\_remediation</li><li>impersonation</li><li>manual\_remediation</li></ul></p><p>If the 'size' parameter value is greater than the mentioned range\(1\-300\), then the max value of range\(i\.e\: 300\) will be in consideration\.</p>

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**domains** |  optional  | Specific domain\(s\) to search by\. Comma\-separated list allowed | string |  `domain` 
**modified\_date** |  optional  | Datetime stamp to limit the search period | string | 
**recipients** |  optional  | The recipients of the email\. The maximum allowed per request is 10\. Comma\-separated list allowed | string |  `email` 
**sender** |  optional  | The sender of the email\. The maximum allowed per request is 10\. Comma\-separated list allowed | string |  `email` 
**status** |  optional  | ETP status to search by\. Comma\-separated list allowed | string | 
**subject** |  optional  | The subject of the email to search for | string | 
**tags** |  optional  | Tags from ETP that are associated with an email | string | 
**size** |  optional  | The number of alerts to include in a response\. Valid range\: 1\-300 \(ETP Defaults to 20\) | numeric | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.domains | string |  `domain` 
action\_result\.parameter\.modified\_date | string | 
action\_result\.parameter\.recipients | string |  `email` 
action\_result\.parameter\.sender | string |  `email` 
action\_result\.parameter\.status | string | 
action\_result\.parameter\.subject | string | 
action\_result\.parameter\.tags | string | 
action\_result\.parameter\.size | numeric | 
action\_result\.data\.\*\.data\.\*\.attributes\.acceptedDateTime | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.countryCode | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.domain | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.downStreamMsgID | string |  `fireeyeetp downstream message id` 
action\_result\.data\.\*\.data\.\*\.attributes\.emailSize | numeric | 
action\_result\.data\.\*\.data\.\*\.attributes\.lastModifiedDateTime | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.originalMessageID | string |  `fireeyeetp original message id` 
action\_result\.data\.\*\.data\.\*\.attributes\.recipientHeader | string |  `email` 
action\_result\.data\.\*\.data\.\*\.attributes\.recipientSMTP | string |  `email` 
action\_result\.data\.\*\.data\.\*\.attributes\.rejectionReason\.code | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.rejectionReason\.description | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.senderHeader | string |  `email` 
action\_result\.data\.\*\.data\.\*\.attributes\.senderIP | string |  `ip` 
action\_result\.data\.\*\.data\.\*\.attributes\.senderSMTP | string |  `email` 
action\_result\.data\.\*\.data\.\*\.attributes\.status | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.subject | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.AS | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.AT | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.AV | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.ActionYARA | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.PV | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.YARA | string | 
action\_result\.data\.\*\.data\.\*\.id | string |  `fireeyeetp message id` 
action\_result\.data\.\*\.data\.\*\.included\.\*\.attributes\.name | string | 
action\_result\.data\.\*\.data\.\*\.included\.\*\.type | string | 
action\_result\.data\.\*\.data\.\*\.type | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'trace message'
Search for Email Message by specifying the Queue/Message\-ID of the Downstream MTA or the Original Message\-ID\. At least one parameter must be filled out\. All fields are filtered by the IN clause where applicable

Type: **investigate**  
Read only: **True**

<p>If the 'size' parameter value is greater than the mentioned range\(1\-300\), then the max value of range\(i\.e\: 300\) will be in consideration\.</p>

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**original\_message\_id** |  optional  | The email's Original\-Message\-ID header value | string |  `fireeyeetp original message id` 
**downstream\_message\_id** |  optional  | The email's Downstream\-Message\-ID header value | string |  `fireeyeetp downstream message id` 
**size** |  optional  | The number of alerts to include in a response\. Valid range\: 1\-300 \(ETP Defaults to 20\) | numeric | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.original\_message\_id | string |  `fireeyeetp original message id` 
action\_result\.parameter\.downstream\_message\_id | string |  `fireeyeetp downstream message id` 
action\_result\.parameter\.size | numeric | 
action\_result\.data\.\*\.data\.\*\.attributes\.acceptedDateTime | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.countryCode | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.domain | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.downStreamMsgID | string |  `fireeyeetp downstream message id` 
action\_result\.data\.\*\.data\.\*\.attributes\.emailSize | numeric | 
action\_result\.data\.\*\.data\.\*\.attributes\.lastModifiedDateTime | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.originalMessageID | string |  `fireeyeetp original message id` 
action\_result\.data\.\*\.data\.\*\.attributes\.recipientHeader | string |  `email` 
action\_result\.data\.\*\.data\.\*\.attributes\.recipientSMTP | string |  `email` 
action\_result\.data\.\*\.data\.\*\.attributes\.rejectionReason\.code | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.rejectionReason\.description | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.senderHeader | string |  `email` 
action\_result\.data\.\*\.data\.\*\.attributes\.senderIP | string |  `ip` 
action\_result\.data\.\*\.data\.\*\.attributes\.senderSMTP | string |  `email` 
action\_result\.data\.\*\.data\.\*\.attributes\.status | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.subject | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.AS | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.AT | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.AV | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.ActionYARA | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.PV | string | 
action\_result\.data\.\*\.data\.\*\.attributes\.verdicts\.YARA | string | 
action\_result\.data\.\*\.data\.\*\.id | string |  `fireeyeetp message id` 
action\_result\.data\.\*\.data\.\*\.included\.\*\.attributes\.name | string | 
action\_result\.data\.\*\.data\.\*\.included\.\*\.type | string | 
action\_result\.data\.\*\.data\.\*\.type | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'download email'
Download the email header as a text file and add it to the vault

Type: **investigate**  
Read only: **True**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**etp\_message\_id** |  required  | The ID of the Email Security message to download | string |  `fireeyeetp message id` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.etp\_message\_id | string |  `fireeyeetp message id` 
action\_result\.data | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'download pcap'
Downloads all the PCAP files of the alert for a specified alert ID and add the files to the vault

Type: **investigate**  
Read only: **True**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**alert\_id** |  required  | The ID of the Email Security message to download | string |  `fireeyeetp alert id` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.alert\_id | string |  `fireeyeetp alert id` 
action\_result\.data | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'download malware files'
Download all malware files of the alert for a specified alert ID and add the files to the vault

Type: **investigate**  
Read only: **True**

<p>These files can contain viruses or other malicious software\. Be cautious when opening these files\!</p>

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**alert\_id** |  required  | The ID of the Email Security message to download | string |  `fireeyeetp alert id` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.alert\_id | string |  `fireeyeetp alert id` 
action\_result\.data | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'download case files'
Download all case files of the alert for a specified alert ID and add the files to the vault

Type: **investigate**  
Read only: **True**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**alert\_id** |  required  | The ID of the Email Security message to download | string |  `fireeyeetp alert id` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.alert\_id | string |  `fireeyeetp alert id` 
action\_result\.data | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'remediate emails'
Enqueues the message IDs provided in the request for remediation from the user's Office365 mailbox

Type: **contain**  
Read only: **False**

<p><b>Note\: There is no <i>undo</i> functionality with this action\!</b></p><p>The permissions the API account needs to have to use this function are, <b>etp\.email\_trace\.update</b> and <b>etp\.email\_trace\.delete</b>\. If the API user does not have these permissions you will not be able to use this action\.</p><p></p><p>Any ETP Messages that have a status from the list given below are ignored and no action will be taken on them\. <p><ul><li>Deleted</li><li>Dropped</li><li>Dropped\(OOB\)</li><li>Dropped \(OOB Retroactive\)</li><li>Rejected</li><li>Split</li><li>Permanent Failure</li></ul></p></p><p>The action\_override parameter allows you to override the default action for remediating an email\. It is important to note that if you select Delete, that it is a permanent delete in Office365\.</p><p>The move\_to parameter allows you to move the identified emails to a specific folder\. If the folder is not in the user's mailbox, a new custom folder will be created and then the email will be moved into the new folder\. Common Office365 folders are <p><ul><li>junk email</li><li>junkemail</li><li>deleted items</li><li>deleteditems</li></ul> These folder names are not case sensitive\.</p>

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**etp\_message\_ids** |  required  | A comma\-separated list of ETP Message IDs | string |  `fireeyeetp message id` 
**action\_override** |  optional  | Allows you to override the default action | string | 
**move\_to** |  optional  | When 'move' is chosen for action\_override, this parameter is mandatory and allows you to specify the folder to move the email to | string | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.etp\_message\_ids | string |  `fireeyeetp message id` 
action\_result\.parameter\.action\_override | string | 
action\_result\.parameter\.move\_to | string | 
action\_result\.data\.\*\.data\.successful | string | 
action\_result\.data\.\*\.data\.failed | string | 
action\_result\.data\.\*\.data\.failure\_reasons\.\*\.reason | string | 
action\_result\.data\.\*\.data\.failure\_reasons\.\*\.message\_ids | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'get quarantined email'
Download the email file present in the quarantine for the given Email Security message ID and add it to the vault

Type: **investigate**  
Read only: **True**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**etp\_message\_id** |  required  | The ID of the Email Security message to download | string |  `fireeyeetp message id` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.etp\_message\_id | string |  `fireeyeetp message id` 
action\_result\.data | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'unquarantine email'
Release the email file\(s\) present in the Quarantine within ETP

Type: **correct**  
Read only: **False**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**etp\_message\_id** |  required  | The ID of the Email Security message to unquarantine\. Allows for a comma\-separated string of IDs | string |  `fireeyeetp message id` 
**is\_not\_spam** |  optional  | Report as not spam to the Spam Engines | boolean | 
**headers\_only** |  optional  | Share only the email headers with Spam Engines | boolean | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.etp\_message\_id | string |  `fireeyeetp message id` 
action\_result\.parameter\.is\_not\_spam | boolean | 
action\_result\.parameter\.headers\_only | boolean | 
action\_result\.data\.\*\.response | string | 
action\_result\.data\.\*\.message | string | 
action\_result\.data | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'delete quarantined email'
Delete the email file\(s\) present in quarantine for the given Email Security message ID

Type: **correct**  
Read only: **False**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**etp\_message\_id** |  required  | The ID of the Email Security message to delete\. Allows a comma\-separated string of IDs | string |  `fireeyeetp message id` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.etp\_message\_id | string |  `fireeyeetp message id` 
action\_result\.data\.\*\.data\.\*\.deleted | boolean | 
action\_result\.data\.\*\.data\.\*\.failed\_message\_ids | string | 
action\_result\.data\.\*\.data\.\*\.operation | string | 
action\_result\.data\.\*\.data\.\*\.successful\_message\_ids | string | 
action\_result\.data\.\*\.data\.\*\.type | string | 
action\_result\.data | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'list quarantined emails'
Get a list of quarantined emails from a given query filter

Type: **investigate**  
Read only: **True**

<p>When specifying a date in either the to or from dates, the date is assumed to be in UTC\. The ETP server runs on UTC so if you are not converting the timestamp to that time the results will be off\.</p><p>If the 'size' parameter value is greater than the mentioned range\(1\-200\), then the max value of range\(i\.e\: 200\) will be in consideration\.</p>

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**from\_date** |  required  | Start date to use to search the by\. Date in YYYY\-MM\-DDTHH\:MM\:SS\.SSSSSS in UTC | string | 
**to\_date** |  optional  | End date to use to search by\. Date in YYYY\-MM\-DDTHH\:MM\:SS\.SSSSSS in UTC | string | 
**domains** |  optional  | Specific domain\(s\) to search by\. Comma\-separated list allowed | string |  `domain` 
**email\_server** |  optional  | Specific email server to search by | string |  `ip` 
**from** |  optional  | Email address of the sender to search by | string |  `email` 
**reason** |  optional  | Reason the email was quarantined\. The strings must be of the following reasons, Action, Advanced Threat, Policy, Spam or Virus\. Comma\-separated list allowed | string | 
**recipients** |  optional  | Recipient\(s\) to search by\. Comma\-separated list allowed | string |  `email` 
**sender\_domain** |  optional  | The senders domain to search by | string |  `domain` 
**subject** |  optional  | The subject of the email to search by | string | 
**size** |  required  | The number of alerts to include in response\. Valid range\: 1\-200 \(ETP Defaults to 20\) | numeric | 
**tags** |  optional  | Tag associated with email\. Must be of the follow tags, auto\_remediation, impersonation, or manual\_remediation\. Comma\-separated list allowed | string | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.from\_date | string | 
action\_result\.parameter\.to\_date | string | 
action\_result\.parameter\.domains | string |  `domain` 
action\_result\.parameter\.email\_server | string |  `ip` 
action\_result\.parameter\.from | string |  `email` 
action\_result\.parameter\.reason | string | 
action\_result\.parameter\.recipients | string |  `email` 
action\_result\.parameter\.sender\_domain | string |  `domain` 
action\_result\.parameter\.subject | string | 
action\_result\.parameter\.size | numeric | 
action\_result\.parameter\.tags | string | 
action\_result\.data\.\*\.data\.\*\.attachments | string | 
action\_result\.data\.\*\.data\.\*\.cc | string |  `email` 
action\_result\.data\.\*\.data\.\*\.country\_code | string | 
action\_result\.data\.\*\.data\.\*\.domain | string | 
action\_result\.data\.\*\.data\.\*\.from | string |  `email` 
action\_result\.data\.\*\.data\.\*\.message\_id | string |  `fireeyeetp message id` 
action\_result\.data\.\*\.data\.\*\.recipients | string |  `email` 
action\_result\.data\.\*\.data\.\*\.released\.\*\.email\_id | string | 
action\_result\.data\.\*\.data\.\*\.released\.\*\.is\_released | numeric | 
action\_result\.data\.\*\.data\.\*\.released\.\*\.key | string | 
action\_result\.data\.\*\.data\.\*\.sender\_domain | string | 
action\_result\.data\.\*\.data\.\*\.sender\_ip | string |  `ip` 
action\_result\.data\.\*\.data\.\*\.subject | string | 
action\_result\.data\.\*\.data\.\*\.timestamp\_quarantine | string | 
action\_result\.data\.\*\.data\.\*\.timestamp\_sent | string | 
action\_result\.data\.\*\.data\.\*\.to | string |  `email` 
action\_result\.data\.\*\.data\.\*\.verdict\_as | string | 
action\_result\.data\.\*\.data\.\*\.verdict\_av | string | 
action\_result\.data\.\*\.data\.\*\.verdict\_ex | string | 
action\_result\.data\.\*\.data\.\*\.verdict\_pv | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric | 