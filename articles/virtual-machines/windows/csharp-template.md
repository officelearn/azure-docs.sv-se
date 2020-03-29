---
title: Distribuera en virtuell dator med C# och en Resource Manager-mall
description: Lär dig hur du använder C# och en Resource Manager-mall för att distribuera en Virtuell Azure.Learn to how to use C# and a Resource Manager template to deploy an Azure VM.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/14/2017
ms.author: cynthn
ms.openlocfilehash: 6d99c5ae91b80b9b6b9af08001b3a7c57bc7ca8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944533"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Distribuera en virtuell Azure-dator med C# och en Resource Manager-mall

Den här artikeln visar hur du distribuerar en Azure Resource Manager-mall med C#. Mallen som du skapar distribuerar en enda virtuell dator som kör Windows Server i ett nytt virtuellt nätverk med ett enda undernät.

En detaljerad beskrivning av resursen för den virtuella datorn finns [i Virtuella datorer i en Azure Resource Manager-mall](template-description.md). Mer information om alla resurser i en mall finns i [Azure Resource Manager-mallgenomgången](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Det tar ungefär 10 minuter att göra dessa steg.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

I det här steget kontrollerar du att Visual Studio är installerat och att du skapar ett konsolprogram som används för att distribuera mallen.

1. Om du inte redan har gjort det installerar du [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Välj **.NET-skrivbordsutveckling** på sidan Arbetsbelastningar och klicka sedan på **Installera**. I sammanfattningen kan du se att **.NET Framework 4 - 4.6 utvecklingsverktyg** väljs automatiskt åt dig. Om du redan har installerat Visual Studio kan du lägga till .NET-arbetsbelastningen med Visual Studio Launcher.
2. Klicka på **Arkiv** > **nytt** > **projekt**i Visual Studio.
3. I **Mallar** > **Visual C#** väljer du Console App **(.NET Framework),** anger *myDotnetProject* för namnet på projektet, väljer platsen för projektet och klickar sedan på **OK**.

## <a name="install-the-packages"></a>Installera paketen

NuGet-paket är det enklaste sättet att installera biblioteken som du behöver för att slutföra dessa steg. Så här hämtar du de bibliotek som du behöver i Visual Studio:

1. Klicka på **Verktyg** > **Nuget Package Manager**och sedan på Package Manager **Console**.
2. Skriv dessa kommandon i konsolen:

    ```powershell
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>Skapa filerna

I det här steget skapar du en mallfil som distribuerar resurser och en parameterfil som levererar parametervärden till mallen. Du kan också skapa en auktoriseringsfil som används för att utföra Azure Resource Manager-åtgärder.

### <a name="create-the-template-file"></a>Skapa mallfilen

1. Högerklicka på *myDotnetProject* > **Add** > **New Item**i Solution Explorer och välj sedan **Textfil** i *Visuella C#-objekt*. Ge filen namnet *CreateVMTemplate.json*och klicka sedan på **Lägg till**.
2. Lägg till den här JSON-koden i filen som du skapade:

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

3. Spara filen CreateVMTemplate.json.

### <a name="create-the-parameters-file"></a>Skapa parameterfilen

Om du vill ange värden för resursparametrarna i mallen skapar du en parameterfil som innehåller värdena.

1. Högerklicka på *myDotnetProject* > **Add** > **New Item**i Solution Explorer och välj sedan **Textfil** i *Visuella C#-objekt*. Ge filen namnet *Parameters.json*och klicka sedan på **Lägg till**.
2. Lägg till den här JSON-koden i filen som du skapade:

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

4. Spara filen Parameters.json.

### <a name="create-the-authorization-file"></a>Skapa auktoriseringsfilen

Innan du kan distribuera en mall kontrollerar du att du har åtkomst till ett huvudnamn för [Active Directory-tjänsten](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). Från tjänstens huvudnamn får du en token för att autentisera begäranden till Azure Resource Manager. Du bör också registrera program-ID: et, autentiseringsnyckeln och klient-ID:et som du behöver i auktoriseringsfilen.

1. Högerklicka på *myDotnetProject* > **Add** > **New Item**i Solution Explorer och välj sedan **Textfil** i *Visuella C#-objekt*. Ge filen namnet *azureauth.properties*och klicka sedan på **Lägg till**.
2. Lägg till dessa auktoriseringsegenskaper:

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

    Ersätt ** &lt;&gt; prenumerations-ID** med din prenumerationsidentifierare, ** &lt;&gt; program-ID** med Active Directory-programidentifieraren, ** &lt;autentiseringsnyckeln&gt; ** med programnyckeln och ** &lt;klient-ID&gt; ** med klientidentifieraren.

3. Spara filen azureauth.properties.
4. Ange en miljövariabel i Windows med namnet AZURE_AUTH_LOCATION med den fullständiga sökvägen till auktoriseringsfilen som du skapade, till exempel kan du använda följande PowerShell-kommando:

    ```powershell
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

    

## <a name="create-the-management-client"></a>Skapa hanteringsklienten

1. Öppna Program.cs filen för projektet som du skapade. Lägg sedan till dessa med hjälp av satser till befintliga satser överst i filen:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. Om du vill skapa hanteringsklienten lägger du till den här koden i huvudmetoden:

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

Om du vill ange värden för programmet lägger du till kod i huvudmetoden:

```csharp
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Mallen och parametrarna distribueras från ett lagringskonto i Azure. I det här steget skapar du kontot och överför filerna. 

Om du vill skapa kontot lägger du till den här koden i huvudmetoden:

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

Distribuera mallen och parametrarna från lagringskontot som skapades. 

Om du vill distribuera mallen lägger du till den här koden i huvudmetoden:

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

Eftersom du debiteras för resurser som används i Azure är det alltid bra att ta bort resurser som inte längre behövs. Du behöver inte ta bort varje resurs separat från en resursgrupp. Ta bort resursgruppen och alla dess resurser tas bort automatiskt. 

Om du vill ta bort resursgruppen lägger du till den här koden i huvudmetoden:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Köra appen

Det bör ta ungefär fem minuter för den här konsolen programmet att köras helt från början till. 

1. Om du vill köra konsolprogrammet klickar du på **Start**.

2. Innan du trycker på **Retur** för att börja ta bort resurser kan det ta några minuter att verifiera att resurserna skapas i Azure-portalen. Klicka på distributionsstatusen om du vill se information om distributionen.

## <a name="next-steps"></a>Nästa steg

* Om det fanns problem med distributionen, skulle ett nästa steg vara att titta på [Felsöka vanliga Azure-distributionsfel med Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
* Lär dig hur du distribuerar en virtuell dator och dess stödresurser genom att granska [Distribuera en virtuell Azure-dator med C#](csharp.md).