> # *Sense Excel Reporting Installation Process*


**TABLE OF CONTENTS**

1. PREPARE SERVER

2. PREPARE INSTALLATION FILES

3. INSTALL CONNECTOR

4. INSTALL EXTENSION

5. EDIT CONNECTOR CONFIGURATION FILE

6. ADD LICENSE INFORMATION

7. ADD ANALYTIC CONNECTION

8. ADD VIRTUAL PROXY

9. ADD SECURITY RULES 

10. VALIDATE CONNECTOR IS RUNNING

11. SET THE SER CONNECTOR TO RUN AS A SERVICE




##  1. PREPARE SERVER

 1.  Confirm Qlik Sense Server version. April 2019 or later is required.

 2.  Download and install NET Core Runtime 2.2.5 or newer prior to this installation. Click here to [Download](https://dotnet.microsoft.com/download)
 
 3. Create the following sub-directories within your Qlik Share directory.
 
\Reporting

\Reporting\SERReports

## 2. PREPARE INSTALLATION FILES

 1. Unzip Sense Excel Reporting.zip to the \Reporting directory
 
 ![Install Files](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Files-Install.PNG)
 

## 3. INSTALL CONNECTOR

1. Execute InstallConnector.bat from the \Reporting directory.  

 ![Install Connector](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Connector-Console.PNG)

2. Security keys will be created in the %appdata%\Roaming\senseexcel\reporting directory.


## 4. INSTALL EXTENSIONS

Install the following extensions on the Qlik Sense server.

QMC> MANAGE RESOURCES > EXTENSIONS +  Import + Choose File

\Reporting\Extension\ser-ext-ondemand-x.x.x.zip

\Reporting\Extension\ser-web-management.x.x.x.zip

![Install Extension](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Extension-Install.PNG)


##  5. EDIT CONNECTOR CONFIGURATION FILE

In a default installation, this file will not need to be changed. 

In limited cases there may need to be changes to parameters such as directory paths or server identification parameters. For additional information consult the example file \Reporting\Connector\config.hjson.example.

If changes to this configuration are necessary, perform the following steps.

1. Open the following file using Notepad or other text editor:

\Reporting\Connector\config.hjson.

Creating a temporary directory for the report process
 workingDir: %temp%\senseexelreporting

Host binding on gRPC Server
 bindingHost: localhost

Port binding on gRPC Server
 bindingPort: 50059

Connection to rest service
 restServiceUrl: http://localhost:40263

Connection settings to Qlik

By default the connectors takes https://PCNAME/ser as serverUri.  This is the location that the Qlik Sense Server certificate is pointed to.  If you need to use another value such as the fully qualified domain name, add an addtional line and leave the new entry uncommented like the example below:
 
 '# serverUri: https://localhost/ser
  
  serverUri: https://qliksense.customer.com/ser

Qlik JWT Connection Info

The defined HTTP header for the virtual proxy

key: X-Qlik-Session-ser

The authentication mode of the proxy. Standard configuration uses JWT, only change this setting if you need to use other authentication method.

type: SESSION

The paremeters below point to the the JWT certificate public and private keys. You can define relative paths or absolute paths

Realtive: cert: %appdata%\senseexcel\reporting\serconnector.pem

Absolute: cert: c:\users\qliksenseuser\senseexcel\reporting\serconnector.pem

Relative privateKey: %appdata%\senseexcel\reporting\serconnector_private.key

Absolute privateKey: c:\users\qliksenseuser\senseexcel\reporting\serconnector_private.key


4. If you have made any changes to these parameters, save and close config.hjson file.

5. Restart SER Connector.


## 6. ADD LICENSE INFORMATION

There are two steps to this process.  If you have already installed a senseexcel content library and associated license.txt file for use with Sense Excel, this process does not need to be repeated unless you need to update your license key. 

1. Create a license.txt file. Choose an approach from below based on your organization's Qlik Sense licensing strategy.

2. Create a content library on the QMC and upload the license.txt file

### Token Licensing 

 1. Open a new text file.  
 
 2. Copy and paste your LEF information.
 
 3. Delete any spaces at end of each line.
 
 4. Save file with name license.txt.

### Named Licensing

 1. Open a new text file.  
 
 2. Copy and paste your LEF information.
 
 3. Append the LEF information with the DOMAIN\user information for each user. See the example below.
 
  ![License Example](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/License-Example.PNG).
  
 4. The FROM:TO info is optional. This will put a start and end date on your specified users. Use the format YYYY-MM-DD.
 
 5. Delete any spaces at end of each line.
 
 6. Save file with name license.txt.
 
### Add License Content Library and Upload License Information

A Content Library needs to be added to the Qlik Sense server for license management services.

1. QMC > Content Libraries >  +Create New > senseexcel

![Content Library](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Content-Library-Add.PNG)

2. When prompted for security rule creation, add the entry User Name Like "*" to make this content library avaiable to all users or include values to specify the users and/or groups that will need access in order to use Sense Excel or Sense Excel Reporting.  

3. Check the Advanced Property on the right side of the screen.
 
In the Conditions Box add the value below
 
!user.IsAnonymous

![Content Library Advanced](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Content-Library-Advanced.PNG)

3. Hit the Apply Button

4. Check the Contents Property under Associated Items

5. Upload the license.txt file.
 
 
## 7. ADD ANALYTIC CONNECTION
 
This process creates a new Analytic Connection in the QMC to allow the Sense Excel Reporting processes to bind with the Qlik Sense Engine.

Define the name, Host, Port, Reconnect Timeout and Reg Timeout parameters.  The name value IS CASE SENSITIVE and must be exactly SER.

QMC > MANAGE CONTENT > Analytic Connections > + Create New

|Setting|Value         |
|-------|--------------|
|Name   | SER          |
|Host   | localhost    |
|Port   | 50059        |
|Reconnect Timeout | 30|
|Request Timeout   | 30|

 ![Analytic Connection](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Analytic-Connection.PNG)


## 8. ADD VIRTUAL PROXY

In this section you will define a new Virtual Proxy and then link it to the Main Proxy.  This process is done to allow user and session information to be securely communicated between the Sense Excel Reporting Engine and the Qlik Sense Server.


### Add

Create the Virtual Proxy via the QMC’s Virtual Proxy menu first. To ensure that the links will work properly DO NOT utilize the Create New Virtual Proxy technique within the QMC Proxy Menu. 

QMC > CONFIGURE SYSTEM > Virtual proxies > + Create new

Check the following Properties on the right side of your screen.

Identification

Authentication

Load Balancing

Advanced

#### IDENTIFICATION

|Setting|Value|
|--|--|
|Description  |ser |
|Prefix|ser |
|Session inactivity timeout | 30 |
|Session cookie header name | X-Qlik-Session-ser|

Open the file %appdata%/senseexcel/reporting/serconnector.pem in a text editor.

Copy the full contents and paste them into the JWT certificate area shown below.

#### AUTHENTICATION

|Setting|Value|
|--|--|
|Anonymous access mode | No anonymous user   |
|Authentication method | JWT|
JWT certificate | -----BEGIN CERTIFICATE----- |
|JWT attribute for user ID | UserId|
|JWT attribute value for user directory | UserDirectory|


![Virtual Proxy](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Virtual-Proxy-1.PNG)


#### LOAD BALANCING

Load Balancing nodes > Server node > +Add New Server node > Central

#### ADVANCED

Host white list > Add New Value > YOUR SERVER'S MACHINE NAME

Host white list > Add New Value > YOUR SERVER'S INTERNAL FULLY QUALIFIED DOMAIN NAME OR IPADDRESS

Host white list > Add New Value > YOUR SERVER'S EXTERNAL FULLY QUALIFIED DOMAIN NAME OR IPADDRESS


### LINK PROXIES
This step is performed last to ensure that the link between the Central Proxy and the newly created ser Virtual proxy will work properly.

QMC > Proxies > Virual Proxies, Central (Default)  > Associate Items > Virtual Proxies > SER > +Add > Link Existing > ser

![Virtual Proxy](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Virtual-Proxy-2.PNG)


## 9. ADD SECURITY RULES

These security rules are defined to allow the Sense Excel processes to interact properly with the Qlik Sense engine.  All parameters are CASE SENSITIVE.

### Shared Content

QMC > MANAGE RESOURCES > Security Rules > + Create new

#### IDENTIFICATION

|Setting      |Value           |
|-------------|----------------|
| Name        | _sharedcontent |
| Description |                |

#### BASIC

|Setting         |Value     |   |
|----------------|----------|---|
|Resource Filter | SharedContent_* |   |
|Actions         | Read, Update, Change Owner |   |
|User            | name      | =  |
|Value           | YOUR QLIK USER NAME |   |

#### ADVANCED

|Setting    |Value             |
|-----------|------------------|
|Conditions | ((user.name="YOUR QLIK USER NAME")) |
|Context    | Both in hub and QMC   |

Validate Rule > Add Rule

![Shared Content Security Rule](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Security-Rule-Shared-Content.PNG)


### SER License

QMC > MANAGE RESOURCES > Security Rules > + Create new > SER License

#### IDENTIFICATION
|Setting |Value
|------------------|------------------|
|Name | SER License |
|Create Rule from Template | Unspecified |
|Disabled | Leave Unchecked |
|Description | |

#### BASIC
|Setting |Value|
|---|---|
|Resource Filter | License_* |
|Actions | Read |

#### ADVANCED
Add the below value manually into the Conditions table:

|Setting    |Value             |
|-----------|------------------|
|Conditions | !user.IsAnononymous() |
|Context    | Both in hub and QMC   |

Validate Rule > Add Rule

![SER License Security Rule](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Security-Rule-SER-License.PNG)

### SER Scheduler

QMC > MANAGE RESOURCES > Security Rules > + Create new > SER Scheduler

#### IDENTIFICATION

| Setting |Value
|------------------|------------------|
| Disabled | Leave Unchecked |
| Name | SER Scheduler |
| Create Rule From Template | App Access|
| Description | |

#### BASIC

|Setting           | Value            |
|------------------|------------------|
| Resource Filter  | App_*            |
| Actions          | Read             |
| User             | name             |
| Value            | ser_scheduler    |
| AND              |                  |
| user             | userDirectory  = |
| value            | INTERNAL         |

#### ADVANCED

| Setting   |Value           |
|-----------|--------------- |
|Conditions | ((user.name="ser_scheduler" and user.userDirectory="INTERNAL"))|

Validate Rule > Add Rule


![SER Scheduler Security Rule](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Security-Rule-SER-Scheduler.PNG)

## 10. VALIDATE CONNECTOR IS RUNNING

1. Execute the TestConnectorService.bat batch file from the \Reporting directory as Administrator.

2. If the connector is running successfully, the console window will display the following text at around the 20th line from the top in the console window.

The Ser.ConAai.SSEtoSER service is now running, press Control+C to exit.

![Install Connector](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Connector-Console.PNG)

3.  Once you have confirmed that the Connector is running successfully follow section 11 below to set the connector to run as a service.


## 11. SET THE SER CONNECTOR TO RUN AS A SERVICE


1. Navigate to the \Reporting\Connector directory

![Connector Service Install](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Connector-Service-Install.PNG)

2. Run InstallConnectorService.bat as Administrator.  Upon successful installation you will see the message below in the command window.

![SER Connector as a Service](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Connector-As-Service.PNG)

4. Open Services.msc.  Select the "Qlik Connector for SER" service, right mouse cliek to access Properties and choose the Log On tab.

5. Change the owner to "This Account" and enter the creentials and password of the account running the other Qlik Sense services.

![SER Service Update Credentials](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Connector-Service-Credentials.PNG)

6. Restart the service.  Make sure that the credentials were entered correctly by confirming that the "Log On As" value matches the account running the other Qlik services and has not defaulted to the "Local System" account.
