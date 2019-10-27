---
title: Anslut en Raspberry Pi till ditt Azure IoT Central-programC#() | Microsoft Docs
description: Som enhets utvecklare kan du ansluta en Raspberry Pi till ditt Azure IoT Central-program med C#hjälp av.
author: viv-liu
ms.author: viviali
ms.date: 09/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ba903d75707be91bb8af1271b52eb260ffcde306
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951242"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Anslut en Raspberry Pi till ditt Azure IoT Central-programC#()

[!INCLUDE [howto-raspberrypi-selector](../../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

I den här artikeln beskrivs hur, som enhets utvecklare, för att ansluta en Raspberry Pi till ditt Microsoft Azure IoT Central C# -program med hjälp av programmeringsspråket.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra stegen i den här artikeln behöver du följande komponenter:

* Ett Azure IoT Central-program som skapats från programmallen **exempel Devkits** . Mer information finns i [snabbstarten om att skapa ett program](quick-deploy-iot-central.md).
* En Raspberry Pi-enhet som kör operativ systemet Raspbian. Raspberry Pi måste kunna ansluta till Internet. Mer information finns i [Konfigurera din Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>**Exempel på Devkits** -program

Ett program som har skapats från **Devkits** program mal len innehåller en **Raspberry Pi** -enhet med följande egenskaper:

* Telemetri, som omfattar följande mätningar som samlas in av enheten:
  * Fuktighet
  * Temperatur
  * Berörda
  * Magnetometer (X, Y, Z)
  * Accelerometer (X, Y, Z)
  * Gyroscope (X, Y, Z)
* Inställningar
  * Strömförsörjning
  * Tillfället
  * Fläkt hastighet
  * IR-växling.
* Egenskaper
  * Enhets egenskap för Die Number
  * Plats moln egenskap

Fullständig information om hur du konfigurerar enhets mal len finns i [Raspberry Pi Device Template information](#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Lägga till en riktig enhet

I ditt Azure IoT Central-program lägger du till en riktig enhet från enhets mal len **Raspberry Pi** . Anteckna enhetens anslutnings information (**scope-ID**, **enhets-ID**och **primär nyckel**). Mer information finns i [lägga till en riktig enhet till ditt Azure IoT Central-program](tutorial-add-device.md).

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
   dotnet add package Microsoft.Azure.Devices.Provisioning.Client
   dotnet add package Microsoft.Azure.Devices.Provisioning.Transport.Mqtt
   dotnet restore
   ```

1. Öppna mappen `pisample` i Visual Studio Code. Öppna sedan projekt filen **pisample. CSPROJ** . Lägg till `<RuntimeIdentifiers>`-taggen som visas i följande kodfragment:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <RootNamespace>pisample</RootNamespace>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.21.0" />
        <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Client" Version="1.4.0" />
        <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Transport.Mqtt" Version="1.1.8" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > Paketets versions nummer kan vara högre än de som visas.

1. Spara **pisample. CSPROJ**. Om Visual Studio Code efterfrågar dig att köra kommandot Restore väljer du **Återställ**.

1. Öppna **program.cs** och ersätt innehållet med följande kod. Uppdatera `{your Scope ID}`, `{your Device ID}`och `{your Device Primary Key}` med de värden du antecknade tidigare:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Microsoft.Azure.Devices.Provisioning.Client;
    using Microsoft.Azure.Devices.Provisioning.Client.Transport;

    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string ScopeID = "{your Scope ID}";
        static string DeviceID = "{your Device ID}";
        static string PrimaryKey = "{your Device Primary Key}";
        static string GlobalDeviceEndpoint = "global.azure-devices-provisioning.net";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;

        static async Task Main(string[] args)
        {
          Console.WriteLine("== Raspberry Pi Azure IoT Central example ==");

          try
          {

            using (var security = new SecurityProviderSymmetricKey(DeviceID, PrimaryKey, null))
            {
              DeviceRegistrationResult result = await RegisterDeviceAsync(security);
              if (result.Status != ProvisioningRegistrationStatusType.Assigned) {
                Console.WriteLine("Failed to register device");
                return;
              }
              IAuthenticationMethod auth = new DeviceAuthenticationWithRegistrySymmetricKey(result.DeviceId, (security as SecurityProviderSymmetricKey).GetPrimaryKey());
              Client = DeviceClient.Create(result.AssignedHub, auth, TransportType.Mqtt);
            }

            await SendDevicePropertiesAsync();

            Console.Write("Register settings changed handler...");
            await Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null);
            Console.WriteLine("Done");

            cts = new CancellationTokenSource();
            Task task = SendTelemetryAsync(cts.Token);

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
            cts.Cancel();
            await task;
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine(ex.Message);
          }
        }

        public static async Task<DeviceRegistrationResult> RegisterDeviceAsync(SecurityProviderSymmetricKey security)
        {
            Console.WriteLine("Register device...");

            using (var transport = new ProvisioningTransportHandlerMqtt(TransportFallbackType.TcpOnly))
            {
              ProvisioningDeviceClient provClient =
                        ProvisioningDeviceClient.Create(GlobalDeviceEndpoint, ScopeID, security, transport);

              Console.WriteLine($"RegistrationID = {security.GetRegistrationID()}");


              Console.Write("ProvisioningClient RegisterAsync...");
              DeviceRegistrationResult result = await provClient.RegisterAsync();

              Console.WriteLine($"{result.Status}");
              Console.WriteLine($"ProvisioningClient AssignedHub: {result.AssignedHub}; DeviceID: {result.DeviceId}");

              return result;
            }
        }

        public static async Task SendDevicePropertiesAsync()
        {
            Console.WriteLine("Send device properties...");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }

        private static async Task SendTelemetryAsync(CancellationToken token)
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


        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          Console.WriteLine("Received settings change...");
          Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

          string setting = "fanSpeed";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "setVoltage";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "setCurrent";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "activateIR";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          Console.WriteLine("Send settings changed acknowledgement...");
          await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }

        private static void BuildAcknowledgement(TwinCollection desiredProperties, string setting)
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

## <a name="run-your-net-application"></a>Kör ditt .NET-program

Så här skapar och kör du exempel programmet:

1. Kör följande kommando i kommando rads miljön:

   ```cmd/sh
   dotnet restore
   dotnet publish -r linux-arm
   ```

1. Kopiera `pisample\bin\Debug\netcoreapp2.1\linux-arm\publish`-mappen till din Raspberry Pi-enhet. Du kan använda **SCP** -kommandot för att kopiera filerna, till exempel:

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
| styr           | C     | – 40     | 120     | 0              |
| tryck       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | – 1000   | 1 000    | 0              |
| magnetometerY  | mgauss | – 1000   | 1 000    | 0              |
| magnetometerZ  | mgauss | – 1000   | 1 000    | 0              |
| accelerometerX | MB     | – 2000   | 2000    | 0              |
| accelerometerY | MB     | – 2000   | 2000    | 0              |
| accelerometerZ | MB     | – 2000   | 2000    | 0              |
| gyroscopeX     | mdps   | – 2000   | 2000    | 0              |
| gyroscopeY     | mdps   | – 2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | – 2000   | 2000    | 0              |

### <a name="settings"></a>Inställningar

Numeriska inställningar

| Visningsnamn | Fältnamn | Enheter | Antal decimaler | Minimum | Maximal | Grund |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Strömförsörjning      | setVoltage | Volt | 0              | 0       | 240     | 0       |
| Tillfället      | setCurrent | A  | 0              | 0       | 100     | 0       |
| Fläkt hastighet    | fanSpeed   | VARVTAL   | 0              | 0       | 1 000    | 0       |

Växla inställningar

| Visningsnamn | Fältnamn | På text | Av text | Grund |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | Rund      | Av     |

### <a name="properties"></a>Egenskaper

| Typ            | Visningsnamn | Fältnamn | Datatyp                              |
| --------------- | ------------ | ---------- | -------------------------------------- |
| Enhets egenskap | Tärnings nummer   | dieNumber  | nummer                                 |
| Plats        | Plats     | location   | {Lat: Float, Long: Float, alt?: Float} |

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en Raspberry Pi till ditt Azure IoT Central-program är det föreslagna nästa steg att lära dig hur du [konfigurerar en anpassad enhets mall](howto-set-up-template.md) för din egen IoT-enhet.
