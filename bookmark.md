## If you want to use yoor Bookmark - Template Settings example


template: 
{

  input: content:///ExecutiveDashboard.xlsx
  
  output: ='Report_'&only(Region)&'_'&max([Fiscal Year])&'.pdf'
  
      selections:[
        {
          type: static
          objectType: bookmark
          values: NameOfTheBookmark
        }
      ]

}
