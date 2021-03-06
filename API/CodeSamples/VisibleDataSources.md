#VisibleDataSources

[[_TOC_]]

##About
**Data Type:** string[]  
**Accepted Values:** Table, view and stored procedure names   
**Default value:** Empty array  
**Impacted Features:** Report Design, Report List  
**Purpose:** Gets or sets the array of names for tables and views that should be shown in the "Data Sources (Tables and Views)" drop-down in the Report Designer on the Data Sources tab.  
**Usage:** This setting is used to make stored procedures appear in Izenda. It is also used to limit what data sources each user has access to for security reasons.  
**Caveats:** Setting any values in this list will completely override the normally constructed list of tables and views on a per-user basis. Additionally, any reports that use a data source that is hidden from the current user will not be available for a user to access.  

##Code Samples
###Global.asax (C♯)

```csharp
//main class: inherits DatabaseAdHocConfig or FileSystemAdHocConfig
public class CustomAdHocConfig : Izenda.AdHoc.DatabaseAdHocConfig
{
  // Configure settings
  // Add custom settings after setting the license key and connection string by overriding the ConfigureSettings() method
  public static void InitializeReporting() {
    //Check to see if we've already initialized.
    if (HttpContext.Current.Session == null || HttpContext.Current.Session["ReportingInitialized"] != null)
      return;
    AdHocSettings.LicenseKey = "INSERT_LICENSE_KEY_HERE";
    //Creates a connection to Microsoft SQL Server
    AdHocSettings.SqlServerConnectionString = "INSERT_CONNECTION_STRING_HERE";
    Izenda.AdHoc.AdHocSettings.AdHocConfig = new CustomAdHocConfig();
    AdHocSettings.VisibleDataSources = new string[] {"DataSource1", "DataSource2"}; //The relevant setting
    HttpContext.Current.Session["ReportingInitialized"] = true;
  }
}
```

###Global.asax (VB.NET)

```visualbasic
'main class: inherits DatabaseAdHocConfig or FileSystemAdHocConfig
Public Class CustomAdHocConfig
    Inherits Izenda.AdHoc.DatabaseAdHocConfig

    Shared Sub InitializeReporting()
        'Check to see if we've already initialized
        If HttpContext.Current.Session Is Nothing OrElse HttpContext.Current.Session("ReportingInitialized") IsNot Nothing Then
            Return
        'Initialize System
        AdHocSettings.LicenseKey = "INSERT_LICENSE_KEY_HERE"
        AdHocSettings.SqlServerConnectionString = "INSERT_CONNECTION_STRING_HERE"
        Izenda.AdHoc.AdHocSettings.AdHocConfig = New CustomAdHocConfig()
        AdHocSettings.VisibleDataSources = New String() {"Datasource1", "Datasource2"} 'The relevant setting
        HttpContext.Current.Session("ReortingInitialized") = True
    End Sub
End Class
```

##Alternate Usage: All Tables, Views and Stored Procedures Visible 

If you would like for all tables, views and stored procedures to be shown you can use the following schema query to populate the visible datasources. (Stored Procedures that are not used for reporting must be limited using the where clause in this query. Stored Procedures are run when loading the data source list, so any that are used to drop tables, delete records, etc. should not be included )

```csharp
//setting vis datasources
DataSet ds = AdHocContext.Driver.GetDataSet(AdHocContext.Driver.CreateCommand("select * from sys.objects where type in ('U','P','V')"));
string[] results = new string[ds.Tables[0].Rows.Count];
for (int i = 0; i < ds.Tables[0].Rows.Count; i++)
results[i] = ds.Tables[0].Rows[i][0].ToString();
AdHocSettings.VisibleDataSources = results;
```

##Screenshots

**AdHocSettings.VisibleDataSources = new string[] { "Products", "Orders", "Customers" };**

![VisibleDataSources = new string[] {"Products", "Orders", "Customers" };](/API/CodeSamples/VisibleDataSources/VisibleDataSources.png)
