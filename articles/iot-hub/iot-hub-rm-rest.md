---
title: Skapa en Azure IoT hub med REST API för resursprovider | Microsoft Docs
description: Hur du använder REST API för resursprovider för att skapa en IoT Hub.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: d87ca39eb27673be8d5cd0feece3eabe3e214de1
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011100"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Skapa en IoT hub med resource provider REST API (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Du kan använda den [IoT Hub REST API för resursprovider] [ lnk-rest-api] att skapa och hantera Azure-IoT-hubbar programmässigt. Den här självstudien visar hur du använder IoT Hub resource provider REST API för att skapa en IoT hub från ett C#-program.

> [!NOTE]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser:  [Azure Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md).  Den här artikeln beskriver distributionsmodellen Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Ett aktivt Azure-konto. <br/>Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.
* [Azure PowerShell 1.0] [ lnk-powershell-install] eller senare.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Förbered ditt Visual Studio-projekt

1. I Visual Studio skapar ett Visual C# Windows Classic Desktop-projekt med den **Konsolapp (.NET Framework)** projektmall. Ge projektet namnet **CreateIoTHubREST**.

2. Högerklicka på projektet i Solution Explorer och klicka sedan på **hantera NuGet-paket**.

3. I NuGet-Pakethanteraren, kontrollera **inkludera förhandsversion**, och på den **Bläddra** sidan Sök efter **Microsoft.Azure.Management.ResourceManager**. Välj paketet, klicka på **installera**i **granska ändringar** klickar du på **OK**, klicka sedan på **jag accepterar** att acceptera licenser.

4. I NuGet-Pakethanteraren, Sök efter **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Klicka på **installera**i **granska ändringar** klickar du på **OK**, klicka sedan på **jag accepterar** att acceptera licensen.

5. I Program.cs, ersätter den befintliga **med** instruktioner med följande kod:

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

6. Lägg till följande statiska variabler ersätta platshållarvärdena i Program.cs. Du antecknade **ApplicationId**, **SubscriptionId**, **TenantId**, och **lösenord** tidigare i den här självstudien. **Resursgruppens namn** är namnet på resursgruppen som du använder när du skapar IoT-hubben. Du kan använda en befintlig eller ny resursgrupp. **IoT-hubbnamn** är namnet på IoT-hubben som du skapar, till exempel **MyIoTHub**. Namnet på din IoT hub måste vara globalt unikt. **Distributionsnamn** är ett namn för distributionen, till exempel **Deployment_01**.

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

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Använda REST API för resursprovider för att skapa en IoT-hubb

Använd den [IoT Hub REST API för resursprovider] [ lnk-rest-api] att skapa en IoT-hubb i din resursgrupp. Du kan också använda REST API för resursprovider för att göra ändringar i en befintlig IoT-hubb.

1. Lägg till följande metod i Program.cs:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Lägg till följande kod till den **CreateIoTHub** metod. Den här koden skapar en **HttpClient** objekt med autentiseringstoken i sidhuvud:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Lägg till följande kod till den **CreateIoTHub** metod. Den här koden beskriver IoT-hubben som du skapar och genererar en JSON-representation. Den aktuella listan över platser som har stöd för IoT Hub finns [Azure-Status][lnk-status]:

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

4. Lägg till följande kod till den **CreateIoTHub** metod. Den här koden skickar REST-begäran till Azure. Koden sedan kontrollerar svaret och hämtar en URL som du kan använda för att övervaka status för distributionen uppgiften:

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

5. Lägg till följande kod i slutet av den **CreateIoTHub** metod. Den här koden använder den **asyncStatusUri** adress som du hämtade i föregående steg för att vänta tills distributionen har slutförts:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Lägg till följande kod i slutet av den **CreateIoTHub** metod. Den här koden hämtar nycklarna för IoT-hubben som du skapade och skriver dem till konsolen:

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

2. Klicka på **skapa** och sedan **bygg lösning**. Korrigera eventuella fel.

3. Klicka på **felsöka** och sedan **Starta felsökning** att köra programmet. Det kan ta flera minuter för att distributionen ska köra.

4. Kontrollera att ditt program till den nya IoT-hubben genom att gå till den [Azure-portalen] [ lnk-azure-portal] och visa din lista över resurser. Du kan också använda den **Get-AzResource** PowerShell-cmdlet.

> [!NOTE]
> Det här exempelprogrammet lägger till en S1 Standard IoT-hubb som du faktureras. När du är klar tar du bort IoT-hubben via den [Azure-portalen] [ lnk-azure-portal] eller genom att använda den **Remove-AzResource** PowerShell-cmdlet när du är klar.

## <a name="next-steps"></a>Nästa steg
Nu du har distribuerat en IoT hub med REST API för resursprovider, kanske du vill utforska ytterligare:

* Läs mer om funktionerna i den [IoT Hub REST API för resursprovider][lnk-rest-api].
* Läs [översikt över Azure Resource Manager] [ lnk-azure-rm-overview] att lära dig mer om funktionerna för Azure Resource Manager.

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

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
