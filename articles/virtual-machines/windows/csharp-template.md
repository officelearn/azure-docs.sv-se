---
title: Distribuera en virtuell dator med C# och en Resource Manager-mall
description: Lär dig hur du använder C# och en Resource Manager-mall för att distribuera en virtuell Azure-dator.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: how-to
ms.date: 07/14/2017
ms.author: cynthn
ms.custom: devx-track-csharp
ms.openlocfilehash: 779a09532790ea272d8c95ac28f8c152216efc5a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008658"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Distribuera en virtuell Azure-dator med C# och en Resource Manager-mall

Den här artikeln visar hur du distribuerar en Azure Resource Manager-mall med C#. Mallen som du skapar distribuerar en enskild virtuell dator som kör Windows Server i ett nytt virtuellt nätverk med ett enda undernät.

En detaljerad beskrivning av den virtuella dator resursen finns [i virtuella datorer i en Azure Resource Manager mall](template-description.md). Mer information om alla resurser i en mall finns i [genom gång av Azure Resource Manager mall](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

Det tar cirka 10 minuter att utföra dessa steg.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

I det här steget ser du till att Visual Studio är installerat och du skapar ett konsol program som används för att distribuera mallen.

1. Om du inte redan har gjort det installerar du [Visual Studio](/visualstudio/install/install-visual-studio). Välj **.net Desktop Development** på sidan arbets belastningar och klicka sedan på **Installera**. I sammanfattningen kan du se att **.NET Framework 4-4,6 utvecklingsverktyg** väljs automatiskt åt dig. Om du redan har installerat Visual Studio kan du lägga till .NET-arbetsbelastningen med hjälp av Visual Studio Launcher.
2. I Visual Studio klickar du på **fil**  >  **nytt**  >  **projekt**.
3. I **mallar**  >  **Visual C#** väljer du **konsol program (.NET Framework)**, anger *myDotnetProject* som namn på projektet, väljer projektets plats och klickar sedan på **OK**.

## <a name="install-the-packages"></a>Installera paketen

NuGet-paket är det enklaste sättet att installera de bibliotek som du behöver för att slutföra de här stegen. Gör så här för att hämta de bibliotek som du behöver i Visual Studio:

1. Klicka på **verktyg**  >  **NuGet Package Manager** och klicka sedan på **Package Manager-konsolen**.
2. Skriv följande kommandon i-konsolen:

    ```powershell
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>Skapa filerna

I det här steget skapar du en mallfil som distribuerar resurserna och en parameter fil som tillhandahåller parameter värden till mallen. Du skapar också en auktoriseringspost som används för att utföra Azure Resource Manager åtgärder.

### <a name="create-the-template-file"></a>Skapa mallfilen

1. I Solution Explorer högerklickar du på *myDotnetProject*  >  **Lägg till**  >  **nytt objekt** och väljer sedan **textfil** i *Visual C#-objekt*. Ge filen namnet *CreateVMTemplate.jspå* och klicka sedan på **Lägg till**.
2. Lägg till den här JSON-koden till filen som du skapade:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" }
      },
      "variables": {
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks','myVNet')]", 
        "subnetRef": "[concat(variables('vnetID'),'/subnets/mySubnet')]", 
      },
      "resources": [
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "myPublicIPAddress",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "myresourcegroupdns1"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "myVNet",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "mySubnet",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "myNic",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses/', 'myPublicIPAddress')]",
            "[resourceId('Microsoft.Network/virtualNetworks/', 'myVNet')]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": { "id": "[resourceId('Microsoft.Network/publicIPAddresses','myPublicIPAddress')]" },
                  "subnet": { "id": "[variables('subnetRef')]" }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-04-30-preview",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "myVM",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkInterfaces/', 'myNic')]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_DS1" },
            "osProfile": {
              "computerName": "myVM",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "myManagedOSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces','myNic')]"
                }
              ]
            }
          }
        }
      ]
    }
    ```

3. Spara CreateVMTemplate.jspå filen.

### <a name="create-the-parameters-file"></a>Skapa parameter filen

Om du vill ange värden för resurs parametrarna i mallen skapar du en parameter fil som innehåller värdena.

1. I Solution Explorer högerklickar du på *myDotnetProject*  >  **Lägg till**  >  **nytt objekt** och väljer sedan **textfil** i *Visual C#-objekt*. Ge filen namnet *Parameters.jspå* och klicka sedan på **Lägg till**.
2. Lägg till den här JSON-koden till filen som du skapade:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

4. Spara Parameters.jspå filen.

### <a name="create-the-authorization-file"></a>Skapa verifierings filen

Innan du kan distribuera en mall ser du till att du har åtkomst till ett [Active Directory tjänstens huvud namn](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). Från tjänstens huvud namn hämtar du en token för att autentisera begär anden till Azure Resource Manager. Du bör också registrera program-ID, autentiseringsnyckel och klient-ID som du behöver i verifierings filen.

1. I Solution Explorer högerklickar du på *myDotnetProject*  >  **Lägg till**  >  **nytt objekt** och väljer sedan **textfil** i *Visual C#-objekt*. Ge filen namnet *azureauth. Properties* och klicka sedan på **Lägg till**.
2. Lägg till följande egenskaper för auktorisering:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    Ersätt **&lt; prenumerations- &gt; ID** med prenumerations-ID, **&lt; program &gt; -ID** med Active Directory-program-ID, **&lt; autentisering- &gt; nyckel** med program nyckeln och klient-ID med klient- **&lt; ID: t &gt;** .

3. Spara filen azureauth. Properties.
4. Ange en miljö variabel i Windows med namnet AZURE_AUTH_LOCATION med den fullständiga sökvägen till den auktoriserade filen som du har skapat, till exempel kan du använda följande PowerShell-kommando:

    ```powershell
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

    

## <a name="create-the-management-client"></a>Skapa hanterings klienten

1. Öppna Program.cs-filen för det projekt som du har skapat. Lägg sedan till dessa med-instruktioner till de befintliga instruktionerna överst i filen:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. Lägg till den här koden i huvud metoden för att skapa hanterings klienten:

    ```csharp
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Om du vill ange värden för programmet lägger du till kod i huvud metoden:

```csharp
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Mallen och parametrarna distribueras från ett lagrings konto i Azure. I det här steget skapar du kontot och laddar upp filerna. 

Lägg till den här koden i huvud metoden för att skapa kontot:

```csharp
string storageAccountName = SdkContext.RandomResourceName("st", 10);

Console.WriteLine("Creating storage account...");
var storage = azure.StorageAccounts.Define(storageAccountName)
    .WithRegion(Region.USWest)
    .WithExistingResourceGroup(resourceGroup)
    .Create();

var storageKeys = storage.GetKeys();
string storageConnectionString = "DefaultEndpointsProtocol=https;"
    + "AccountName=" + storage.Name
    + ";AccountKey=" + storageKeys[0].Value
    + ";EndpointSuffix=core.windows.net";

var account = CloudStorageAccount.Parse(storageConnectionString);
var serviceClient = account.CreateCloudBlobClient();

Console.WriteLine("Creating container...");
var container = serviceClient.GetContainerReference("templates");
container.CreateIfNotExistsAsync().Wait();
var containerPermissions = new BlobContainerPermissions()
    { PublicAccess = BlobContainerPublicAccessType.Container };
container.SetPermissionsAsync(containerPermissions).Wait();

Console.WriteLine("Uploading template file...");
var templateblob = container.GetBlockBlobReference("CreateVMTemplate.json");
templateblob.UploadFromFileAsync("..\\..\\CreateVMTemplate.json").Result();

Console.WriteLine("Uploading parameters file...");
var paramblob = container.GetBlockBlobReference("Parameters.json");
paramblob.UploadFromFileAsync("..\\..\\Parameters.json").Result();
```

## <a name="deploy-the-template"></a>Distribuera mallen

Distribuera mallen och parametrarna från det lagrings konto som skapades. 

Om du vill distribuera mallen lägger du till den här koden i huvud metoden:

```csharp
var templatePath = "https://" + storageAccountName + ".blob.core.windows.net/templates/CreateVMTemplate.json";
var paramPath = "https://" + storageAccountName + ".blob.core.windows.net/templates/Parameters.json";
var deployment = azure.Deployments.Define("myDeployment")
    .WithExistingResourceGroup(groupName)
    .WithTemplateLink(templatePath, "1.0.0.0")
    .WithParametersLink(paramPath, "1.0.0.0")
    .WithMode(Microsoft.Azure.Management.ResourceManager.Fluent.Models.DeploymentMode.Incremental)
    .Create();
Console.WriteLine("Press enter to delete the resource group...");
Console.ReadLine();
```

## <a name="delete-the-resources"></a>Ta bort resurserna

Eftersom du debiteras för resurser som används i Azure är det alltid en bra idé att ta bort resurser som inte längre behövs. Du behöver inte ta bort varje resurs separat från en resurs grupp. Ta bort resurs gruppen och alla dess resurser tas bort automatiskt. 

Om du vill ta bort resurs gruppen lägger du till den här koden i huvud metoden:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Kör programmet

Det bör ta ungefär fem minuter för konsol programmet att köras helt från början till slut. 

1. Kör konsol programmet genom att klicka på **Start**.

2. Innan du trycker på **RETUR** för att börja ta bort resurser kan det ta några minuter innan du verifierar att resurserna har skapats i Azure Portal. Klicka på distributions status om du vill se information om distributionen.

## <a name="next-steps"></a>Nästa steg

* Om det uppstod problem med distributionen är ett nästa steg att titta på [Felsök vanliga problem med Azure-distribution med Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md).
* Lär dig hur du distribuerar en virtuell dator och dess stöd resurser genom att granska [distribuera en virtuell Azure-dator med C#](../../azure-resource-manager/templates/deploy-rest.md).