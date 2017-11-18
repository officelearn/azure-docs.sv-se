---
title: Skapa en Azure IoT-hubb med resursprovidern REST API | Microsoft Docs
description: "Hur du använder resursprovidern REST API för att skapa en IoT-hubb."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 52814ee5-bc10-4abe-9eb2-f8973096c2d8
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: d9372f8345257c45ae6b3b915383788f698a0e35
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Skapa en IoT-hubb med resursprovidern REST API (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Du kan använda den [IoT-hubb resursprovidern REST API] [ lnk-rest-api] att skapa och hantera Azure IoT-hubbar programmässigt. Den här kursen visar hur du skapar en IoT-hubb från ett C#-program med hjälp av resursprovidern IoT Hub REST API.

> [!NOTE]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Azure Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md).  Den här artikeln täcker Azure Resource Manager-distributionsmodellen.

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Ett aktivt Azure-konto. <br/>Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.
* [Azure PowerShell 1.0] [ lnk-powershell-install] eller senare.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Förbered din Visual Studio-projekt

1. I Visual Studio, skapa ett Visual C# klassiska skrivbordet projekt med den **Konsolapp (.NET Framework)** projektmall. Namnge projektet **CreateIoTHubREST**.

2. Högerklicka på projektet i Solution Explorer och klicka sedan på **hantera NuGet-paket**.

3. Kontrollera NuGet Package Manager **inkludera förhandsversion**, och på den **Bläddra** sidan Sök efter **Microsoft.Azure.Management.ResourceManager**. Välj paketet, klicka på **installera**i **granska ändringar** klickar du på **OK**, klicka på **jag accepterar** att acceptera licenser.

4. I NuGet-Pakethanteraren, söka efter **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Klicka på **installera**i **granska ändringar** klickar du på **OK**, klicka på **jag accepterar** att acceptera licensvillkoren.

5. I Program.cs ersätta den befintliga **med** instruktioner med följande kod:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```

6. Lägg till följande statiska variabler ersätta platshållarvärdena i Program.cs. Du antecknade **ApplicationId**, **SubscriptionId**, **TenantId**, och **lösenord** tidigare i den här kursen. **Resursgruppens namn** är namnet på resursgruppen som du använder när du skapar en IoT-hubben. Du kan använda en befintlig eller en ny resursgrupp. **IoT-hubbnamnet** är namnet på IoT-hubb som du skapar, till exempel **MyIoTHub**. Namnet på din IoT-hubb måste vara globalt unika. **Distributionsnamnet** är ett namn för distributionen, till exempel **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";

    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```
[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Använda resursprovidern REST API för att skapa en IoT-hubb

Använd den [IoT-hubb resursprovidern REST API] [ lnk-rest-api] att skapa en IoT-hubb i resursgruppen. Du kan också använda resursprovidern REST API för att göra ändringar i en befintlig IoT-hubb.

1. Lägg till följande metod i Program.cs:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Lägg till följande kod i den **CreateIoTHub** metod. Den här koden skapar en **HttpClient** objekt med autentiseringstoken i huvud:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Lägg till följande kod i den **CreateIoTHub** metod. Den här koden beskriver IoT-hubb för att skapa och genererar en JSON-representation. Den aktuella listan över platser som har stöd för IoT-hubb finns [Azure Status][lnk-status]:

    ```csharp
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };

    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Lägg till följande kod i den **CreateIoTHub** metod. Den här koden skickar REST-begäran till Azure. Koden sedan kontrollerar svaret och hämtar URL: en som du kan använda för att övervaka status för aktiviteten distribution:

    ```csharp
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;

    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }

    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Lägg till följande kod i slutet av den **CreateIoTHub** metod. Den här koden används den **asyncStatusUri** adress hämtades i föregående steg att slutföra distributionen:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Lägg till följande kod i slutet av den **CreateIoTHub** metod. Den här koden hämtar nycklarna i IoT hub du skapat och skriver ut dem till konsolen:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Slutför och köra programmet

Du kan nu slutföra programmet genom att anropa den **CreateIoTHub** metoden innan du skapar och kör den.

1. Lägg till följande kod i slutet av den **Main** metoden:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Klicka på **skapa** och sedan **skapa lösning**. Korrigera eventuella fel.

3. Klicka på **felsöka** och sedan **Start Debugging** att köra programmet. Det kan ta flera minuter för att distributionen ska köras.

4. Om du vill verifiera att ditt program till nya IoT-hubben, finns det [Azure-portalen] [ lnk-azure-portal] och visa en lista över resurser. Du kan också använda den **Get-AzureRmResource** PowerShell-cmdlet.

> [!NOTE]
> Det här exempelprogrammet lägger till en S1 Standard IoT-hubb du debiteras. När du är klar kan du ta bort IoT-hubb via den [Azure-portalen] [ lnk-azure-portal] eller genom att använda den **ta bort AzureRmResource** PowerShell-cmdlet när du är klar.

## <a name="next-steps"></a>Nästa steg
Nu du har distribuerat en IoT-hubb med resursprovidern REST-API, kanske du vill utforska vidare:

* Läs mer om funktionerna i den [IoT-hubb resursprovidern REST API][lnk-rest-api].
* Läs [översikt över Azure Resource Manager] [ lnk-azure-rm-overview] att lära dig mer om funktionerna i Azure Resource Manager.

Mer information om hur du utvecklar för IoT-hubb finns i följande artiklar:

* [Introduktion till C SDK][lnk-c-sdk]
* [Azure IoT-SDK][lnk-sdks]

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Distribuera AI till enheter med Azure IoT kant][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
