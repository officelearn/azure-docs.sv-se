[Microsoft Azure-konfigurationshanterarens bibliotek för .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) erbjuder en klass för parsning av en anslutningssträng från en konfigurationsfil. [CloudConfigurationManager-klassen](https://msdn.microsoft.com/library/azure/mt634650.aspx) parsar konfigurationsinställningar oavsett om klientprogrammet körs på skrivbordet, på en mobil enhet, i en virtuell Azure-dator eller i en Azure-molntjänst.

Om du vill referera till CloudConfigurationManager-paketet lägger du till följande `using`-direktiv:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
```

Här följer ett exempel som visar hur du kan hämta en anslutningssträng från en konfigurationsfil:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Du måste inte använda Azure Configuration Manager. Du kan också använda ett API som .NET Frameworks [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx)-klass.

