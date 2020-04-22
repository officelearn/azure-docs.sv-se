---
title: Identiteten för Azure IoT Hub-modulidentitet &-modultvilling (portal och .NET)
description: Lär dig att skapa modulidentitet och uppdatera modultvillingar med portalen och .NET.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp
ms.openlocfilehash: a3258de2ed7269ab50e6feca3c421d55de5a9d91
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759780"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Kom igång med IoT Hub-modulidentitet och modultvilling med portalen och .NET-enhet

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Modulidentiteter och modultvillingar](iot-hub-devguide-module-twins.md) liknar enhetsidentitet och enhetstvilling i Azure IoT Hub, men har en större detaljnivå. Medan Azure IoT Hub-enhetsidentitet och enhetstvilling gör det möjligt för backend-programmet att konfigurera en enhet och ge synlighet på enhetens villkor, tillhandahåller en modulidentitet och modultvilling dessa funktioner för enskilda komponenter på en enhet. På kompatibla enheter med flera komponenter, till exempel operativsystembaserade enheter eller enheter för inbyggd programvara, möjliggör modulidentiteter och modultvillingar isolerad konfiguration och villkor för varje komponent.
>

I den här kursen lär du dig:

* Så här skapar du en modulidentitet i portalen.

* Så här använder du en .NET-enhet SDK för att uppdatera modultvillingen från enheten.

> [!NOTE]
> Information om Azure IoT SDK:er som du kan använda för att skapa båda programmen för att köras på enheter och din lösnings serverdel finns i [Azure IoT SDK: er](iot-hub-devguide-sdks.md).
>

## <a name="prerequisites"></a>Krav

* Visual Studio.

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.

## <a name="create-a-hub"></a>Skapa en hubb

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>Registrera en ny enhet i navet

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Skapa en modulidentitet i portalen

Inom en enhetsidentitet kan du skapa upp till 20 modulidentiteter. Så här lägger du till en identitet:

1. För enheten som du skapade i föregående avsnitt väljer du **Lägg till modulidentitet** för att skapa din första modulidentitet.

1. Ange namnet *myFirstModule*. Spara din modulidentitet.

    ![Lägg till modulidentitet](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    Din nya modulidentitet visas längst ned på skärmen. Välj den för att se information om modulidentitet.

    ![Se information om modulidentitet](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

Spara **anslutningssträngen - primärnyckel**. Du använder den i nästa avsnitt för att ställa in modulen på enheten.

## <a name="update-the-module-twin-using-net-device-sdk"></a>Uppdatera modultvillingen med SDK för .NET-enheter

Du har skapat modulidentiteten i din IoT Hub. Försök kommunicera till molnet från den simulerade enheten. När en modulidentitet har skapats skapas en modultvilling implicit i IoT Hub. I det här avsnittet skapar du en .NET-konsolapp på din simulerade enhet som uppdaterar modultvillingens rapporterade egenskaper.

### <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

Så här skapar du en app som uppdaterar modulens tvillingrapporterade egenskaper:

1. I Visual Studio väljer du **Skapa ett nytt projekt**och väljer sedan **Konsolapp (.NET Framework)** och väljer **Nästa**.

1. I **Konfigurera det nya projektet**anger du *UpdateModuleTwinReportedProperties* som **projektnamn**. Välj **Skapa** för att fortsätta.

    ![Konfigurera dig som ett visuellt studioprojekt](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Installera den senaste Azure IoT Hub .NET-enheten SDK

Modulidentitet och modultvilling är i offentlig förhandsversion. Den är bara tillgänglig i IoT Hub-förutgivningsenhet SDK:er. Så här installerar du den:

1. Öppna **Tools** > **NuGet Package Manager** > **Hantera NuGet-paket för lösning**i Visual Studio.

1. Välj **Bläddra**och välj sedan **Inkludera förhandsversion**. Sök efter *Microsoft.Azure.Devices.Client*. Välj den senaste versionen och installera.

    ![Installera förhandsversionen av Azure IoT Hub .NET-tjänsten SDK](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    Nu har du åtkomst till alla modulfunktioner.

### <a name="get-your-module-connection-string"></a>Hämta modulanslutningssträngen

Du behöver modulens anslutningssträng för konsolappen. Följ de här stegen:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Navigera till din IoT-hubb och välj **IoT-enheter**. Öppna **myFirstDevice** och du ser att **myFirstModule** skapades.

1. Välj **myFirstModule** under **Modulidentiteter**. Kopiera **anslutningssträngen (primärnyckeln)** i **Modulidentitetsinformation**.

    ![Information om Azure-portalmodulen](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>Skapa konsolapp för UpdateModuleTwinReportedProperties

Så här skapar du appen:

1. Lägg till `using` följande satser högst upp i **Program.cs-filen:**

  ```csharp
  using Microsoft.Azure.Devices.Client;
  using Microsoft.Azure.Devices.Shared;
  using Newtonsoft.Json;
  ```

2. Lägg till följande fält i klassen **Program**. Ersätt platshållarens värde med modulens anslutningssträng.

  ```csharp
  private const string ModuleConnectionString = "<Your module connection string>";
  private static ModuleClient Client = null;
  ```

3. Lägg till följande metod **OnDesiredPropertyChanged** till klassen **Program**:

  ```csharp
  private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
      {
          Console.WriteLine("desired property change:");
          Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
          Console.WriteLine("Sending current time as reported property");
          TwinCollection reportedProperties = new TwinCollection
          {
              ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
          };
  
          await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
      }
  ```

4. Slutligen ersätter **du huvudmetoden** med följande kod:

  ```csharp
  static void Main(string[] args)
  {
      Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;
  
      try
      {
          Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
          Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
          Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();
  
          Console.WriteLine("Retrieving twin");
          var twinTask = Client.GetTwinAsync();
          twinTask.Wait();
          var twin = twinTask.Result;
          Console.WriteLine(JsonConvert.SerializeObject(twin));
  
          Console.WriteLine("Sending app start time as reported property");
          TwinCollection reportedProperties = new TwinCollection();
          reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;
  
          Client.UpdateReportedPropertiesAsync(reportedProperties);
      }
      catch (AggregateException ex)
      {
          Console.WriteLine("Error in sample: {0}", ex);
      }
  
      Console.WriteLine("Waiting for Events.  Press enter to exit...");
      Console.ReadKey();
      Client.CloseAsync().Wait();
  }
  
  private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
  {
      Console.WriteLine($"Status {status} changed: {reason}");
  }
  ```
  
  Du kan skapa och köra den här appen med hjälp av **F5**.

Det är kodexemplet visar hur du hämtar modultvillingen och uppdaterar rapporterade egenskaper med AMQP-protokollet. I offentlig förhandsversion stöder vi endast AMQP för modultvillingåtgärder.

## <a name="next-steps"></a>Nästa steg

För att fortsätta komma igång med IoT-hubb och utforska andra IoT-scenarier, se:

* [Kom igång med IoT Hub-modulidentitet och modultvilling med .NET-säkerhetskopiering och .NET-enhet](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Komma igång med IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
