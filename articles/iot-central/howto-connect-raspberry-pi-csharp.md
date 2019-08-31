---
title: Anslut en Raspberry Pi till ditt Azure IoT Central-programC#() | Microsoft Docs
description: Som enhets utvecklare kan du ansluta en Raspberry Pi till ditt Azure IoT Central-program med C#hjälp av.
author: viv-liu
ms.author: viviali
ms.date: 04/15/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 9395fa5ba5ae8dfa6ff03aabd1a27942ca4fa60a
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195184"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Anslut en Raspberry Pi till ditt Azure IoT Central-programC#()

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

I den här artikeln beskrivs hur, som enhets utvecklare, för att ansluta en Raspberry Pi till ditt Microsoft Azure IoT Central C# -program med hjälp av programmeringsspråket.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra stegen i den här artikeln behöver du följande komponenter:

* Ett Azure IoT Central-program som skapats från programmallen **exempel Devkits** . Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
* En Raspberry Pi-enhet som kör operativ systemet Raspbian. Raspberry Pi måste kunna ansluta till Internet. Mer information finns i [Konfigurera din Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>**Exempel på Devkits** -program

Ett program som har skapats från **Devkits** program mal len innehåller en **Raspberry Pi** -enhet med följande egenskaper:

- Telemetri, som omfattar följande mätningar som samlas in av enheten:
  - Fuktighet
  - Temperatur
  - Tryck
  - Magnetometer (X, Y, Z)
  - Accelerometer (X, Y, Z)
  - Gyroscope (X, Y, Z)
- Inställningar
  - Strömförsörjning
  - Aktuell
  - Fläkt hastighet
  - IR-växling.
- properties
  - Enhets egenskap för Die Number
  - Plats moln egenskap

Fullständig information om hur du konfigurerar enhets mal len finns i [Raspberry Pi Device Template information](#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Lägga till en riktig enhet

I ditt Azure IoT Central-program lägger du till en riktig enhet från enhets mal len **Raspberry Pi** . Anteckna enhetens anslutnings information (**scope-ID**, enhets **-ID**och **primär nyckel**). Mer information finns i [lägga till en riktig enhet till ditt Azure IoT Central-program](tutorial-add-device.md).

### <a name="create-your-net-application"></a>Skapa ditt .NET-program

Du skapar och testar enhets programmet på din station ära dator.

Du kan använda Visual Studio Code för att utföra följande steg. Mer information finns i [arbeta med C# ](https://code.visualstudio.com/docs/languages/csharp).

> [!NOTE]
> Om du vill kan du utföra följande steg med en annan kod redigerare.

1. Starta .NET-projektet och Lägg till de nödvändiga NuGet-paketen genom att köra följande kommandon:

   ```cmd/sh
   mkdir pisample
   cd pisample
   dotnet new console
   dotnet add package Microsoft.Azure.Devices.Client
   dotnet restore
   ```

1. `pisample` Öppna mappen i Visual Studio Code. Öppna sedan projekt filen **pisample. CSPROJ** . Lägg till `<RuntimeIdentifiers>` den tagg som visas i följande kodfragment:

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
    > Versions numret för **Microsoft. Azure. devices. client** -paketet kan vara högre än det som visas.

1. Spara **pisample. CSPROJ**. Om Visual Studio Code efterfrågar dig att köra kommandot Restore väljer du **Återställ**.

1. Öppna **program.cs** och ersätt innehållet med följande kod:

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
    > Du uppdaterar plats hållaren `{your device connection string}` i nästa steg.

## <a name="run-your-net-application"></a>Kör ditt .NET-program

Lägg till din enhets-/regionsspecifika anslutnings sträng till koden för enheten för att autentisera med Azure IoT Central. Följ dessa anvisningar för att [generera enhets anslutnings strängen](howto-generate-connection-string.md) med hjälp av omfångs **-ID**, **enhets-ID**och **primär nyckel** som du antecknade tidigare.

1. Ersätt `{your device connection string}` i **program.cs** -filen med den anslutnings sträng som du skapade.

1. Kör följande kommando i kommando rads miljön:

   ```cmd/sh
   dotnet restore
   dotnet publish -r linux-arm
   ```

1. `pisample\bin\Debug\netcoreapp2.1\linux-arm\publish` Kopiera mappen till din Raspberry Pi-enhet. Du kan använda **SCP** -kommandot för att kopiera filerna, till exempel:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    Mer information finns i [Raspberry Pi Remote Access](https://www.raspberrypi.org/documentation/remote-access/).

1. Logga in på din Raspberry Pi-enhet och kör följande kommandon i ett gränssnitt:

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. Kör följande kommandon på din Raspberry Pi:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![Programmet börjar](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. I ditt Azure IoT Central-program kan du se hur koden som körs på Raspberry Pi interagerar med programmet:

   * På sidan **mått** för din riktiga enhet kan du se Telemetrin.
   * På sidan **Egenskaper** kan du se värdet för egenskapen rapporterade **tärnings nummer** .
   * På sidan **Inställningar** kan du ändra olika inställningar för Raspberry Pi, till exempel spänning och fläkt hastighet.

     Följande skärm bild visar Raspberry Pi som tar emot inställnings ändringen:

     ![Raspberry Pi tar emot inställnings ändring](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="raspberry-pi-device-template-details"></a>Information om Raspberry Pi-enhet

Ett program som har skapats från **Devkits** program mal len innehåller en **Raspberry Pi** -enhet med följande egenskaper:

### <a name="telemetry-measurements"></a>Mått för telemetri

| Fältnamn     | Enheter  | Minimum | Maximal | Antal decimaler |
| -------------- | ------ | ------- | ------- | -------------- |
| luftfuktighet       | %      | 0       | 100     | 0              |
| styr           | °C     | -40     | 120     | 0              |
| tryck       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | – 1000   | 1000    | 0              |
| magnetometerY  | mgauss | – 1000   | 1000    | 0              |
| magnetometerZ  | mgauss | – 1000   | 1000    | 0              |
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
| Strömförsörjning      | setVoltage | Volt | 0              | 0       | 240     | 0       |
| Aktuell      | setCurrent | A  | 0              | 0       | 100     | 0       |
| Fläkt hastighet    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Växla inställningar

| Visningsnamn | Fältnamn | På text | Av text | Inledande |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | PÅ      | AV      | Av     |

### <a name="properties"></a>properties

| type            | Visningsnamn | Fältnamn | Datatyp                              |
| --------------- | ------------ | ---------- | -------------------------------------- |
| Enhets egenskap | Tärnings nummer   | dieNumber  | nummer                                 |
| Location        | Location     | location   | {Lat: Float, Long: Float, alt?: Float} |

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en Raspberry Pi till ditt Azure IoT Central-program är det föreslagna nästa steg att lära dig hur du [konfigurerar en anpassad enhets mall](howto-set-up-template.md) för din egen IoT-enhet.
