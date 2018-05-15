> # *Sense Excel Reporting and Demo Apps Installation Process*

##  *SERVER PREPARATION*

 1.  Confirm Qlik Sense Server version - November 2017 or later

 2.  Confirm .NET Installation. 

Please install the  .NET Core Runtime prior to this installation. Click the link to download the appropriate version from Microsoft. [Download](https:/www.microsoft.com/net/download/Windows/run)
 
 3. Create Target Sub-directories in your existing QlikShare directory.
 
C:\QlikShare\Reporting

C:\QlikShare\Reporting\SERReports

## *FILE PREPARATION*

 1. Copy/Download Install Files to Desktop

ser-ext ondemand.zip

serConnector.zip 

 2. Unzip the SerConnnector.zip to the Desktop

 3. Copy/unzip the following files to the C:\QlikShare\Reporting directory

Apps

ArchivedLogs

CustomData

Reporting

StaticContent

RunConnector.bat

## *RUN CONNECTOR*

1.  Execute RunConnector.bat as Administrator from the \Reporting directory

Security keys will be created in  %appdata%\Roaming\senseexcel\reporting

 2. Open Services.msc
 
 3. Restart Qlik Repository Services and all sub-processes.
 

## *INSTALL EXTENSION*

Install the ser-ext-ondemand extension on the Qlik Sense 
server.

QMC> MANAGE RESOURCES > EXTENSIONS +  Import + Choose File

C:\Desktop\ser-ext-ondemand.zip

##  *UPDATE REPORTING CONFIGURATION FILE*

For a default installation this file will not need to be changed.  

For additional information consult the example file \Reporting\Connector\config.hjson.example

 1. Open the following file using Notepad or other text editor:
C:\Reporting\Connector\config.hjson

 2. Update the serEnginePath parameter:
serEnginePath: C:\Reporting\Engine\SenseExcelReporting.exe

 3. Update bindingHost parmeter:
bindingHost: localhost
  
 4. Update  bindingPort parameter
bindingPort: 50059

 5. Update the default the connectors takes parameters to your local server name:
 https://LOCALSERVERNAME/ser as serverUri

6.  Update the defined HTTP header for the virtual proxy
		 key parameter: X-Qlik-Session-ser
 7. Save and close config.hjson file.

## *ADD CONTENT LIBRARY*
Add the following content library to the Qlik Sense server:

senseexcel

QMC > Content Libraries >  +Create New > senseexcel


## *ADD LICENSE INFO*

### Token Licensing 

 1. Open a new text file.  
 2. Copy and paste your LEF information
 3. Delete any spaces at end of each line.
 4. Save file with name license.txt.
 5. Upload the license.txt file to the senseexcel content library.
 
QMC > MANAGE CONTENT > Content Libraries > senseexcel > Upload > license.txt

 6. Check the Advanced Property on the right side of the screen
 7. Add the value !user.IsAnonymous in the Conditions box.
 8. Hit the Apply button.
 

### Named Licensing

 1. Open a new text file.  
 2. Copy and paste your LEF information
 3. Delete any spaces at end of each line.
 4. Append the LEF information with user information as in the example below.  
 5. The  FROM:TO info is optional.  Only include it if you want to put time limits on your named user licenses.
 6. Save file with name license.txt. 
 7. Upload the license.txt file to the senseexcel content library.

QMC > MANAGE CONTENT > Content Libraries > senseexcel > Upload > license.txt

 8. Check the Advanced Property on the right side of the screen
 9. Add the value !user.IsAnonymous in the Conditions box.
 10. Hit the Apply button.

##  *ADD ANALYTIC CONNECTION*
 
Define the name, Host, Port, Reconnect Timeout and Reg Timeout parameters

QMC > MANAGE CONTENT > Analytic Connections > + Create New

|Setting|Value|
------|--------------
|Name | ser
|Host | localhost
|Port | 50059|
|Reconnect Timeout|30
|Request Timeout|30|

##  *ADD VIRTUAL PROXY*
In this section you will define a new Virtual Proxy and then link it to the Main Proxy.  This operation MUST be done in a particular order. 

Create the Virtual Proxy via the QMC’s Virtual Proxy menu first. To ensure that the links will work properly DO NOT utilize the Create New Virtual Proxy technique within the QMC Proxy Menu. 

QMC > CONFIGURE SYSTEM > Virtual proxies > + Create new

Once the new virtual proxy is created, confirm the following Properties are checked in the Dialogue Box on the right side of your screen.

Identification

Authentication

Load Balancing

Advanced

### *IDENTIFICATION SECTION*

|Setting|Value|
|--|--|
|Description  |ser
|Prefix| ser 
Session inactivity timeout | 30
Session cookie header name | X-Qlik-Session-ser|

Open the file %appdata%/senseexcel/reporting/serconnector.pem in a text editor.

Copy the full contents and paste them into the JWT certificate area shown below.

### *AUTHENTICATION*
|Setting|Value|
|--|--|
|Anonymous access mode | No anonymous user   |
|Authentication method | JWT|
JWT certificate | -----BEGIN CERTIFICATE-----|
|JWT attribute for user ID | UserID|
|JWT attribute value for user directory | UserDirectory


### LOAD BALANCING

Load Balancing nodes > Server node > +Add New Server node > Central

### ADVANCED
Host white list > Add New Value > YOUR SERVER NAME


### LINK PROXIES
This process is done last as earlier mentioned to ensure that the link between the existing Central proxy and newly created Virtual proxy will work properly.

QMC > Proxies > Virual Proxies, Central (Default)  > Associate Items > Virtual Proxies > SER > +Add > Link Existing > ser

## SECURITY RULES CREATION

### Add Shared Content Rule

QMC > MANAGE RESOURCES > Security Rules > + Create new

### IDENTIFICATION

|Setting | Value |
|------------------|-----------------|
|Name |_sharedContent*|
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

### Add SER License rule

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


### Add SER Scheduler Rule

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

Conditions field in the ADVANCED section below is automatically populated by the above selections 

### *ADVANCED*
Setting |Value
----------|---------------
|Conditions | ((user.name="ser_scheduler" or user.userDirectory="INTERNAL"))|

Validate Rule > Add Rule

