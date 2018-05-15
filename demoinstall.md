> # INSTALL DEMO CONTENT

##  INSTALL DEMO APPS TO APP LIBRARY

From the C:\Reporting\Apps directory install the following files to the QMC App Library.

Executive Dashboard Connector.qvf
Executive Dashboard.qvf

QMC > MANAGE CONTENT > APPS > Import app > Choose FIle >Executive Dashboard Connector.qvf

QMC > MANAGE CONTENT > APPS > Import app > Choose FIle >Executive Dashboard.qvf

## CREATE CONTENT LIBRARY

QMC > MANAGE CONTENT > Content libraries > Create New

Identification : senseexcelreporting

Hit Apply Button

Check the Advanced Tab on the right side of the screen

In the Conditions box, add the value !userIsAnonymous()

Hit Apply Button


##  INSTALL DEMO REPORT TEMPLATE TO CONTENT LIBRARY


 1. Download ExecutiveDashboard.xlsx from the App Contents of the Executive Dashboard App in the QMC.

QMC > MANAGE CONTENT > Apps > Executive Dashboard > App contents (check) > ExecutiveDashboard.xlsx

2. Upload the ExecutiveDashboard.xlsx file to the senseexcelreports content library.

QMC > MANAGE CONTENT > Content libraries > senseexcelreports > Upload > Choose Files >ExecutiveDashboard.xlsx


##  ON DEMAND REPORTS DEMO

1. Open the Executive Dashboard App from the Hub

2. Choose the Accounts Receivable tab

3. Use the Edit Function to choose the parameters of the report you would like to run,

4. Click on the widget with the Generate Report Button

5. Open the Configuration Tab

6. Choose from the Library dropdown list where you would like to run the report from.  Choices are the senseexcelreports content library or InApp which will let you choose from templates that are attached to the app itself.

7. Choose the ExceutiveDashboard.xlsx from the Content dropdown list.

8. Choose the Output Format. Excel or PDF

9. Chose the Selection Mode.  Selection over Shared Session or Selection Over Book Mark. 

Shared session will display the report running in an animated fashion on the screen.  

Selection over Book Mark will perform report processing in the background.

 10.   Choose Direct Download On or Off

Direct Download On will open the report automatically in either your default PDF viewer or in Excel

Direct Download off will require the Download Report button to be pushed.

11. Hit the Done Key.

12. Choose the region you would like to have the report run for in the Region filter pane.  The associated segments will update based on that selection.

13. Hit the Generate Report Button to execute the report.

The demo app is set to generate a Title Page a Summary Page a seperate Detail page for Each Segment Selected.



##  SCHEDULED REPORTS DEMO

 1. Open the Executive Dashboard Connector App from the HUB.

 2. Press open on the This app contains no data dialogue box if it appears.  If it doesn't appear hit the Edit Script button.

 3. Confirm that the Data Connections Attached Files includes the ExecutiveDashboard.xlsx file.  If it doesn't download it from the senseexcelreports or the Executive Dashboard app as described earlier and attach it here.

 4.  Create a new data connection called SERReports.  Make the path C:\Reporting\SERReports.

 5. Open Data Connections section of the QMC.  Choose the SERReports connection and remove the appended user information from the connection name.   

 6. Save the connection.

 7.  Return to the Executive Dashboard Connector App. Press the Save button.  Confirm that the SERReports connection has the appended user information removed.

 8.   Change the following parameters in the script:

 To change the App that the job will run on.

**app:  Executive Dashboard AppID from QMC**  

To Choose the Template that the job will run on.  /// will use the associated content library.  // will use a discreet path.

**input: content:///ExecutiveDashboard.xlsx**

To define report output parameters (optional for Demo app)

output: ='Report_'&only(Region)&'_'&max([Fiscal Year])&'.pdf'
		
	selections:[
	{
		type: static
		objectType: field
		name: Fiscal Year
		values: =[Fiscal Year]=(Year(ToDay())-4)
	}
	{
		type: Dynamic
		name: Region
	}
	]
  }

To set the locations that the reports will be sent when processed:

Below will send the output to the file location referenced in the Data Connection and defined as a target path at the beginning of the install process.

**target: =lib://SERReports'**
	
Define the user for delivery via the Qlik Sense Hub

**Settings for upload in the Qlik hub**

**owner: Domain\User**

10.  Once all parameters are set, hit the Load Data button to execute the script and run the distribution job.
