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

![Demo Content Library](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Demo-Content-Library.PNG)

6. ![App Button Edit Sheet](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/App-Button-Edit-Sheet.PNG)

6. Check the Contents Property on the right side of the screen

7. Upload the ExecutiveDashboard.xlsx file from \Reporting\Examples directory.

![Demo Content Library Upload](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Demo-Content-Library-Upload.PNG)


##  ON DEMAND REPORTS DEMO

1. Open the Executive Dashboard App from the Hub

![Hub Icon App Executive Dashboard](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Hub-Icon-App-Executive-Dashboard.PNG)

2. Choose the Account Receivable sheet.

![Hub Icon App Account Receivable](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Hub-Icon-App-Account-Receivable.PNG)

3. ![App Button Edit Sheet](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/App-Button-Edit-Sheet.PNG)

4. Click on the ser-On Demand Sense Report Custom Object that includes the Generate Report Button.

![App Custom Object Box](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/App-Custom-Object-Box.PNG)

5. Open the Configuration Tab in the Propery Panel on the right side of the screen

6. Open the Library dropdown list and choose the source of your template. Default, SenseExcelReports (content library) or InApp.

![Demo App Select Content](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Demo-App-Select-Content.png)

7. Choose ExceutiveDashboard.xlsx from the Content dropdown list.

8. Choose the Output Format. "Excel" or "PDF"

![Demo App Select Output Format](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Demo-App-Select-Output-Format.png)

9. Chose the Selection Mode.  "Selection over shared session" or "Selection over bookmark". 

![Demo App Select Selection Mode](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Demo-App-Select-Selection-Mode.png)

10. Choose Direct Download On or Off. Selecting "On" will automatically download the output file in the specified format to the local machine as well as place it in the Reports section of the Hub.  Choosing "Off" will place the file in the Hub and allow the user to  download the file on demand by pressing the Download Report button in the Custom Object panel.

12. Use the Selections tool, enter "Region" in the search dialgue box and select the values you would like included in your report.   The associated Segments will update based on that selection.

![Demo App Report Selection](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Demo-App-Report-Selection.PNG)

13. This application is configured to loop over the included Segment values based upon the app Selections made by the user. This will generate an output report including the following: a Title Page, Summary Dashboard and seperate Detail page for each Segment.


##  SCHEDULED REPORTS DEMO

 1. Open the Reporting Task Demo App from the HUB.
 
 ![Hub Icon App Reporting Task Demo(https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Hub-Icon-Reporting-Task.PNG)

 2. Press "Open" when the "This app contains no data" dialogue box appears.
 
![Demo App This app contains no data](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Demo-App-This-app-contains-no-data.PNG) 

 3.  ![App Button Create New Connection](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/App-Button-Create-New-Connection.PNG)

 4. Open the Data Connection section of the QMC.
 
 5. Choose the SERReports connection 

![Demo-App-Data-Connection][https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/Demo-App-Data-Connection.PNG)

 6.  ![QMC Button Edit](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/QMC-Button-Edit.PNG)
 
 7. Remove the appended user information from the connection name.

 8.  ![QMC Button Save](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/QMC-Button-Save.PNG)

 9. Return to the Executive Dashboard Connector App.
 
 10. ![App Button Save](https://github.com/senseexcel/senseexcel-reporting/blob/master/docs/App-Button-Save.PNG)
 
 10. Confirm that the SERReports connection no longer includes the appended user information in the name.

 11. Within the script change the following parameters:

 The App that the job will run on.

**app:  Executive Dashboard AppID from QMC**  

To Choose the Template that the job will run on.  /// will use the file(s) attached to the App.  // will use a lib: statement or a discreet path.

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
