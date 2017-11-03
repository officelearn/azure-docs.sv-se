---
title: "Använda Azure IoT Hub direkt metoder (.NET/nod) | Microsoft Docs"
description: "Hur du använder Azure IoT Hub direkt metoder. Du kan använda Azure IoT-enhet SDK för Node.js för att implementera en simulerad enhetsapp som innehåller en direkt metod och Azure IoT SDK för .NET att implementera ett service-appen som anropar metoden direkt."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2017
ms.author: nberdy
ms.openlocfilehash: 76f1d32b4afeacae1488b4cf28be6c8cf7f4ea37
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-netnode"></a>Använda direkt metoder (.NET/nod)
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

I den här kursen ska vi ta fram en .NET och Node.js-konsolapp:

* **CallMethodOnDevice.sln**, en .NET backend-app, som anropar en metod i appen simulerade enheten och visar svaret.
* **SimulatedDevice.js**, en Node.js-app som simulerar en enhet som ansluter till din IoT-hubb med enhetens identitet skapade tidigare och svarar på metoden som anropades av molnet.

> [!NOTE]
> Artikeln om [Azure IoT SDK:er][lnk-hub-sdks] innehåller information om Azure IoT SDK:er som du kan använda för att skapa båda apparna så att de kan köras på enheter och på lösningens backend-servrar.
> 
> 

För att slutföra den här kursen behöver du:

* Visual Studio 2015 eller Visual Studio 2017.
* Node.js version 4.0.x eller senare.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp
I det här avsnittet skapar du en Node.js-konsolprogram som svarar på en metod som anropas av lösningen tillbaka slutet.

1. Skapa en ny tom mapp med namnet **simulateddevice**. I mappen **simulateddevice** skapar du en package.json-fil med hjälp av följande kommando i Kommandotolken. Acceptera alla standardvärden:
   
    ```
    npm init
    ```
2. Vid en kommandotolk i den **simulateddevice** mapp, kör följande kommando för att installera den **azure iot-enhet** och **azure-iot-enhet – mqtt** paket:
   
    ```
        npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Med hjälp av en textredigerare, skapa en fil i den **simulateddevice** mapp och ger den namnet **SimulatedDevice.js**.
4. Lägg till följande `require`-instruktioner i början av **SimulatedDevice.js**-filen:
   
    ```
    'use strict';
   
    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. Lägg till en **connectionString** variabel och använda den för att skapa en **DeviceClient** instans. Ersätt **{enheten anslutningssträngen}** med enhetsanslutningen sträng som du genererade i den *skapa en enhetsidentitet* avsnitt:
   
    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. Lägg till följande funktion för att implementera metoden direkt på enheten:
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. Öppna anslutning till din IoT-hubb och initiera lyssnaren för metoden:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. Spara och stäng filen **SimulatedDevice.js**.

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskod, bör du implementera försök principer (till exempel anslutning försök), enligt förslaget i MSDN-artikel [hantering av tillfälliga fel][lnk-transient-faults].
> 
> 

## <a name="call-a-direct-method-on-a-device"></a>Anropa en metod som direkt på en enhet
I det här avsnittet skapar du en .NET-konsolapp som anropar en metod i appen simulerade enheten och sedan visar svaret.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Kontrollera att .NET Framework-versionen är 4.5.1 eller senare. Namnge projektet **CallMethodOnDevice**.
   
    ![Nytt Visual C# Windows Classic Desktop-projekt][10]
2. I Solution Explorer högerklickar du på den **CallMethodOnDevice** projektet och klicka sedan på **hantera NuGet-paket...** .
3. Välj **Bläddra** i fönstret för **NuGet-pakethanteraren**, leta upp **microsoft.azure.devices**, välj **Installera** för att installera **Microsoft.Azure.Devices**-paketet och godkänn användningsvillkoren. Denna procedur hämtar, installerar och lägger till en referens för [NuGet-paketetet SDK för Azure IoT-tjänster][lnk-nuget-service-sdk] och dess beroenden.
   
    ![Fönstret för NuGet-pakethanteraren][11]

4. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices;
5. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med IoT Hub-anslutningssträngen som du skapade i föregående avsnitt.
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Lägg till följande metod i klassen **Program**:
   
        private static async Task InvokeMethod()
        {
            var methodInvocation = new CloudToDeviceMethod("writeLine") { ResponseTimeout = TimeSpan.FromSeconds(30) };
            methodInvocation.SetPayloadJson("'a line to be written'");

            var response = await serviceClient.InvokeDeviceMethodAsync("myDeviceId", methodInvocation);

            Console.WriteLine("Response status: {0}, payload:", response.Status);
            Console.WriteLine(response.GetPayloadAsJson());
        }
   
    Den här metoden startar direkt metod med namnet `writeLine` på den `myDeviceId` enhet. Sedan skriver svaret från enheten på konsolen. Observera hur är det möjligt att ange en tidsgräns för enheten att svara.
7. Slutligen lägger du till följande rader till **Main**-metoden:
   
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        InvokeMethod().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

## <a name="run-the-applications"></a>Köra programmen
Nu är det dags att köra programmen.

1. Högerklicka på lösningen i Visual Studio Solution Explorer och klicka sedan på **ange Startprojekt...** . Välj **enda Startprojekt**, och välj sedan den **CallMethodOnDevice** projekt i den nedrullningsbara menyn.

2. Vid en kommandotolk i den **simulateddevice** mapp, kör följande kommando för att påbörja avlyssning för metodanrop från din IoT-hubb:
   
    ```
    node SimulatedDevice.js
    ```
   Vänta tills den simulerade enheten att öppna:![][7]
3. Nu när enheten är ansluten och väntar på att metoden anrop kör .NET **CallMethodOnDevice** app att anropa metoden i appen simulerade enheten. Du bör se svaret från enheten som har skrivits i konsolen.
   
    ![][8]
4. Enheten reagerar sedan till metoden genom att skriva ut det här meddelandet:
   
    ![][9]

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Denna enhetsidentitet använde för att aktivera appen simulerade enheten att ta hänsyn till metoder som anropas av molnet. Du kan också skapat en app som anropar metoder på enheten och visar svaret från enheten. 

Mer information om hur du kan komma igång med IoT Hub och utforska andra IoT-scenarier finns här:

* [Kom igång med IoT-hubb]
* [Schema-jobb på flera enheter][lnk-devguide-jobs]

Information om hur du utökar din IoT-lösningen och schema metodanrop på flera enheter finns i [schema och broadcast jobb] [ lnk-tutorial-jobs] kursen.

<!-- Images. -->
[7]: ./media/iot-hub-csharp-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-csharp-node-direct-methods/netserviceapp.png
[9]: ./media/iot-hub-csharp-node-direct-methods/methods-output.png

[10]: ./media/iot-hub-csharp-node-direct-methods/direct-methods-csharp1.png
[11]: ./media/iot-hub-csharp-node-direct-methods/direct-methods-csharp2.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Kom igång med IoT-hubb]: iot-hub-node-node-getstarted.md
