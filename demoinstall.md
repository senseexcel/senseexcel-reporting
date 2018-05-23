> # SENSE EXCEL REPORTING DEMO INSTALL

##  INSTALL DEMO APPS

Upload the following files to the QMC App Library from the existing \qlikshare\Reporting\Examples directory or Sense Excel Reportng.zip.

Reporting Task Demo.qvf
Executive Dashboard.qvf


QMC > MANAGE CONTENT > APPS > Import app > Choose FIle >Executive Dashboard.qvf
QMC > MANAGE CONTENT > APPS > Import app > Choose FIle >Reporting Task Demo.qvf

INSERT PICTURE HERE - Demo-Apps-Import.png


## CREATE CONTENT LIBRARY

QMC > MANAGE CONTENT > Content libraries > Create New

Identification : senseexcelreporting

Hit Apply Button

Check the Advanced Property on the right side of the screen

In the Conditions box, add the value !userIsAnonymous()

Hit Apply Button

INSERT PICTURE Demo-Content-Library.png

Check the Contents Property on the right side of the screen

Upload the ExecutiveDashboard.xlsx file from \Reporting\Examples directory.

INSERT PICTURE Demo-Content-Library-Upload.png

##  ON DEMAND REPORTS DEMO

1. Open the Executive Dashboard App from the Hub

2. Choose the Accounts Receivable tab

3. Press the sheet Edit Button

INSERT PICTURE Demo-Report-Edit

4. Click on the ser-On Demand Sense Report Custom Object that includes the Generate Report Button.

5. Open the Configuration Tab in the Propery Panel on the right side of the screen

6. Open the Library dropdown list and choose the source of your template. Default, SenseExcelReports (content library) or InApp.

7. Choose ExceutiveDashboard.xlsx from the Content dropdown list.

8. Choose the Output Format. Excel or PDF

9. Chose the Selection Mode.  Selection over shared session or Selection over bookmark. 

10. Choose Direct Download On or Off.  On will download the output file in the selected format to the local machine automatically as well as placing it in the Reports section of the Hub.  Off will place the file in the Hub and will allow the user to manually download the file manually by pressing the Download Report button in the Custom Object panel.

11. Hit the Done Key.

12. Use the Selections button, enter Region in the search dialgue box and select the values you would like included in your report.   The associated Segments will update based on that selection.

13. Hit the Generate Report Button to execute the report.

This demo application is configured to loop over the Segment values based upon the Selection made and generate an output report which includes a Title Page a Summary Dasboard and a seperate Detail page for each Segment.



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
