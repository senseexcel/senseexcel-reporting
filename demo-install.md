> # SENSE EXCEL REPORTING DEMO INSTALL

##  INSTALL DEMO APPS

Upload the following files to the QMC App Library from the existing \qlikshare\Reporting\Examples directory or Sense Excel Reportng.zip.

Reporting Task Demo.qvf
Executive Dashboard.qvf


1. QMC > MANAGE CONTENT > APPS > Import app > Choose FIle >Executive Dashboard.qvf

2. QMC > MANAGE CONTENT > APPS > Import app > Choose FIle >Reporting Task Demo.qvf

INSERT PICTURE HERE - Demo-Apps-Import.png


## CREATE CONTENT LIBRARY

1. QMC > MANAGE CONTENT > Content libraries > Create New

2. Identification : senseexcelreporting

3. ![QMC Button Apply](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/QMC-Button-Apply.PNG)

4. Check the Advanced Property on the right side of the screen

5. In the Conditions box, add the value !userIsAnonymous()

6. ![QMC Button Apply](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/QMC-Button-Apply.PNG)

![Demo Content Library](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Demo-Content-Library.PNG)

6. Check the Contents Property on the right side of the screen

7. Upload the ExecutiveDashboard.xlsx file from \Reporting\Examples directory.

INSERT PICTURE Demo-Content-Library-Upload.png

##  ON DEMAND REPORTS DEMO

1. Open the Executive Dashboard App from the Hub

2. Choose the Accounts Receivable sheet.

3. Press the Edit Button.

INSERT PICTURE Demo-Report-Edit

4. Click on the ser-On Demand Sense Report Custom Object that includes the Generate Report Button.

5. Open the Configuration Tab in the Propery Panel on the right side of the screen

6. Open the Library dropdown list and choose the source of your template. Default, SenseExcelReports (content library) or InApp.

7. Choose ExceutiveDashboard.xlsx from the Content dropdown list.

8. Choose the Output Format. "Excel" or "PDF"

9. Chose the Selection Mode.  "Selection over shared session" or "Selection over bookmark". 

10. Choose Direct Download On or Off. Selecting "On" will automatically download the output file in the specified format to the local machine as well as place it in the Reports section of the Hub.  Choosing "Off" will place the file in the Hub and allow the user to  download the file on demand by pressing the Download Report button in the Custom Object panel.

11. Hit the Done Key.

12. Use the Selections button, enter "Region" in the search dialgue box and select the values you would like included in your report.   The associated Segments will update based on that selection.

13. Hit the Generate Report Button to execute the report.

This application is configured to loop over the included Segment values based upon the app Selections made by the user. This will generate an output report including the following: a Title Page, Summary Dashboard and seperate Detail page for each Segment.


##  SCHEDULED REPORTS DEMO

 1. Open the Reporting Task Demo App from the HUB.

 2. Press "Open" when the "This app contains no data" dialogue box appears.
 
 3. Create a new data connection and name it SERReports.  Define the path as \Reporting\SERReports.

 4. Open the Data Connection section of the QMC.
 
 5. Choose the SERReports connection and remove the appended user information from the connection name.   

 6. Save the connection.

 7. Return to the Executive Dashboard Connector App and press the "Save" button.  Confirm that the SERReports connection no longer includes the appended user information in the name.

 8. Within the script change the following parameters:

 The App that the job will run on.

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
