---
title: Anslut en Raspberry Pi till din Azure IoT Central program (C#) | Microsoft Docs
description: Som utvecklare av enheten, hur du ansluter en Raspberry Pi till Azure IoT Central appen med C#.
author: viv-liu
ms.author: viviali
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8a7b2d15884f7a1cb8b6e72937b0f1e3b6cb8ca9
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59616024"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Ansluta en Raspberry Pi till Azure IoT Central programmet (C#)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Den här artikeln beskrivs hur du som utvecklare enheten att ansluta en Raspberry Pi till ditt Microsoft Azure IoT Central-program med programmeringsspråket C#.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra stegen i den här artikeln, måste följande komponenter:

* Ett Azure IoT Central program som skapats från den **exempel Devkits** mall för program. Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
* En Raspberry Pi-enhet som kör operativsystemet Raspbian. Raspberry Pi måste kunna ansluta till internet. Mer information finns i [konfigurerar Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>**Exempel på Devkits** program

Ett program som skapats från den **exempel Devkits** programmall innehåller en **Raspberry Pi** enheten mallen med följande egenskaper:

- Telemetri, vilket inkluderar följande mått som samlar in enheten:
  - Fuktighet
  - Temperatur
  - Tryck
  - Magnetometer (X, Y, Z)
  - Accelerometer (X, Y, Z)
  - Gyroskop (X, Y, Z)
- Inställningar
  - Spänning
  - Aktuell
  - Fläkthastighet
  - IR växlingsknappen.
- Egenskaper
  - Dör nummer enhetsegenskap
  - Platsegenskapen för molnet

Fullständig information om konfigurationen av enheten mallen finns i [Raspberry Pi enhetsinformation mallen](#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Lägga till en riktig enhet

I Azure IoT Central programmet, lägger du till en riktig enhet från den **Raspberry Pi** enheten mall. Anteckna enheten anslutningsinformation (**Scopeid**, **enhets-ID**, och **primärnyckel**). Mer information finns i [ge en riktig enhet till Azure IoT Central programmet](tutorial-add-device.md).

### <a name="create-your-net-application"></a>Skapa .NET-program

Skapa och testa enheten programmet på din stationära dator.

Du kan använda Visual Studio Code för att slutföra följande steg. Mer information finns i [arbeta med C#](https://code.visualstudio.com/docs/languages/csharp).

> [!NOTE]
> Om du vill kan slutföra du följande steg med hjälp av en annan Kodredigerare.

1. Om du vill initiera ditt .NET-projekt och Lägg till NuGet-paket som krävs, kör du följande kommandon:

   ```cmd/sh
   mkdir pisample
   cd pisample
   dotnet new console
   dotnet add package Microsoft.Azure.Devices.Client
   dotnet restore
   ```

1. Öppna den `pisample` mappen i Visual Studio Code. Öppna sedan den **pisample.csproj** projektfilen. Lägg till den `<RuntimeIdentifiers>` tagg som visas i följande kodavsnitt:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.19.0" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > Din **Microsoft.Azure.Devices.Client** versionsnummer för paketet kan vara högre än den som visas.

1. Save **pisample.csproj**. Om Visual Studio Code uppmanar dig att köra kommandot restore, välja **återställa**.

1. Öppna **Program.cs** och Ersätt innehållet med följande kod:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string DeviceConnectionString = "{your device connection string}";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;
        static void Main(string[] args)
        {
          Console.WriteLine("Raspberry Pi Azure IoT Central example");

          try
          {
            InitClient();
            SendDeviceProperties();

            cts = new CancellationTokenSource();
            SendTelemetryAsync(cts.Token);

            Console.WriteLine("Wait for settings update...");
            Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null).Wait();
            Console.ReadKey();
            cts.Cancel();
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static void InitClient()
        {
          try
          {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static async void SendDeviceProperties()
        {
          try
          {
            Console.WriteLine("Sending device properties:");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static async void SendTelemetryAsync(CancellationToken token)
        {
          try
          {
            Random rand = new Random();

            while (true)
            {
              double currentTemperature = baseTemperature + rand.NextDouble() * 20;
              double currentPressure = basePressure + rand.NextDouble() * 100;
              double currentHumidity = baseHumidity + rand.NextDouble() * 20;

              var telemetryDataPoint = new
              {
                humidity = currentHumidity,
                pressure = currentPressure,
                temp = currentTemperature
              };
              var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
              var message = new Message(Encoding.ASCII.GetBytes(messageString));

              token.ThrowIfCancellationRequested();
              await Client.SendEventAsync(message);

              Console.WriteLine("{0} > Sending telemetry: {1}", DateTime.Now, messageString);

              await Task.Delay(1000);
            }
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Intentional shutdown: {0}", ex.Message);
          }
        }

        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          try
          {
            Console.WriteLine("Received settings change...");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            string setting = "fanSpeed";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setVoltage";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setCurrent";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "activateIR";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }

          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static void AcknowledgeSettingChange(TwinCollection desiredProperties, string setting)
        {
          reportedProperties[setting] = new
          {
            value = desiredProperties[setting]["value"],
            status = "completed",
            desiredVersion = desiredProperties["$version"],
            message = "Processed"
          };
        }
      }
    }
    ```

    > [!NOTE]
    > Du uppdaterar platshållaren `{your device connection string}` i nästa steg.

## <a name="run-your-net-application"></a>Kör .NET-program

Lägga till specifika anslutningssträngen i koden för enheten för att autentisera med Azure IoT Central. Följ de här anvisningarna [generera enhetens anslutningssträng](howto-generate-connection-string.md) med hjälp av den **Scope-ID**, **enhets-ID**, och **primärnyckel** du gjort en Observera av tidigare.

1. Ersätt `{your device connection string}` i den **Program.cs** filen med den anslutningssträng som du skapade.

1. Kör följande kommando i miljön kommandoraden:

   ```cmd/sh
   dotnet restore
   dotnet publish -r linux-arm
   ```

1. Kopiera den `pisample\bin\Debug\netcoreapp2.1\linux-arm\publish` mappen till din Raspberry Pi-enhet. Du kan använda den **scp** kommando för att kopiera filer, till exempel:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    Mer information finns i [Raspberry Pi fjärråtkomst](https://www.raspberrypi.org/documentation/remote-access/).

1. Logga in på Raspberry Pi-enheten och kör följande kommandon i ett gränssnitt:

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. På Raspberry Pi, kör du följande kommandon:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![Programmet börjar](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. Du kan se hur koden körs på Raspberry Pi interagerar med programmet i Azure IoT Central programmet:

   * På den **mätningar** sidan för din riktig enhet visas telemetri.
   * På den **egenskaper** sidan ser du värdet för det rapporterade **dör nummer** egenskapen.
   * På den **inställningar** kan du kan ändra olika inställningar på Raspberry Pi, till exempel spänning och fläkt hastighet.

     I följande skärmbild visas den Raspberry Pi som tar emot ändringen:

     ![Raspberry Pi tar emot Inställningsändringen](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="raspberry-pi-device-template-details"></a>Raspberry Pi mall enhetsinformation

Ett program som skapats från den **exempel Devkits** programmall innehåller en **Raspberry Pi** enheten mallen med följande egenskaper:

### <a name="telemetry-measurements"></a>Telemetri mätning av faktisk användning

| Fältnamn     | Enheter  | Minimum | Maximal | Antal decimaler |
| -------------- | ------ | ------- | ------- | -------------- |
| luftfuktighet       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| tryck       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Inställningar

Numeriska inställningar

| Visningsnamn | Fältnamn | Enheter | Antal decimaler | Minimum | Maximal | Inledande |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Spänning      | setVoltage | V | 0              | 0       | 240     | 0       |
| Aktuell      | setCurrent | A  | 0              | 0       | 100     | 0       |
| Fläkthastighet    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Visa/Dölj inställningar

| Visningsnamn | Fältnamn | Text | Av text | Inledande |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | AV      | Av     |

### <a name="properties"></a>Egenskaper

| Typ            | Visningsnamn | Fältnamn | Datatyp |
| --------------- | ------------ | ---------- | --------- |
| Enhetsegenskap | Dör nummer   | dieNumber  | nummer    |
| Text            | Plats     | location   | Gäller inte       |

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en Raspberry Pi till programmet Azure IoT Central, föreslagna nästa steg är att lära dig hur du [konfigurera en anpassad enhet mall](howto-set-up-template.md) för dina egna IoT-enheter.
