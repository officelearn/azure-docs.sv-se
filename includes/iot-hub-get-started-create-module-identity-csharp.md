---
title: ta med fil
description: ta med fil
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: a5c1ddd085ae65b9920d73f50f993f4646785a69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883727"
---
## <a name="create-a-module-identity"></a>Skapa en modulidentitet

I det här avsnittet skapar du en .NET-konsolapp som skapar en enhetsidentitet och en modulidentitet i identitetsregistret i hubben. En enhet eller modul kan inte ansluta till hubben om den inte har en post i identitetsregistret. Mer information finns i avsnittet om [identitetsregistret i utvecklarhandboken för IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

När du kör den här konsolappen, genereras ett unikt ID och en unik nyckel för både enheten och modulen. Enheten och modulen använder dessa värden för att identifiera sig när de skickar meddelanden från enhet till moln till IoT Hub. ID:n är skiftlägeskänsliga.

1. Öppna Visual Studio och välj **Skapa ett nytt projekt**.

1. Välj **Console App (.NET Framework) i**Skapa ett nytt **projekt**.

1. Välj **Nästa** om du vill öppna **Konfigurera det nya projektet**. Ge projektet namnet *CreateIdentities* och lösningen namnet *IoTHubGetStarted*. Kontrollera att .NET Framework-versionen är 4.6.1 eller senare.

    ![Ange namn och ramverk för visual studio-lösningen](./media/iot-hub-get-started-create-module-identity-csharp/configure-createidentities-project.png)

1. Öppna **Tools** > **NuGet Package Manager** > **Hantera NuGet-paket för lösning**i Visual Studio. Välj fliken **Bläddra**.

1. Sök efter **Microsoft.Azure.Devices**. Markera den och välj sedan **Installera**.

    ![Installera azure IoT Hub .NET-tjänst SDK-aktuell version](./media/iot-hub-get-started-create-module-identity-csharp/install-service-sdk.png)

1. Lägg till `using` följande satser högst upp i **Program.cs-filen:**

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

1. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med IoT Hub-anslutningssträngen som du skapade i föregående avsnitt.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

1. Lägg till följande kod i klassen **Huvud.**

   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

1. Lägg till följande metoder i klassen **Program**:

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    Metoden `AddDeviceAsync` skapar en enhetsidentitet med ID **myFirstDevice**. Om det redan finns enhets-ID i identitetsregistret hämtar koden helt enkelt den befintliga enhetsinformationen. Appen visar sedan den primära nyckeln för den identiteten. Du använder den här nyckeln i den simulerade enhetsappen för att ansluta till navet.

    Metoden `AddModuleAsync` skapar en modul identitet med ID **myFirstModule** under enheten **myFirstDevice**. Om det finns ett modul-ID redan i identitetsregistret hämtar koden helt enkelt den befintliga modulinformationen. Appen visar sedan den primära nyckeln för den identiteten. Du använder den här nyckeln i den simulerade modulappen för att ansluta till din hubb.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Kör den här appen och anteckna enhetsnyckeln och modulnyckeln.

> [!NOTE]
> IoT Hub-identitetsregistret lagrar bara enhets- och modulidentiteter för att möjliggöra säker åtkomst till navet. Enhets-ID:n och nycklar lagras i identitetsregistret och används som autentiseringsuppgifter. I identitetsregistret lagras också en aktiverad/inaktiverad-flagga för varje enhet som du kan använda till att inaktivera enhetens åtkomst. Om din app behöver lagra andra enhetsspecifika metadata bör den använda en programspecifik butik. Det finns ingen aktiverad/inaktiverad flagga för modulidentiteter. Mer information finns i [utvecklarhandboken för IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
