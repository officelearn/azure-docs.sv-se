---
title: Skapa en Azure IoT Hub med hjälp av en mall (.NET) | Microsoft Docs
description: Hur du använder en Azure Resource Manager-mall för att skapa en IoT Hub med C#-program.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: fb3456f399e9fa8bbe35336a3b8933c39a0d03d9
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57008966"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Skapa en IoT hub med Azure Resource Manager-mall (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Du kan använda Azure Resource Manager för att skapa och hantera Azure-IoT-hubbar programmässigt. Den här självstudien visar hur du använder en Azure Resource Manager-mall för att skapa en IoT hub från ett C#-program.

> [!NOTE]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser:  [Azure Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md).  Den här artikeln beskriver distributionsmodellen Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Ett aktivt Azure-konto. <br/>Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.
* En [Azure Storage-konto] [ lnk-storage-account] där du kan lagra dina mallfiler för Azure Resource Manager-.
* [Azure PowerShell 1.0] [ lnk-powershell-install] eller senare.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Förbered ditt Visual Studio-projekt

1. I Visual Studio skapar ett Visual C# Windows Classic Desktop-projekt med den **Konsolapp (.NET Framework)** projektmall. Ge projektet namnet **CreateIoTHub**.

2. Högerklicka på projektet i Solution Explorer och klicka sedan på **hantera NuGet-paket**.

3. I NuGet-Pakethanteraren, kontrollera **inkludera förhandsversion**, och på den **Bläddra** sidan Sök efter **Microsoft.Azure.Management.ResourceManager**. Välj paketet, klicka på **installera**i **granska ändringar** klickar du på **OK**, klicka sedan på **jag accepterar** att acceptera licenser.

4. I NuGet-Pakethanteraren, Sök efter **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Klicka på **installera**i **granska ändringar** klickar du på **OK**, klicka sedan på **jag accepterar** att acceptera licensen.

5. I Program.cs, ersätter den befintliga **med** instruktioner med följande kod:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. Lägg till följande statiska variabler ersätta platshållarvärdena i Program.cs. Du antecknade **ApplicationId**, **SubscriptionId**, **TenantId**, och **lösenord** tidigare i den här självstudien. **Namnet på ditt Azure Storage** är namnet på Azure Storage-konto där du lagrar dina mallfiler för Azure Resource Manager-. **Resursgruppens namn** är namnet på resursgruppen som du använder när du skapar IoT-hubben. Namnet kan vara en befintlig eller ny resursgrupp. **Distributionsnamn** är ett namn för distributionen, till exempel **Deployment_01**.

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

## <a name="submit-a-template-to-create-an-iot-hub"></a>Skicka in en mall för att skapa en IoT-hubb

Använd en JSON-fil i mallen och parameterfilerna för att skapa en IoT-hubb i resursgruppen. Du kan också använda en Azure Resource Manager-mall för att göra ändringar i en befintlig IoT-hubb.

1. Högerklicka på projektet i Solution Explorer, klicka på **Lägg till**, och klicka sedan på **nytt objekt**. Lägg till en JSON-fil som heter **template.json** i projektet.

2. Att lägga till en IoT-hubb som standard till den **USA, östra** region, Ersätt innehållet i **template.json** med följande resursdefinition. Den aktuella listan över regioner som har stöd för IoT Hub finns [Azure-Status][lnk-status]:

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

3. Högerklicka på projektet i Solution Explorer, klicka på **Lägg till**, och klicka sedan på **nytt objekt**. Lägg till en JSON-fil som heter **parameters.json** i projektet.

4. Ersätt innehållet i **parameters.json** med följande parameterinformation som anger ett namn för den nya IoT-hubben som **{din initialer} mynewiothub**. IoT hub-namn måste vara globalt unikt så att den ska innehålla ditt namn eller initialer:

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

5. I **Server Explorer**, ansluta till din Azure-prenumeration och i ditt Azure Storage-konto skapar du en behållare som kallas **mallar**. I den **egenskaper** panelen genom att ange den **offentlig läsbehörighet** behörigheter för den **mallar** behållaren **Blob**.

6. I **Server Explorer**, högerklicka på den **mallar** behållare och klicka sedan på **visa Blobbehållare**. Klicka på den **ladda upp Blob** knapp, Välj de två filerna **parameters.json** och **templates.json**, och klicka sedan på **öppna** att ladda upp den JSON-filer till den **mallar** behållare. URL: er på de blobar som innehåller JSON-data är:

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

8. Lägg till följande kod till den **CreateIoTHub** metod för att skicka filer till Azure Resource Manager-mallen och parameterfilerna:

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

9. Lägg till följande kod till den **CreateIoTHub** metod som visar status och nycklarna för den nya IoT-hubben:

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

2. Klicka på **skapa** och sedan **bygg lösning**. Korrigera eventuella fel.

3. Klicka på **felsöka** och sedan **Starta felsökning** att köra programmet. Det kan ta flera minuter för att distributionen ska köra.

4. Kontrollera att ditt program har lagts till den nya IoT-hubben genom att gå till den [Azure-portalen] [ lnk-azure-portal] och visa din lista över resurser. Du kan också använda den **Get-AzResource** PowerShell-cmdlet.

> [!NOTE]
> Det här exempelprogrammet lägger till en S1 Standard IoT-hubb som du faktureras. Du kan ta bort IoT-hubben via den [Azure-portalen] [ lnk-azure-portal] eller genom att använda den **Remove-AzResource** PowerShell-cmdlet när du är klar.

## <a name="next-steps"></a>Nästa steg
Nu du har distribuerat en IoT hub med en Azure Resource Manager-mall med ett C#-program, kanske du vill utforska ytterligare:

* Läs mer om funktionerna i den [IoT Hub REST API för resursprovider][lnk-rest-api].
* Läs [översikt över Azure Resource Manager] [ lnk-azure-rm-overview] att lära dig mer om funktionerna för Azure Resource Manager.
* JSON-syntax och egenskaper som ska användas i mallar finns i [Microsoft.Devices resurstyper](/azure/templates/microsoft.devices/iothub-allversions).

Mer information om hur du utvecklar för IoT Hub finns i följande artiklar:

* [Introduktion till C SDK][lnk-c-sdk]
* [Azure IoT SDK: er][lnk-sdks]

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

* [Distribuera AI till gränsenheter med Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
