---
title: Koppla de olika processtegen en hallon Pi till dina Azure IoT centralt program (C#) | Microsoft Docs
description: Som en enhet utvecklare, hur du ansluter en hallon Pi till dina Azure IoT centralt program med hjälp av C#.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/22/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: d09d3de04f8c846eadc7367ca4d4559eb55f995b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Ansluta en hallon Pi till dina Azure IoT centralt program (C#)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Den här artikeln beskriver hur som utvecklare som enheten kan ansluta en hallon Pi till Microsoft Azure IoT Central programmet med hjälp av programmeringsspråket C#.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver följande för att slutföra stegen i den här artikeln:

* [.NET core 2](https://www.microsoft.com/net) installerat på utvecklingsdatorn. Du bör även ha en lämplig redigerare som [Visual Studio Code](https://code.visualstudio.com/).
* Ett Azure IoT centrala program som skapas från den **exempel Devkits** mall för program. Mer information finns i [skapa Azure IoT centrala programmet](howto-create-application.md).
* En hallon Pi-enhet som kör operativsystemet Raspbian.

Ett program som skapas från den **exempel Devkits** programmall innehåller en **hallon Pi** enheten mallen med följande egenskaper:

### <a name="telemetry-measurements"></a>Telemetri mått

| Fältnamn     | Enheter  | Minimum | Maximal | Decimaler |
| -------------- | ------ | ------- | ------- | -------------- |
| fuktighet       | %      | 0       | 100     | 0              |
| Temp           | ° C     | -40     | 120     | 0              |
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

| Visningsnamn | Fältnamn | Enheter | Decimaler | Minimum | Maximal | Inledande |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Spänning      | setVoltage | V | 0              | 0       | 240     | 0       |
| Aktuell      | setCurrent | A  | 0              | 0       | 100     | 0       |
| Fläkthastighet    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Visa/Dölj inställningar

| Visningsnamn | Fältnamn | På text | Av text | Inledande |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | AV      | Av     |

### <a name="properties"></a>Egenskaper

| Typ            | Visningsnamn | Fältnamn | Datatyp |
| --------------- | ------------ | ---------- | --------- |
| Enhetsegenskap | Dör nummer   | dieNumber  | nummer    |
| Text            | Plats     | location   | Gäller inte       |

### <a name="add-a-real-device"></a>Lägga till en verklig enhet

Azure IoT centrala programmet, lägga till en verklig enhet från den **hallon Pi** enheten mallen och gjort en notering om anslutningssträngen för enheten. Mer information finns i [lägger till en verklig enhet tillämpningsprogrammet Azure IoT Central](tutorial-add-device.md).

## <a name="create-your-net-application"></a>Skapa .NET-program

Skapa och testa programmet enhet på den stationära datorn.

Du kan använda Visual Studio-koden för att slutföra följande steg. Mer information finns i [arbeta med C#](https://code.visualstudio.com/docs/languages/csharp).

> [!NOTE]
> Om du vill kan slutföra du följande steg med en annan redigerare.

1. Om du vill initiera .NET-projektet och Lägg till nödvändiga NuGet-paketen, kör du följande kommandon:

  ```cmd/sh
  mkdir pisample
  cd pisample
  dotnet new console
  dotnet add package Microsoft.Azure.Devices.Client
  dotnet restore
  ```

1. Öppna den `pisample` mapp i Visual Studio-koden. Öppna den **pisample.csproj** projektfilen. Lägg till den `<RuntimeIdentifiers>` tagg som visas i följande utdrag:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.5.2" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > Din **Microsoft.Azure.Devices.Client** versionsnumret kan vara högre än det som visas.

1. Spara **pisample.csproj**. Om Visual Studio Code uppmanas du att köra kommandot restore, välja **återställa**.

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

Lägga till enhetsspecifika anslutningssträngen till kod för enheten för att autentisera med Azure IoT Central. Du antecknade den här anslutningssträngen när du har lagt till enheten verkliga Azure IoT centrala programmet.

1. Ersätt `{your device connection string}` i den **Program.cs** filen med den anslutningssträng som du antecknade tidigare.

1. Kör följande kommando i Kommandotolken miljön:

  ```cmd/sh
  dotnet restore
  dotnet publish -r linux-arm
  ```

1. Kopiera den `pisample\bin\Debug\netcoreapp2.0\linux-arm\publish` mappen på enheten hallon Pi. Du kan använda den **scp** kommando för att kopiera filer, till exempel:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    Mer information finns i [hallon Pi fjärråtkomst](https://www.raspberrypi.org/documentation/remote-access/).

1. Logga in på enheten hallon Pi och kör följande kommandon i ett gränssnitt:

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. Kör följande kommandon på din hallon Pi:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![Programmet börjar](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. Du kan se hur koden körs på hallon Pi interagerar med programmet i ditt Azure IoT centrala program:

    * På den **mätningar** sidan för enheten verkliga kan du se telemetrin.
    * På den **egenskaper** sida, ser du värdet för den rapporterade **dör nummer** egenskapen.
    * På den **inställningar** kan du ändra olika inställningar på hallon Pi, till exempel spänning och fläkt hastighet.

    Följande skärmbild visar den hallon Pi tar emot Inställningsändringen:

    ![Raspberry Pi får inställningen ändring](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en hallon Pi till dina Azure IoT centralt program, är här de föreslagna nästa steg:

* [Ansluta ett allmänt Node.js-klientprogram till Azure IoT Central](howto-connect-nodejs.md)