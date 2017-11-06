---
title: Skapa en Azure IoT-Edge-modul med C# | Microsoft Docs
description: "Den här kursen visar hur du skriver en TIVERA data konverteraren modul med de senaste Azure IoT kant NuGet-paket, Visual Studio Code och C#."
services: iot-hub
author: jeffreyCline
manager: timlt
keywords: "Azure iot, självstudier, modul, nuget, vscode, csharp, kant"
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: jcline
ms.openlocfilehash: 7175ffc8de2c043593d61143b402484d33e4a8cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-iot-edge-module-with-cx23"></a>Skapa en Azure IoT-Edge-modul med C & #x23;

Den här kursen visar hur du skapar en modul för `Azure IoT Edge` med `Visual Studio Code` och `C#`.

I den här självstudiekursen kommer vi att gå igenom miljön inställning och hur du skriver en [TIVERA](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) data konverteraren modul med senast `Azure IoT Edge NuGet` paket. 

>[!NOTE]
Den här kursen använder den `.NET Core SDK`, som har stöd för flera plattformar kompatibilitet. Följande självstudierna är skrivna med hjälp av den `Windows 10` operativsystem. Vissa kommandon i den här kursen kan vara olika beroende på din `development environment`. 

## <a name="prerequisites"></a>Krav

I det här avsnittet vi ställa in miljön för `Azure IoT Edge` modulen utveckling. Det gäller både **64-bitars Windows** och **64-bitars Linux (8 Ubuntu/Debian)** operativsystem.

Följande programvara krävs:

- [Git-klient](https://git-scm.com/downloads)
- [.NET Core SDK](https://www.microsoft.com/net/core#windowscmd)
- [Visual Studio Code](https://code.visualstudio.com/)

Du behöver inte klona lagringsplatsen för det här exemplet, men alla exempelkoden som beskrivs i den här kursen finns i följande lagringsplats:

- `git clone https://github.com/Azure-Samples/iot-edge-samples.git`.
- `cd iot-edge-samples/dotnetcore/simulated_ble`

## <a name="getting-started"></a>Komma igång

1. Installera `.NET Core SDK`.
2. Installera `Visual Studio Code` och `C# extension` från Visual Studio Code Marketplace.

Visa den här [snabb självstudiekurs](https://channel9.msdn.com/Blogs/dotnet/Get-started-VSCode-Csharp-NET-Core-Windows) om hur du kommer igång med `Visual Studio Code` och `.NET Core SDK`.

## <a name="creating-the-azure-iot-edge-converter-module"></a>Azure IoT kant modulen konverteraren

1. Initiera en ny `.NET Core` C# klassbiblioteksprojektet:
    - Öppna en kommandotolk (`Windows + R` -> `cmd` -> `enter`).
    - Navigera till mappen där du vill skapa den `C#` projekt.
    - Typen **dotnet nya classlib -o IoTEdgeConverterModule -f netstandard1.3**. 
    - Det här kommandot skapar en tom klass som heter `Class1.cs` i projektkatalogen.
2. Navigera till mappen där vi just skapade klassbiblioteksprojektet genom att skriva **cd IoTEdgeConverterModule**.
3. Öppna projektet i `Visual Studio Code` genom att skriva **kod.**.
4. När projektet har öppnats i `Visual Studio Code`, klicka på den **IoTEdgeConverterModule.csproj** att öppna filen som visas i följande bild:

    ![Visual Studio Code redigeringsfönstret](media/iot-hub-iot-edge-create-module/vscode-edit-csproj.png)

5. Infoga den `XML` blob som visas i följande kodavsnitt mellan stängningen `PropertyGroup` taggen och stängningen `Project` tagga; rad sex i den föregående bilden och spara filen genom att trycka på `Ctrl`  +  `S`.

   ```xml
     <ItemGroup>
       <PackageReference Include="Microsoft.Azure.Devices.Gateway.Module.NetStandard" Version="1.0.5" />
       <PackageReference Include="System.Threading.Thread" Version="4.3.0" />
       <PackageReference Include="Newtonsoft.Json" Version="10.0.2" />
     </ItemGroup> 
   ```

6. När du sparar den `.csproj` filen `Visual Studio Code` fråga ska visas med en `unresolved dependencies` dialogrutan som visas i följande bild: 

    ![Visual Studio Code återställning beroenden dialogrutan](media/iot-hub-iot-edge-create-module/vscode-restore.png)

    en) Klicka på `Restore` att återställa alla referenser i projekten `.csproj` filen inklusive den `PackageReferences` vi har lagt till. 

    b) `Visual Studio Code` skapar automatiskt den `project.assets.json` filen i projektet `obj` mapp. Den här filen innehåller information om projektberoenden att göra efterföljande återställningar snabbare.
 
    >[!NOTE]
    `.NET Core Tools`är nu MSBuild-baserade. Vilket innebär en `.csproj` projektfilen har skapats i stället för en `project.json`.

    - Om `Visual Studio Code` visas inte som är det ok, vi kan göra det manuellt. Öppna den `Visual Studio Code` integrerad terminalfönster genom att trycka på `Ctrl`  +  `backtick` nycklar eller använda menyerna `View`  ->  `Integrated Terminal`.
    - I den `Integrated Terminal` fönstret typen **dotnet återställning**.
    
7. Byt namn på den `Class1.cs` filen till `BleConverterModule.cs`. 

    en) för att byta namn på filen först klickar du på filen tryck sedan på den `F2` nyckel.
    
    b) typ i det nya namnet **BleConverterModule**, som visas i följande bild:

    ![Visual Studio Code byta namn på en klass](media/iot-hub-iot-edge-create-module/vscode-rename.png)

8. Ersätt den befintliga koden i den `BleConverterModule.cs` filen genom att kopiera och klistra in följande kodfragment i din `BleConverterModule.cs` fil.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Globalization;
   using System.Linq;
   using System.Text;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace IoTEdgeConverterModule
   {
       public class BleConverterModule : IGatewayModule, IGatewayModuleStart
       {
           private Broker broker;
           private string configuration;
           private int messageCount;

           public void Create(Broker broker, byte[] configuration)
           {
               this.broker = broker;
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Start()
           {
           }

           public void Destroy()
           {
           }

           public void Receive(Message received_message)
           {
               string recMsg = Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               BleData receivedData = JsonConvert.DeserializeObject<BleData>(recMsg);

               float temperature = float.Parse(receivedData.Temperature, CultureInfo.InvariantCulture.NumberFormat); 
               Dictionary<string, string> receivedProperties = received_message.Properties;
            
               Dictionary<string, string> properties = new Dictionary<string, string>();
               properties.Add("source", receivedProperties["source"]);
               properties.Add("macAddress", receivedProperties["macAddress"]);
               properties.Add("temperatureAlert", temperature > 30 ? "true" : "false");
   
               String content = String.Format("{0} \"deviceId\": \"Intel NUC Gateway\", \"messageId\": {1}, \"temperature\": {2} {3}", "{", ++this.messageCount, temperature, "}");
               Message messageToPublish = new Message(content, properties);
   
               this.broker.Publish(messageToPublish);
           }
       }
   }
   ```

9. Spara filen genom att trycka på `Ctrl`  +  `S`.

10. Skapa en ny fil med namnet `Untitled-1` genom att trycka på `Ctrl`  +  `N` nycklar som visas i följande bild:

    ![Visual Studio Code ny fil](media/iot-hub-iot-edge-create-module/vscode-new-file.png)

11. Att deserialisera den `JSON` objekt som vi får från det simulerade `BLE` enhet, kopiera in följande kod i den `Untitled-1` filen kod editor-fönstret. 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace IoTEdgeConverterModule
   {
       public class BleData
       {
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

12. Spara filen som `BleData.cs` genom att trycka på `Ctrl`  +  `Shift`  +  `S` nycklar.
    - På den dialogrutan Spara som, i den `Save as Type` väljer du `C# (*.cs;*.csx)` som visas i följande bild:

    ![Visual Studio Code Spara som dialog](media/iot-hub-iot-edge-create-module/vscode-save-as.png)

13. Skapa en ny fil med namnet `Untitled-1` genom att trycka på `Ctrl`  +  `N` nycklar.

14. Kopiera och klistra in följande kodavsnitt i den `Untitled-1` filen. Den här klassen är en `Azure IoT Edge` module, som vi använder för att spara data togs emot från våra `BleConverterModule`.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace PrinterModule
   {
       public class DotNetPrinterModule : IGatewayModule
       {
           private string configuration;
           public void Create(Broker broker, byte[] configuration)
           {
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Destroy()
           {
           }
   
           public void Receive(Message received_message)
           {
               string recMsg = System.Text.Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               Dictionary<string, string> receivedProperties = received_message.Properties;
               
               BleConverterData receivedData = JsonConvert.DeserializeObject<BleConverterData>(recMsg);
   
               Console.WriteLine();
               Console.WriteLine("Module           : [DotNetPrinterModule]");
               Console.WriteLine("received_message : {0}", recMsg);
   
               if(received_message.Properties["source"] == "bleTelemetry")
               {
                   Console.WriteLine("Source           : {0}", receivedProperties["source"]);
                   Console.WriteLine("MAC address      : {0}", receivedProperties["macAddress"]);
                   Console.WriteLine("Temperature Alert: {0}", receivedProperties["temperatureAlert"]);
                   Console.WriteLine("Deserialized Obj : [BleConverterData]");
                   Console.WriteLine("  DeviceId       : {0}", receivedData.DeviceId);
                   Console.WriteLine("  MessageId      : {0}", receivedData.MessageId);
                   Console.WriteLine("  Temperature    : {0}", receivedData.Temperature);
               }
   
               Console.WriteLine();
           }
       }
   }
   ```

15. Spara filen som `DotNetPrinterModule.cs` genom att trycka på `Ctrl`  +  `Shift`  +  `S`.
    - På den dialogrutan Spara som, i den `Save as Type` väljer du `C# (*.cs;*.csx)`.

16. Skapa en ny fil med namnet `Untitled-1` genom att trycka på `Ctrl`  +  `N` nycklar.

17. Att deserialisera den `JSON` objekt som vi får från den `BleConverterModule`, kopiera och klistra in följande kod fragment till den `Untitled-1` filen. 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace PrinterModule
   {
       public class BleConverterData
       {
           [JsonProperty(PropertyName = "deviceId")]
           public string DeviceId { get; set; }
   
           [JsonProperty(PropertyName = "messageId")]
           public string MessageId { get; set; }
   
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

18. Spara filen som `BleConverterData.cs` genom att trycka på `Ctrl`  +  `Shift`  +  `S`.
    - På den dialogrutan Spara som, i den `Save as Type` väljer du `C# (*.cs;*.csx)`.

19. Skapa en ny fil med namnet `Untitled-1` genom att trycka på `Ctrl`  +  `N` nycklar.

20. Kopiera och klistra in följande kodavsnitt i den `Untitled-1` filen.

   ```json
   {
       "loaders": [
           {
               "type": "dotnetcore",
               "name": "dotnetcore",
               "configuration": {
                   "binding.path": "dotnetcore.dll",
                   "binding.coreclrpath": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\coreclr.dll",
                   "binding.trustedplatformassemblieslocation": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\"
               }
           }
       ],
          "modules": [
           {
               "name": "simulated_device",
               "loader": {
                   "name": "native",
                   "entrypoint": {
                       "module.path": "simulated_device.dll"
                   }
               },
               "args": {
                   "macAddress": "01:02:03:03:02:01",
                   "messagePeriod": 500
               }
           },
           {
               "name": "ble_converter_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "IoTEdgeConverterModule.BleConverterModule"
                   }
               },
               "args": ""
           },
           {
               "name": "printer_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "PrinterModule.DotNetPrinterModule"
                   }
               },
               "args": ""
           }
       ],
       "links": [
           {
               "source": "simulated_device", "sink": "ble_converter_module"
           },
           {
               "source": "ble_converter_module", "sink": "printer_module"
           }
   ]
   }
   ```

21. Spara filen som `gw-config.json` genom att trycka på `Ctrl`  +  `Shift`  +  `S`.
    - På den dialogrutan Spara som, i den `Save as Type` väljer du `JSON (*.json;*.bowerrc;*.jshintrc;*.jscsrc;*.eslintrc;*.babelrc;*webmanifest)`.

22. Om du vill aktivera kopiering av konfigurationsfilen till den angivna katalogen, uppdatera det `IoTEdgeConverterModule.csproj` med följande XML-blob:

   ```xml
     <ItemGroup>
       <None Update="gw-config.json" CopyToOutputDirectory="PreserveNewest" />
     </ItemGroup>
   ```
    
   - Den uppdaterade `IoTEdgeConverterModule.csproj` bör se ut som följande bild:

    ![Visual Studio Code uppdaterade .csproj fil](media/iot-hub-iot-edge-create-module/vscode-update-csproj.png)

23. Skapa en ny fil med namnet `Untitled-1` genom att trycka på `Ctrl`  +  `N` nycklar.

24. Kopiera och klistra in följande kodavsnitt i den `Untitled-1` filen.

   ```powershell
   Copy-Item -Path $env:userprofile\.nuget\packages\microsoft.azure.devices.gateway.native.windows.x64\1.1.3\runtimes\win-x64\native\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.formatters\4.3.0\lib\netstandard1.4\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.primitives\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\newtonsoft.json\10.0.2\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.componentmodel.typeconverter\4.3.0\lib\netstandard1.5\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.nongeneric\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.specialized\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   ```

25. Spara filen som `binplace.ps1` genom att trycka på `Ctrl`  +  `Shift`  +  `S`.
    - På den dialogrutan Spara som, i den `Save as Type` väljer du `PowerShell (*.ps1;*.psm1;*.psd1;*.pssc;*.psrc)`.

26. Skapa projektet genom att trycka på `Ctrl`  +  `Shift`  +  `B` nycklar. När du skapar projektet för första gången `Visual Studio Code` uppmanar dig med de `No build task defined.` dialogrutan som visas i följande bild:

    ![Visual Studio Code build-aktivitetsdialogrutan](media/iot-hub-iot-edge-create-module/vscode-build-task.png)

    en) Klicka på den `Configure Build Task` knappen.

    b) i den `Select a Task Runner` dialogrutan listrutan. Välj `.NET Core` som visas i följande bild: 

    ![Visual Studio Code väljer en aktivitetsdialogrutan](media/iot-hub-iot-edge-create-module/vscode-build-task-runner.png)

    c) Klicka på den `.NET Core` objekt skapar den `tasks.json` filen i din `.vscode` directory och öppnar filen i den `code editor` fönster. Det finns inget behov av att ändra den här filen, Stäng fliken.

27.  Öppna den `Visual Studio Code` integrerad terminalfönster genom att trycka på `Ctrl`  +  `backtick` nycklar eller använda menyerna `View`  ->  `Integrated Terminal` och skriv **.\binplace.ps1** i den `PowerShell` kommandotolk. Det här kommandot kopierar alla våra beroenden till den angivna katalogen.

28. Navigera till utdatakatalogen projekt i den `Integrated Terminal` fönster genom att skriva **cd.\bin\Debug\netstandard1.3**.

29. Köra exempelprojektet genom att skriva **. \gw.exe gw-config.json** till den `Integrated Terminal` fönstret Kommandotolken. 
    - Om du har följt stegen i den här självstudiekursen noggrant kan du nu att köra den `Azure IoT Edge BLE Data Converter Module` exempelprojektet som visas i följande bild:
    
        ![Simulerade enheten exempel körs i Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-run.png)
    
    - Om du vill avsluta programmet trycker du på den `<Enter>` nyckel.

>[!IMPORTANT]
Det rekommenderas inte att använda `Ctrl`  +  `C` att avsluta den `IoT Edge` gateway-program (det vill säga **gw.exe**). Eftersom den här åtgärden kan orsaka processen att avbrytas onormalt.

