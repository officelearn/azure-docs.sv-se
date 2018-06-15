---
title: Distribuera en virtuell dator med hjälp av C# och Resource Manager-mall | Microsoft Docs
description: Lär dig hur du använder C# och en Resource Manager-mall för att distribuera en Azure VM.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: cynthn
ms.openlocfilehash: e57505b4bd89a79af076dc4cf132c844ae0abd1d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
ms.locfileid: "31527832"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Distribuera en Azure-dator med hjälp av C# och Resource Manager-mall
Den här artikeln visar hur du distribuerar en Azure Resource Manager-mallen med C#. Den mall som du skapar distribuerar en enskild virtuell dator som kör Windows Server i ett nytt virtuellt nätverk med ett enda undernät.

En detaljerad beskrivning av den virtuella datorresursen finns [virtuella datorer i en Azure Resource Manager-mall](template-description.md). Mer information om alla resurser i en mall finns [genomgång av Azure Resource Manager-mall](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Det tar cirka 10 minuter för att utföra de här stegen.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

I det här steget ska kontrollera du att Visual Studio är installerat och du skapar ett konsolprogram som används för att distribuera mallen.

1. Om du inte redan gjort installera [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Välj **.NET skrivbord development** på arbetsbelastningar sidan och klicka sedan på **installera**. Sammanfattningsvis, kan du se att **utvecklingsverktyg för .NET Framework 4 4.6** väljs automatiskt för dig. Om du redan har installerat Visual Studio kan du lägga till .NET arbetsbelastningen i Visual Studio-starta.
2. I Visual Studio klickar du på **filen** > **ny** > **projekt**.
3. I **mallar** > **Visual C#** väljer **Konsolapp (.NET Framework)**, ange *myDotnetProject* för namnet på projektet, välj platsen för projektet och klicka sedan på **OK**.

## <a name="install-the-packages"></a>Installera paket

NuGet-paket är det enklaste sättet att installera de bibliotek som du behöver för att slutföra de här stegen. För att få de bibliotek som du behöver i Visual Studio kan du utföra de här stegen:

1. Klicka på **verktyg** > **Nuget Package Manager**, och klicka sedan på **Pakethanterarkonsolen**.
2. Ange följande kommandon i konsolen:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>Skapa filer

I det här steget skapar du en mallfil som distribuerar resurser och en fil med parametrar som tillhandahåller parametervärden för mallen. Du kan också skapa en auktoriseringsfil som används för att utföra åtgärder på Azure Resource Manager.

### <a name="create-the-template-file"></a>Skapa mall

1. I Solution Explorer högerklickar du på *myDotnetProject* > **Lägg till** > **nytt objekt**, och välj sedan **textfil** i *Visual C# objekt*. Namn på filen *CreateVMTemplate.json*, och klicka sedan på **Lägg till**.
2. Lägg till den här JSON-kod i filen som du skapade:

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

Om du vill ange värden för parametrarna resurs som definieras i mallen kan du skapa en fil med parametrar som innehåller värdena.

1. I Solution Explorer högerklickar du på *myDotnetProject* > **Lägg till** > **nytt objekt**, och välj sedan **textfil** i *Visual C# objekt*. Namn på filen *Parameters.json*, och klicka sedan på **Lägg till**.
2. Lägg till den här JSON-kod i filen som du skapade:

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

4. Spara filen parameters.JSON.

### <a name="create-the-authorization-file"></a>Skapa auktoriseringsfilen

Innan du kan distribuera en mall, se till att du har åtkomst till en [Active Directory-tjänstens huvudnamn](../../resource-group-authenticate-service-principal.md). Från huvudnamn för tjänsten kan du skaffa en token för att autentisera förfrågningar till Azure Resource Manager. Du bör också registrera program-ID, autentiseringsnyckeln och klient-ID som du behöver i auktoriseringsfilen.

1. I Solution Explorer högerklickar du på *myDotnetProject* > **Lägg till** > **nytt objekt**, och välj sedan **textfil** i *Visual C# objekt*. Namn på filen *azureauth.properties*, och klicka sedan på **Lägg till**.
2. Lägg till dessa auktorisering egenskaper:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    Ersätt **&lt;prenumerations-id&gt;** med prenumerations-ID **&lt;program-id&gt;** med programidentifierare Active Directory **&lt;autentiseringsnyckel&gt;** med nyckeln för programmet och **&lt;klient-id&gt;** med klient-ID.

3. Spara filen azureauth.properties.
4. Ange en miljövariabel i Windows som heter AZURE_AUTH_LOCATION med den fullständiga sökvägen till auktoriseringsfilen som du skapade, till exempel följande PowerShell-kommandot kan användas:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```
    
## <a name="create-the-management-client"></a>Skapa management-klienten

1. Öppna filen Program.cs för projektet som du skapade och Lägg sedan till följande using-instruktioner till befintliga instruktioner överst i filen:

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. Lägg till den här koden Main-metoden för att skapa management-klienten:

    ```
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Lägg till kod Main-metoden för att ange värden för programmet:

```
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Mallen och parametrar distribueras från ett lagringskonto i Azure. I det här steget skapar kontot och överför filer. 

Lägg till den här koden Main-metoden för att skapa kontot:

```
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

Distribuera mallen och parametrar från lagringskontot som skapades. 

Lägg till den här koden Main-metoden för att distribuera mallen:

```
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

Eftersom du debiteras för de resurser som används i Azure, men det är alltid bra att ta bort resurser som inte längre behövs. Du behöver inte ta bort varje resurs separat från en resursgrupp. Ta bort resursgruppen och alla dess resurser tas bort automatiskt. 

Lägg till den här koden Main-metoden för att ta bort resursgruppen:

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Köra programmet

Det bör ta ungefär fem minuter för den här konsolen programmet helt från början till slut. 

1. Klicka på att köra konsolprogrammet **starta**.

2. Innan du trycker på **ange** om du vill börja ta bort resurser, du kan ta några minuter för att verifiera att skapa resurser i Azure-portalen. Klicka på Distributionsstatus för att visa information om hur du distribuerar.

## <a name="next-steps"></a>Nästa steg
* Om det fanns problem med distributionen, är nästa steg att titta på [felsöka vanliga Azure-distribution med Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
* Lär dig hur du distribuerar en virtuell dator och dess stödfiler resurser genom att granska [distribuera ett Azure virtuella datorer med hjälp av C#](csharp.md).
