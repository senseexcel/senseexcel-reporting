
 -----------------------  CSV outputFormatOptions with delimeter -----------------------

SET task = ´
 Connection settings to Qlik Desktop or Server
connections:
{
	app: xxxxxxx-xxxxx-xxxxx-xxxx-xxxxxxxx
}

 Template Settings
template: 
{
	input: content://senseexcel/your-template.xlsx
	output: ='Finance.csv'
  	# outputFormatOptions: {
      		# seperator: ';' //set your seperator what you like...
  		}
		
	
  }
