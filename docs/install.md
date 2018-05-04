<img src="https://github.com/senseexcel/senseexcel-reporting/raw/master/docs/qmc-securityrule-license.PNG" width="250">

> # *Sense Excel Reporting Installation Process*

##  SERVER PREPARATION

### FILES

If you do not have the .NET Core Runtime installed please do so prior to this installation.
Click the link to download the appropriate version from Microsoft. [Download](https:/www.microsoft.com/net/download/Windows/run)

Create directory c:\qlikshare or other share directory on Qlik Sense server as preferred

Copy/unzip the following files to the share directory

"C:\QlikShare\Apps"
"C:\QlikShare\ArchivedLogs"
"C:\QlikShare\CustomData"
"C:\QlikShare\Reporting"
"C:\QlikShare\StaticContent"

### CREATION OF PUBLIC AND PRIVATE KEY
Run Connector once manually from  \share\reporting\runconnector.bat

Keys will be located in \share\reporting\connector directory

Open public key serconnector.pem file with notepad or other text editor.  

Leave it open for use later in the process

## ADD VIRTUAL PROXY
In this section you will define a new Virtual Proxy and then link it to the Main Proxy.  This operation MUST be done in a particular order. 

Create the Virtual Proxy via the QMC’s Virtual Proxy menu first. To ensure that the links will work properly DO NOT utilize the Create New Virtual Proxy technique within the QMC Proxy Menu. 

QMC > CONFIGURE SYSTEM > Virtual proxies > + Create new

Once the new virtual proxy is created, confirm the following Properties are checked in the Dialogue Box on the right side of your screen.

Identification
Authentication
Load Balancing

### IDENTIFICATION
Define the Description as ser
Define the Prefix as ser
Leave the session inactivity period at the default 30 minutes
Append the Session cookie header name with -ser

|Setting|Value|
|--|--|
|Description  |ser
|Prefix| ser 
Session inactivity timeout | 30
Session cookie header name | X-Qlik-Session-ser|


### AUTHENTICATION
Leave Anonymous access mode as default "No anonymous user"
Change Authentication method from Ticket to "JWT"
Copy key information from text editor and paste into the JWT Certificate dialogue box
Add JWT attribute for user ID value of "UserID"
Add JWT attribute value for user directory of "UserDirectory "

|Setting|Value|
|--|--|
|Anonymous access mode | No anonymous user   |
|Authentication method | JWT|
JWT certificate | -----BEGIN CERTIFICATE-----|
|JWT attribute for user ID | UserID|
|JWT attribute value for user directory | UserDirectory


### LOAD BALANCING

Load Balancing nodes > Server node > +Add New Server node > Central

### IDENTIFICATION
|Setting|Value|
|--|--|
|Description  |ser
|Prefix| ser 
Session inactivity timeout | 30
Session cookie header name | X-Qlik-Session-ser|

### LINK PROXIES
This process is done last as earlier mentioned to ensure that the link between the existing Central proxy and newly created Virtual proxy will work properly.

QMC > Proxies > Virual Proxies, Central (Default)  > Associate Items > Virtual Proxies > SER > +Add > Link Existing > ser

## ANALYTIC CONNECTION CREATION
Define the name, Host, Port, Reconnect Timeout and Reg Timeout parameters

QMC > MANAGE CONTENT > Analytic Connections > + Create New

|Setting|Value|
------|--------------
|Name | ser
|Host | localhost
|Port | 50059|
|Reconnect Timeout|30
|Request Timeout|30|

## SECURITY RULE CREATION

### CREATE SER LICENSE RULE

QMC > MANAGE RESOURCES > Security Rules > + Create new

### IDENTIFICATION

|Setting |Value
------------------|------------------
|Name | SER License|
|Create Rule from Template | Unspecified|
|Disabled | Leave Unchecked|
|Description ||

### BASIC
|Setting |Value
------------------|------------------
|Resource Filter | License_* |
|Actions | Read|


### ADVANCED
|Setting |Value
------------------|------------------
|Conditions | !user.IsAnononymous()|
|Context | Both in hub and QMC|

Validate Rule > Add Rule


## CREATE SER SCHEDULER RULE

QMC > MANAGE RESOURCES > Security Rules > + Create new

#### IDENTIFICATION

Setting |Value
------------------|------------------
Disabled | Leave Unchecked |
Name | SER Scheduler|
Create Rule From Template | App Access|
Description ||

### BASIC

Setting |Value
------------------|-------------
|Resource Filter | App_*  |
|Actions | Read |
|User |name      = |
|Value |ser_scheduler |
| OR||
|user | userDirectory      = |
|value | INTERNAL|

Conditions field in the ADVANCED section below is automatically populated by the above selections 

### ADVANCED
Setting |Value
----------|---------------
|Conditions | ((user.name="ser_scheduler" or user.userDirectory="INTERNAL"))|

Validate Rule > Add Rule

## INSTALL EXTENSION

QMC > MANAGE RESOURCES > Extensions > +Import > ser-ext-ondemand.zip
