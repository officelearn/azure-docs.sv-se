### Abrufen der Verbindungszeichenfolge
Sie können die **CloudStorageAccount** Typ zum darstellen 
Speicherkontodaten abzubilden. Falls Sie eine 
Azure-Projektvorlage verwenden und/oder über einen Verweis auf
Microsoft.WindowsAzure.CloudConfigurationManager verfügen, 
können die **CloudConfigurationManager** Typ
aus
der Azure-Dienstkonfiguration abrufen:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Wenn Sie eine Anwendung ohne einen Verweis auf "Microsoft.windowsazure.CloudConfigurationManager" erstellen und die Verbindungszeichenfolge sich in befindet der `web.config` oder `app.config` wie oben gezeigt, dann können Sie **ConfigurationManager** zum Abrufen der Verbindungszeichenfolge.  Sie müssen einen Verweis auf "System.Configuration.dll" zu Ihrem Projekt und eine andere Namespace-Deklaration dafür hinzufügen:

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);


