> # *Sense Excel Reporting Installation Process*

##  PREPARE SERVER

 1.  Confirm Qlik Sense Server version - November 2017 or later

 2.  Please install the NET Core Runtime prior to this installation. Click on the link to download [Download](https:/www.microsoft.com/net/download/Windows/run)
 
 3. Create the following sub-directories withing your QlikShare directory.
 
\Reporting

\Reporting\SERReports

## PREPARE INSTALLATION FILES

 1. Place the following installation files on your desktop.

ser-ext ondemand.zip

serConnector.zip 

 2. Unzip serConnector.zip to the \Reporting directory
 
 
 ![Install Files](https://github.com/senseexcel/senseexcel-reporting/edit/docs-edit/docs/Install-Files.PNG)
 

## INSTALL CONNECTOR

1. Execute RunConnector.bat from the \Reporting directory

 ![Install Connector](https://github.com/senseexcel/senseexcel-reporting/docs/blob/master/Connector-Console.PNG)

2. Security keys will be created in  %appdata%\Roaming\senseexcel\reporting.

3. Restart Qlik Repository Services and all sub-processes.
 

## INSTALL EXTENSION

Install the ser-ext-ondemand extension on the Qlik Sense server.

QMC> MANAGE RESOURCES > EXTENSIONS +  Import + Choose File

\Desktop\ser-ext-ondemand.zip

![Install Extension](https://github.com/senseexcel/senseexcel-reporting/blob/master/Extension-Install.PNG)

##  UPDATE REPORTING CONFIGURATION FILE

In a typical installation, this file will not need to be changed. For additional information consult the example file \Reporting\Connector\config.hjson.example.

If changes to this configuration are necessary, perform the following steps.

 1. Open the following file using Notepad or other text editor:
C:\Reporting\Connector\config.hjson

 2. Update the serEnginePath parameter:
serEnginePath: C:\Reporting\Engine\SenseExcelReporting.exe

 3. Update bindingHost parmeter:
bindingHost: localhost
  
 4. Update  bindingPort parameter
bindingPort: 50059

 5. Update the default the connectors takes parameters to your local server name:
 (https://LOCALSERVERNAME/ser) as serverUri
 
 6. Update the defined HTTP header for the virtual proxy
		 key parameter: X-Qlik-Session-ser
		 
 7. Save and close config.hjson.

## ADD CONTENT LIBRARY

The following content library needs to be added to the Qlik Sense server for license management services.

senseexcel

QMC > Content Libraries >  +Create New > senseexcel

![Add Content Library](https://github.com/senseexcel/senseexcel-reporting/docs/blob/master/Content-Library-Add.PNG)


## ADD LICENSE INFORMATION

Depending on your Qlik Sense licensing strategy, choose one of the following approaches. 

### Token Licensing 

 1. Open a new text file.  
 2. Copy and paste your LEF information
 3. Delete any spaces at end of each line.
 4. Save file with name license.txt.
 5. Upload the license.txt file to the senseexcel content library.
 
QMC > MANAGE CONTENT > Content Libraries > senseexcel > Upload > license.txt

![Content Library Contents](https://github.com/senseexcel/senseexcel-reporting/docs/master/Conent-Library-Contents.PNG)

 6. Check the Advanced Property on the right side of the screen
 7. Add the value !user.IsAnonymous in the Conditions box.
 
 ![Content Library Advanced](https://github.com/senseexcel/senseexcel-reporting/docs/blob/master/Conent-Library-Advanced.PNG)
 
  8. Hit the Apply button.
 

### Named Licensing

 1. Open a new text file.  
 2. Copy and paste your LEF information
 3. Delete any spaces at end of each line.
 4. Append the LEF information with user information as in the example below.  
 5. The  FROM:TO info is optional. This will put a start and end date on your specified users. Use the format YYYY-MM-DD
 6. Save file with name license.txt. 
 7. Upload the license.txt file to the senseexcel content library.
 
QMC > MANAGE CONTENT > Content Libraries > senseexcel > Upload > license.txt

 ![License Example](https://github.com/senseexcel/senseexcel-reporting/docs/blob/master/License-Example.PNG)

 8. Check the Advanced Property on the right side of the screen
 
 9. Add the value !user.IsAnonymous in the Conditions box.
 
  ![Content Library Advanced](https://github.com/senseexcel/senseexcel-reporting/docs/blob/master/Conent-Library-Advanced.PNG)
 
 10. Hit the Apply button.

##  ADD ANALYTIC CONNECTION
 
Define the name, Host, Port, Reconnect Timeout and Reg Timeout parameters

QMC > MANAGE CONTENT > Analytic Connections > + Create New

|Setting|Value|
------|--------------
|Name | ser
|Host | localhost
|Port | 50059|
|Reconnect Timeout|30
|Request Timeout|30|

 ![Analytic Connection](https://github.com/senseexcel/senseexcel-reporting/docs/blob/master/Analytic-Connection.PNG)


##  ADD VIRTUAL PROXY
In this section you will define a new Virtual Proxy and then link it to the Main Proxy.  This operation MUST be done in a particular order. 

Create the Virtual Proxy via the QMC’s Virtual Proxy menu first. To ensure that the links will work properly DO NOT utilize the Create New Virtual Proxy technique within the QMC Proxy Menu. 

QMC > CONFIGURE SYSTEM > Virtual proxies > + Create new

Check the following Properties on the right side of your screen.

Identification

Authentication

Load Balancing

Advanced

### IDENTIFICATION

|Setting|Value|
|--|--|
|Description  |ser
|Prefix| ser 
Session inactivity timeout | 30
Session cookie header name | X-Qlik-Session-ser|

Open the file %appdata%/senseexcel/reporting/serconnector.pem in a text editor.

Copy the full contents and paste them into the JWT certificate area shown below.

### AUTHENTICATION

|Setting|Value|
|--|--|
|Anonymous access mode | No anonymous user   |
|Authentication method | JWT|
JWT certificate | -----BEGIN CERTIFICATE-----|
|JWT attribute for user ID | UserID|
|JWT attribute value for user directory | UserDirectory


![Virtual Proxy](https://github.com/senseexcel/senseexcel-reporting/docs/blob/master/Analytic-Connection.PNG)


### LOAD BALANCING

Load Balancing nodes > Server node > +Add New Server node > Central

### ADVANCED
Host white list > Add New Value > YOUR SERVER NAME


### LINK PROXIES
As mnentioned earlier, this step is performed last to ensure that the link between the Central proxy and newly created Virtual proxy will work properly.

QMC > Proxies > Virual Proxies, Central (Default)  > Associate Items > Virtual Proxies > SER > +Add > Link Existing > ser

## CREATE SECURITY RULES

### LicenseReadAll

QMC > MANAGE RESOURCES > Security Rules > + Create new

### IDENTIFICATION

|Setting | Value |
|-------------------------|
|Name |LicenseReadAll  |
|Create Rule from Template | Unspecified|
|Disabled | Leave Unchecked||
|Description ||

### BASIC

|Setting | Value |
|-------------------------|
|Resource filter |License_*|
|Actions|Read|

### ADVANCED

Conditions		!user.IsAnonymous()

Context			Both in hub and QMC		


 ![License ReadAll Security Rule](https://github.com/senseexcel/senseexcel-reporting/blob/master/License-Read-All-Security-Rule.PNG)

### Shared Content

QMC > MANAGE RESOURCES > Security Rules > + Create new

### IDENTIFICATION

|Setting | Value |
|-------------------------|
|Name | _sharedContent* |
|Create Rule from Template | Unspecified|
|Disabled | Leave Unchecked||
|Description ||

### BASIC

|Setting         |Value     |   |
|----------------|----------|---|
|Resource Filter |SharedContent_* |   |
|Actions         |Read, Update, Change Owner     |   |
|User            |name      |=  |
|Value           |YOUR QLIK USER NAME |   |

### ADVANCED

Conditions		((user.name="YOUR QLIK USER NAME"))
Context			Both in hub and QMC			

Validate Rule > Add Rule

![Shared Content Security Rule](https://github.com/senseexcel/senseexcel-reporting/blob/master/Shared-Content-Security-Rule.PNG)


### SER License

QMC > MANAGE RESOURCES > Security Rules > + Create new > SER License

### IDENTIFICATION
|Setting |Value
------------------|------------------
|Name | SER License|
|Create Rule from Template | Unspecified|
|Disabled | Leave Unchecked|
|Description ||

### BASIC
|Setting |Value|
---|---|
|Resource Filter | License_*
|Actions | Read |

### ADVANCED
Add the below value manually into the Conditions table:

|Setting |Value
------------------|------------------
|Conditions |!user.IsAnononymous()
|Context |Both in hub and QMC|

Validate Rule > Add Rule

![SER License Security Rule]( https://github.com/senseexcel/senseexcel-reporting/docs/blob/master/Security-Rule-SER-License.PNG)

### SER Scheduler

QMC > MANAGE RESOURCES > Security Rules > + Create new > SER Scheduler

### IDENTIFICATION

Setting |Value
------------------|------------------
Disabled | Leave Unchecked |
Name | SER Scheduler|
Create Rule From Template | App Access|
Description ||

### BASIC

Setting |Value
------------------|------------------
Resource Filter | App_*
Actions | Check Read|
|User |name | =
|Value |ser_scheduler|
| AND ||
|user | userDirectory  = |
|value | INTERNAL|

### *ADVANCED*
Setting |Value
----------|---------------
|Conditions | ((user.name="ser_scheduler" and user.userDirectory="INTERNAL"))|

Validate Rule > Add Rule


![SER Scheduler Security Rule]( https://github.com/senseexcel/senseexcel-reporting/docs/blob/master/Security-Rule-SER-Scheduler.PNG)

## VALIDATE CONNECTOR IS RUNNING

1. Execute the RunConnector.bat batch file from the \Reporting directory. Do not run as Administrator.

2. If the connector is running successfully, the console window will display the following text on the last line.

The Ser.ConAai.SSEtoSER service is now running, press Control+C to exit.

![Install Connector](https://github.com/senseexcel/senseexcel-reporting/docs/blob/master/Connector-Console.PNG)

4. Go to services.msc and restart the Qlik Repository Database Service. This will prompt for a restart of every Qlik service.

## SET THE SER CONNECTOR TO RUN AS A SERVICE

1. Open a command prompt.

2. Navigate to the C:/Reporting/Connector directory

3. Execute the following command  dotnet SerConAai.dll action:install

![SER Connector as a Service](https://github.com/senseexcel/senseexcel-reporting/docs/blob/master/Connector-As-Service.PNG)


4. Open Services.msc

5. Change the owner of the “Qlik Connector for SER” to the This User account.

![SER Service Update Credentials](https://github.com/senseexcel/senseexcel-reporting/docs/blob/master/Connector-Service-Credentials.PNG)

6. Restart the service.

