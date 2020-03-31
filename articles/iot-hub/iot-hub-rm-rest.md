---
title: Skapa en Azure IoT-hubb med hjälp av RESURSPROVIDERN REST API | Microsoft-dokument
description: Lär dig hur du använder resursprovidern C# REST API för att skapa och hantera ett IoT Hub programmässigt.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: c4cb230c9f0b56e3ff9d81e0d85134a7f192e6e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429165"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Skapa en IoT-hubb med hjälp av RESURSPROVIDERN REST API (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Du kan använda [REST-API:ET för IoT Hub-resursprovidern](https://docs.microsoft.com/rest/api/iothub/iothubresource) för att skapa och hantera Azure IoT-hubbar programmässigt. Den här självstudien visar hur du använder REST API:et för IoT Hub-resursprovidern för att skapa en IoT-hubb från ett C#-program.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio.

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

* [Azure PowerShell 1.0](https://docs.microsoft.com/powershell/azure/install-Az-ps) eller senare.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Förbereda visual studio-projektet

1. Skapa ett Visual C# Windows Classic Desktop-projekt i Visual Studio med projektmallen **Console App (.NET Framework).** Ge projektet namnet **CreateIoTHubREST**.

2. Högerklicka på projektet i Solution Explorer och klicka sedan på **Hantera NuGet-paket**.

3. I NuGet Package Manager kontrollerar du **Inkludera förhandsversion och**sök efter **Microsoft.Azure.Management.ResourceManager**på sidan **Bläddra.** Markera paketet, klicka på **Installera**, i **Granska ändringar** klicka på **OK**och klicka sedan på **Jag accepterar** för att acceptera licenserna.

4. Sök efter **Microsoft.IdentityModel.Clients.ActiveDirectory**i NuGet Package Manager .  Klicka på **Installera**i **Granska ändringar** på **OK**och sedan på **Jag accepterar** för att acceptera licensen.

5. I Program.cs ersätter du befintliga **med hjälp av** satser med följande kod:

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

6. I Program.cs lägger du till följande statiska variabler som ersätter platshållarvärdena. Du har antecknat **ApplicationId,** **SubscriptionId**, **TenantId**och **Lösenord** tidigare i den här självstudien. **Resursgruppsnamn** är namnet på den resursgrupp som du använder när du skapar IoT-hubben. Du kan använda en befintlig eller en ny resursgrupp. **IoT Hub-namnet** är namnet på den IoT-hubb som du skapar, till exempel **MyIoTHub**. Namnet på din IoT-hubb måste vara globalt unikt. **Distributionsnamn** är ett namn för distributionen, till exempel **Deployment_01**.

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

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Använda RESURSPROVIDERN REST API för att skapa en IoT-hubb

Använd [REST-API:ET för IoT Hub-resursleverantören](https://docs.microsoft.com/rest/api/iothub/iothubresource) för att skapa en IoT-hubb i resursgruppen. Du kan också använda RESURSPROVIDERN REST API för att göra ändringar i en befintlig IoT-hubb.

1. Lägg till följande metod i Program.cs:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Lägg till följande kod i **Metoden CreateIoTHub.** Den här koden skapar ett **HttpClient-objekt** med autentiseringstoken i rubrikerna:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Lägg till följande kod i **Metoden CreateIoTHub.** Den här koden beskriver IoT-hubben för att skapa och genererar en JSON-representation. För den aktuella listan över platser som stöder IoT Hub se [Azure Status:](https://azure.microsoft.com/status/)

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

4. Lägg till följande kod i **Metoden CreateIoTHub.** Den här koden skickar REST-begäran till Azure. Koden kontrollerar sedan svaret och hämtar den URL som du kan använda för att övervaka distributionsaktivitetens tillstånd:

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

5. Lägg till följande kod i slutet av **CreateIoTHub-metoden.** Den här koden använder **asyncStatusUri-adressen** som hämtats i föregående steg för att vänta på att distributionen ska slutföras:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Lägg till följande kod i slutet av **CreateIoTHub-metoden.** Den här koden hämtar nycklarna till IoT-hubben som du skapade och skriver ut dem till konsolen:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Slutför och kör programmet

Du kan nu slutföra programmet genom att anropa **CreateIoTHub-metoden** innan du skapar och kör den.

1. Lägg till följande kod i **Main** slutet av huvudmetoden:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Klicka på **Bygg** och sedan **bygg lösning**. Korrigera eventuella fel.

3. Klicka på **Felsöka** och sedan **starta felsökning** för att köra programmet. Det kan ta flera minuter innan distributionen körs.

4. Om du vill verifiera att ditt program har lagt till den nya IoT-hubben besöker du [Azure-portalen](https://portal.azure.com/) och visar din lista över resurser. Du kan också använda Cmdleten **Get-AzResource** PowerShell.

> [!NOTE]
> Det här exemplet läggs till en S1 Standard IoT Hub som du faktureras för. När du är klar kan du ta bort IoT-hubben via [Azure-portalen](https://portal.azure.com/) eller med hjälp av **Cmdlet Remove-AzResource** PowerShell när du är klar.

## <a name="next-steps"></a>Nästa steg

Nu har du distribuerat en IoT-hubb med hjälp av resursprovidern REST API, kanske du vill utforska ytterligare:

* Läs om funktionerna i [IoT Hub-resursprovidern REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource).

* Läs [översikten över Azure Resource Manager](../azure-resource-manager/management/overview.md) om du vill veta mer om funktionerna i Azure Resource Manager.

Mer information om hur du utvecklar för IoT Hub finns i följande artiklar:

* [Introduktion till C SDK](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK:er](iot-hub-devguide-sdks.md)

Mer information om hur du utforskar funktionerna i IoT Hub finns i:

* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)