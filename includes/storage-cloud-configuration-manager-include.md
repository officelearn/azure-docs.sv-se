[Microsoft Azure-konfigurationshanterarens bibliotek för .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) erbjuder en klass för parsning av en anslutningssträng från en konfigurationsfil. [CloudConfigurationManager-klassen](https://msdn.microsoft.com/library/azure/mt634650.aspx) parsar konfigurationsinställningar oavsett om klientprogrammet körs på skrivbordet, på en mobil enhet, i en virtuell Azure-dator eller i en Azure-molntjänst.

För att referera till CloudConfigurationManager-paketet lägger du till följande `using`-uttryck till din klass:

```csharp
using Microsoft.Azure;    //Namespace for CloudConfigurationManager
```

Här är ett exempel som visar hur du hämtar en anslutningssträng från en konfigurationsfil:
```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```
Du måste inte använda Azure Configuration Manager. Du kan också använda ett API som .NET Frameworks [ConfigurationManager-klass](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx).



<!--HONumber=Nov16_HO3-->


