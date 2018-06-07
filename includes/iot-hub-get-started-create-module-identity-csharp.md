---
title: ta med fil
description: ta med fil
services: iot-hub
author: chrissie926
manager: timlt
ms.service: iot-hub
ms.topic: include
ms.date: 04/26/2018
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: d2b409c7454645893665b080b927998402056cdd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34666916"
---
## <a name="create-a-module-identity"></a>Skapa en modulidentitet

I det här avsnittet skapar du en .NET-konsolapp som skapar en enhetsidentitet och en modulidentitet i identitetsregistret i din IoT Hub. Enheter och moduler kan inte ansluta till IoT Hub utan en post i identitetsregistret. Mer information finns i avsnittet om identitetsregistret i [utvecklarhandboken för IoT Hub][lnk-devguide-identity]. När du kör den här konsolappen, genereras ett unikt ID och en unik nyckel för både enheten och modulen. Enheten och modulen använder dessa värden för att identifiera sig vid överföring av enhet-till-moln-meddelanden till IoT Hub. ID:n är skiftlägeskänsliga.


1. **Skapa ett Visual Studio-projekt** – I Visual Studio lägger du till ett Visual C# Windows Classic Desktop-projekt i en ny lösning med hjälp av projektmallen **Console App (.NET Framework)**. Kontrollera att .NET Framework-versionen är 4.6.1 eller senare. Ge projektet namnet **CreateIdentities** och lösningen namnet **IoTHubGetStarted**.

    ![Skapa en Visual Studio-lösning][11]

2. **Installera SDK V1.16.0-preview-001 för Azure IoT Hub .NET-tjänsten** – Modulidentitet och modultvilling är tillgängliga som förhandsversion. Den finns bara tillgänglig i IoT Hub-förhandsversionens tjänst-SDK:er. I Visual Studio öppnar du Verktyg > Nuget-pakethanteraren > Hantera NuGet-paket för lösningen. Sök i Microsoft.Azure.Devices. Se till att du markerar kryssrutan för att inkludera förhandsversion. Välj version 1.1.16.0-preview-001 och installera. Nu har du åtkomst till alla modulfunktioner. 

    ![Installera SDK V1.16.0-preview-001 för Azure IoT Hub .NET-tjänsten][10]

3. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Common.Exceptions;
    ```

4. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med IoT Hub-anslutningssträngen som du skapade i föregående avsnitt.

    ```csharp
    const string connectionString = "<replace_with_iothub_connection_string>";
    const string deviceID = "myFirstDevice";
    const string moduleID = "myFirstModule";
    ```

5. Lägg till följande kod i den **Main** klass.
    ```csharp
    static void Main(string[] args)
    {
        AddDeviceAsync().Wait();
        AddModuleAsync().Wait();
    }
    ```

6. Lägg till följande metoder i klassen **Program**:

    ```csharp
    private static async Task AddDeviceAsync()
    {
        RegistryManager registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        Device device;

        try
        {
            device = await registryManager.AddDeviceAsync(new Device(deviceID));
        }
        catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
            }

            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    Metoden AddDeviceAsync() skapar en enhetsidentitet med ID:t **myFirstDevice**. (Om enhets-ID:t redan finns i identitetsregistret hämtar koden bara den befintliga enhetsinformationen.) Appen visar sedan den primära nyckeln för den identiteten. Du använder den här nyckeln i den simulerade enhetsappen för att ansluta till din IoT Hub.

    Metoden AddModuleAsync() skapar en modulidentitet med ID:t **myFirstModule** under enheten **myFirstDevice**. (Om modul-ID:t redan finns i identitetsregistret, hämtar koden bara den befintliga modulinformationen.) Appen visar sedan den primära nyckeln för den identiteten. Du använder den här nyckeln i den simulerade modulappen för att ansluta till din IoT Hub.

[!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

7. Kör det här programmet och notera enhetsnyckeln och modulnyckeln.

> [!NOTE]
> IoT Hub-identitetsregistret lagrar enhets- och modulidentiteter endast för att skydda åtkomsten till IoT Hub. Enhets-ID:n och nycklar lagras i identitetsregistret och används som autentiseringsuppgifter. I identitetsregistret lagras också en aktiverad/inaktiverad-flagga för varje enhet som du kan använda till att inaktivera enhetens åtkomst. Om ditt program behöver lagra andra enhetsspecifika metadata bör det använda ett programspecifikt datalager. Det finns ingen aktiverad/inaktiverad flagga för modulidentiteter. Mer information finns i [utvecklarhandboken för IoT Hub][lnk-devguide-identity].

<!-- Images. -->
[10]: ./media/iot-hub-get-started-create-module-identity-csharp/install-sdk.png
[11]: ./media/iot-hub-get-started-create-module-identity-csharp/create-identities-csharp1.JPG

<!-- Links -->
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
