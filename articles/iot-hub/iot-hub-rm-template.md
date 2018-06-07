---
title: Skapa en Azure IoT-hubb med en mall (.NET) | Microsoft Docs
description: Hur du använder en Azure Resource Manager-mall för att skapa en IoT-hubb med ett C#-program.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 1a64749b7218fccfdad6b6eeebfac39a44aa0522
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635550"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Skapa en IoT-hubb med hjälp av Azure Resource Manager-mall (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Du kan använda Azure Resource Manager för att skapa och hantera Azure IoT-hubbar programmässigt. Den här kursen visar hur du skapar en IoT-hubb från ett C#-program med hjälp av en Azure Resource Manager-mall.

> [!NOTE]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Azure Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md).  Den här artikeln täcker Azure Resource Manager-distributionsmodellen.

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Ett aktivt Azure-konto. <br/>Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.
* En [Azure Storage-konto] [ lnk-storage-account] där du kan lagra dina mallfiler för Azure Resource Manager-.
* [Azure PowerShell 1.0] [ lnk-powershell-install] eller senare.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Förbered din Visual Studio-projekt

1. I Visual Studio, skapa ett Visual C# klassiska skrivbordet projekt med den **Konsolapp (.NET Framework)** projektmall. Namnge projektet **CreateIoTHub**.

2. Högerklicka på projektet i Solution Explorer och klicka sedan på **hantera NuGet-paket**.

3. Kontrollera NuGet Package Manager **inkludera förhandsversion**, och på den **Bläddra** sidan Sök efter **Microsoft.Azure.Management.ResourceManager**. Välj paketet, klicka på **installera**i **granska ändringar** klickar du på **OK**, klicka på **jag accepterar** att acceptera licenser.

4. I NuGet-Pakethanteraren, söka efter **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Klicka på **installera**i **granska ändringar** klickar du på **OK**, klicka på **jag accepterar** att acceptera licensvillkoren.

5. I Program.cs ersätta den befintliga **med** instruktioner med följande kod:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. Lägg till följande statiska variabler ersätta platshållarvärdena i Program.cs. Du antecknade **ApplicationId**, **SubscriptionId**, **TenantId**, och **lösenord** tidigare i den här kursen. **Namnet på ditt Azure Storage** är namnet på Azure Storage-konto där du lagrar mallfilerna för Azure Resource Manager-. **Resursgruppens namn** är namnet på resursgruppen som du använder när du skapar en IoT-hubben. Namnet kan vara en befintlig eller ny resursgrupp. **Distributionsnamnet** är ett namn för distributionen, till exempel **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-a-template-to-create-an-iot-hub"></a>Skicka en mall för att skapa en IoT-hubb

Använda en JSON-fil i mallen och parametern för att skapa en IoT-hubb i resursgruppen. Du kan också använda en Azure Resource Manager-mall för att göra ändringar i en befintlig IoT-hubb.

1. Högerklicka på projektet i Solution Explorer, klicka på **Lägg till**, och klicka sedan på **nytt objekt**. Lägg till en JSON-fil som kallas **template.json** i projektet.

2. Att lägga till en IoT-hubb som standard till den **östra USA** region, ersätter du innehållet i **template.json** med följande resursdefinitionen. Den aktuella listan över regioner som har stöd för IoT-hubb finns [Azure Status][lnk-status]:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. Högerklicka på projektet i Solution Explorer, klicka på **Lägg till**, och klicka sedan på **nytt objekt**. Lägg till en JSON-fil som kallas **parameters.json** i projektet.

4. Ersätt innehållet i **parameters.json** med följande parameterinformation som anger ett namn för den nya IoT-hubben som **{din initialer} mynewiothub**. IoT-hubbnamnet måste vara globalt unika så att den ska innehålla ditt namn eller initialer:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
  [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

5. I **Server Explorer**, ansluta till din Azure-prenumeration och skapa en behållare som kallas i Azure Storage-konto **mallar**. I den **egenskaper** panelen genom att ange den **offentlig läsbehörighet** behörigheter för den **mallar** behållare för att **Blob**.

6. I **Server Explorer**, högerklicka på den **mallar** behållaren och klicka sedan på **visa Blob-behållaren**. Klicka på den **överför Blob** , Välj två filer, **parameters.json** och **templates.json**, och klicka sedan på **öppna** överför JSON-filer till den **mallar** behållare. URL: er för de blobbar som innehåller de JSON-data är:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Lägg till följande metod i Program.cs:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Lägg till följande kod i den **CreateIoTHub** metod för att skicka filer till Azure Resource Manager-mall och parameter:

    ```csharp
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

9. Lägg till följande kod i den **CreateIoTHub** metod som visar status och nycklarna för ny IoT-hubben:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Slutför och köra programmet

Du kan nu slutföra programmet genom att anropa den **CreateIoTHub** metoden innan du skapar och kör den.

1. Lägg till följande kod i slutet av den **Main** metoden:

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Klicka på **skapa** och sedan **skapa lösning**. Korrigera eventuella fel.

3. Klicka på **felsöka** och sedan **Start Debugging** att köra programmet. Det kan ta flera minuter för att distributionen ska köras.

4. För att verifiera ditt program läggs ny IoT-hubben, finns det [Azure-portalen] [ lnk-azure-portal] och visa en lista över resurser. Du kan också använda den **Get-AzureRmResource** PowerShell-cmdlet.

> [!NOTE]
> Det här exempelprogrammet lägger till en S1 Standard IoT-hubb du debiteras. Du kan ta bort IoT-hubb via den [Azure-portalen] [ lnk-azure-portal] eller genom att använda den **ta bort AzureRmResource** PowerShell-cmdlet när du är klar.

## <a name="next-steps"></a>Nästa steg
Nu du har distribuerat en IoT-hubb med en Azure Resource Manager-mall med ett C#-program, kanske du vill utforska vidare:

* Läs mer om funktionerna i den [IoT-hubb resursprovidern REST API][lnk-rest-api].
* Läs [översikt över Azure Resource Manager] [ lnk-azure-rm-overview] att lära dig mer om funktionerna i Azure Resource Manager.

Mer information om hur du utvecklar för IoT-hubb finns i följande artiklar:

* [Introduktion till C SDK][lnk-c-sdk]
* [Azure IoT-SDK][lnk-sdks]

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Distribuera AI till gränsenheter med Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
