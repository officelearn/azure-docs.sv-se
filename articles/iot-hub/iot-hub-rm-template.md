---
title: Skapa en Azure IoT-hubb med en mall (.NET) | Microsoft-dokument
description: Så här använder du en Azure Resource Manager-mall för att skapa en IoT Hub med ett C#-program.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 02e814a9da320d688fe57edf3a3fe0640b8f5a47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976743"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Skapa en IoT-hubb med Azure Resource Manager-mall (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Du kan använda Azure Resource Manager för att skapa och hantera Azure IoT-hubbar programmässigt. Den här självstudien visar hur du använder en Azure Resource Manager-mall för att skapa en IoT-hubb från ett C#-program.

> [!NOTE]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Azure Resource Manager och klassisk](../azure-resource-manager/management/deployment-models.md).  Den här artikeln beskriver hur du använder distributionsmodellen för Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio.
* Ett aktivt Azure-konto. <br/>Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.
* Ett [Azure Storage-konto][lnk-storage-account] där du kan lagra dina Azure Resource Manager-mallfiler.
* [Azure PowerShell 1.0][lnk-powershell-install] eller senare.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Förbereda visual studio-projektet

1. Skapa ett Visual C# Windows Classic Desktop-projekt i Visual Studio med projektmallen **Console App (.NET Framework).** Ge projektet namnet **CreateIoTHub**.

2. Högerklicka på projektet i Solution Explorer och klicka sedan på **Hantera NuGet-paket**.

3. I NuGet Package Manager kontrollerar du **Inkludera förhandsversion och**sök efter **Microsoft.Azure.Management.ResourceManager**på sidan **Bläddra.** Markera paketet, klicka på **Installera**, i **Granska ändringar** klicka på **OK**och klicka sedan på **Jag accepterar** för att acceptera licenserna.

4. Sök efter **Microsoft.IdentityModel.Clients.ActiveDirectory**i NuGet Package Manager .  Klicka på **Installera**i **Granska ändringar** på **OK**och sedan på **Jag accepterar** för att acceptera licensen.

5. I Program.cs ersätter du befintliga **med hjälp av** satser med följande kod:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. I Program.cs lägger du till följande statiska variabler som ersätter platshållarvärdena. Du har antecknat **ApplicationId,** **SubscriptionId**, **TenantId**och **Lösenord** tidigare i den här självstudien. **Ditt Azure Storage-kontonamn** är namnet på Azure Storage-kontot där du lagrar dina Azure Resource Manager-mallfiler. **Resursgruppsnamn** är namnet på den resursgrupp som du använder när du skapar IoT-hubben. Namnet kan vara en befintlig eller ny resursgrupp. **Distributionsnamn** är ett namn för distributionen, till exempel **Deployment_01**.

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

Använd en JSON-mall och parameterfil för att skapa en IoT-hubb i resursgruppen. Du kan också använda en Azure Resource Manager-mall för att göra ändringar i en befintlig IoT-hubb.

1. Högerklicka på projektet i Solution Explorer, klicka på **Lägg till**och klicka sedan på **Nytt objekt**. Lägg till en JSON-fil som heter **template.json** i projektet.

2. Om du vill lägga till en standard-IoT-hubb i regionen **östra USA** ersätter du innehållet i **template.json** med följande resursdefinition. För den aktuella listan över regioner som stöder IoT Hub se [Azure-status:][lnk-status]

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

3. Högerklicka på projektet i Solution Explorer, klicka på **Lägg till**och klicka sedan på **Nytt objekt**. Lägg till en JSON-fil som heter **parameters.json** i projektet.

4. Ersätt innehållet i **parameters.json** med följande parameterinformation som anger ett namn för den nya IoT-hubben, till exempel **{your initials}mynewiothub**. IoT-hubbnamnet måste vara globalt unikt så att det ska innehålla ditt namn eller dina initialer:

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

5. I **Server Explorer**ansluter du till din Azure-prenumeration och i ditt Azure Storage-konto skapar du en behållare som kallas **mallar**. Ange **Properties** behörigheterna för **offentlig läsåtkomst** för **mallbehållaren** till **Blob**på egenskapspanelen.

6. Högerklicka på **mallbehållaren** i **Server Explorer**och klicka sedan på **Visa Blob Container**. Klicka på knappen **Ladda upp blob,** markera de två filerna, **parameters.json** och **templates.json**och klicka sedan på **Öppna** för att överföra JSON-filerna till **mallbehållaren.** Url:erna för blobbar som innehåller JSON-data är:

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

8. Lägg till följande kod i **Metoden CreateIoTHub** för att skicka mall- och parameterfilerna till Azure Resource Manager:

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

9. Lägg till följande kod i **Metoden CreateIoTHub** som visar status och nycklar för den nya IoT-hubben:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Slutför och kör programmet

Du kan nu slutföra programmet genom att anropa **CreateIoTHub-metoden** innan du skapar och kör den.

1. Lägg till följande kod i **Main** slutet av huvudmetoden:

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Klicka på **Bygg** och sedan **bygg lösning**. Korrigera eventuella fel.

3. Klicka på **Felsöka** och sedan **starta felsökning** för att köra programmet. Det kan ta flera minuter innan distributionen körs.

4. Om du vill verifiera att ditt program har lagt till den nya IoT-hubben besöker du [Azure-portalen][lnk-azure-portal] och visar din lista över resurser. Du kan också använda Cmdleten **Get-AzResource** PowerShell.

> [!NOTE]
> Det här exemplet läggs till en S1 Standard IoT Hub som du faktureras för. Du kan ta bort IoT-hubben via [Azure-portalen][lnk-azure-portal] eller med hjälp av cmdleten **Remove-AzResource** PowerShell när du är klar.

## <a name="next-steps"></a>Nästa steg
Nu när du har distribuerat en IoT-hubb med hjälp av en Azure Resource Manager-mall med ett C#-program kanske du vill utforska vidare:

* Läs om funktionerna i [IoT Hub-resursprovidern REST API][lnk-rest-api].
* Läs [översikten över Azure Resource Manager][lnk-azure-rm-overview] om du vill veta mer om funktionerna i Azure Resource Manager.
* JSON-syntax och egenskaper som ska användas i mallar finns i [Microsoft.Devices resurstyper](/azure/templates/microsoft.devices/iothub-allversions).

Mer information om hur du utvecklar för IoT Hub finns i följande artiklar:

* [Introduktion till C SDK][lnk-c-sdk]
* [Azure IoT SDK:er][lnk-sdks]

Mer information om hur du utforskar funktionerna i IoT Hub finns i:

* [Distribuera AI till gränsenheter med Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
