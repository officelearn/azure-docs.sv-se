---
title: "Schemalägga med Azure IoT Hub (.NET/nod) | Microsoft Docs"
description: "Så här schemalägger du ett Azure IoT Hub-jobb att anropa en direkt metod på flera enheter. Du kan använda Azure IoT-enhet SDK för Node.js för att implementera apparna simulerade enheten och tjänsten Azure IoT SDK för .NET att implementera ett service-appen att köra jobbet."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: juanpere
ms.openlocfilehash: e6795f09e275f9fcd38000d48710560244abc11d
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="schedule-and-broadcast-jobs-netnodejs"></a>Schemat och sändning jobb (.NET/Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Använd Azure IoT-hubb för att schemalägga och spåra jobb som uppdaterar miljoner enheter. Använd jobb till:

* Uppdatera önskade egenskaper
* Uppdatera taggar
* Anropa direkt metoder

Ett jobb radbryts i någon av följande åtgärder och spårar körning mot en uppsättning enheter som definieras av en enhet dubbla fråga. En backend-app kan till exempel använda ett jobb för att anropa en direkt metod i 10 000 enheter som startar om enheterna. Anger vilken uppsättning av enheter med en enhet dubbla fråga och schemalägga jobbet ska köras vid ett senare tillfälle. Spårar jobbförloppet som var och en av enheterna som tar emot och köra den direkta metoden omstart.

Mer information om var och en av dessa funktioner finns:

* Enheten dubbla och egenskaper: [Kom igång med enheten twins] [ lnk-get-started-twin] och [Självstudier: hur du använder identiska enhetsegenskaper][lnk-twin-props]
* Dirigera metoder: [IoT-hubb Utvecklarhandbok - direkt metoder] [ lnk-dev-methods] och [kursen: direkta metoder][lnk-c2d-methods]

I den här självstudiekursen lär du dig att:

* Skapa en enhetsapp som implementerar en direkt metod som kallas **lockDoor** som kan anropas av backend-app. Enheten appen tar också emot önskade egenskapsändringar från backend-app.
* Skapa en backend-app som skapar ett jobb för att anropa den **lockDoor** direkt metod på flera enheter. Ett annat jobb skickar önskade egenskapen uppdateringar till flera enheter.

I slutet av den här kursen har du en enhet för Node.js konsolapp och en serverdel för .NET (C#) konsolapp:

**simDevice.js** som ansluter till din IoT-hubb implementerar den **lockDoor** direkt metod och hanterar behövs egenskapsändringar.

**ScheduleJob** som använder jobb för att anropa den **lockDoor** direkt metod och uppdatera dubbla önskad enhetsegenskaper på flera enheter.

För att kunna genomföra den här kursen behöver du följande:

* Visual Studio 2015 eller Visual Studio 2017.
* Node.js version 4.0.x eller senare. Artikeln [förbereda din utvecklingsmiljö] [ lnk-dev-setup] beskriver hur du installerar Node.js för den här självstudiekursen på Windows- eller Linux.
* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Schemaläggning för direkta metoden och skicka dubbla uppdateringar

I det här avsnittet skapar du en .NET-konsolapp (med C#) som använder jobb för att anropa den **lockDoor** direkt metod och skicka önskade egenskapen uppdateringar till flera enheter.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Namnge projektet **ScheduleJob**.

    ![Nytt Visual C# Windows Classic Desktop-projekt][img-createapp]

1. I Solution Explorer högerklickar du på den **ScheduleJob** projektet och klicka sedan på **hantera NuGet-paket...** .
1. Välj **Bläddra** i fönstret för **NuGet-pakethanteraren**, leta upp **microsoft.azure.devices**, välj **Installera** för att installera **Microsoft.Azure.Devices**-paketet och godkänn användningsvillkoren. Det här steget hämtar, installerar och lägger till en referens till den [Azure IoT service SDK] [ lnk-nuget-service-sdk] NuGet-paketet och dess beroenden.

    ![Fönstret för NuGet-pakethanteraren][img-servicenuget]
1. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Lägg till följande `using` instruktionen om den inte redan finns i standard-instruktioner.

    ```csharp
    using System.Threading.Tasks;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt platshållaren med IoT-hubb anslutningssträngen för hubben som du skapade i föregående avsnitt.

    ```csharp
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static JobClient jobClient;
    ```

1. Lägg till följande metod i klassen **Program**:

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && (result.Status != JobStatus.Failed));
    }
    ```

1. Lägg till följande metod i klassen **Program**:

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = new CloudToDeviceMethod("lockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            "deviceId='myDeviceId'",
            directMethod,
            DateTime.Now,
            10);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Lägg till följande metod i klassen **Program**:

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        var twin = new Twin();
        twin.Properties.Desired["Building"] = "43";
        twin.Properties.Desired["Floor"] = "3";
        twin.ETag = "*";

        JobResponse result = await jobClient.ScheduleTwinUpdateAsync(jobId,
            "deviceId='myDeviceId'",
            twin,
            DateTime.Now,
            10);

        Console.WriteLine("Started Twin Update Job");
    }
    ```

1. Slutligen lägger du till följande rader till **Main**-metoden:

    ```csharp
    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. I Solution Explorer, öppna den **ange Startprojekt...**  och kontrollera att den **åtgärd** för **ScheduleJob** projektet är **starta**. Skapa lösningen.

## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp

I det här avsnittet skapar du en Node.js-konsolprogram som svarar på en direkt metod som anropas av molnet, vilket utlöser simulerade enheten startas om och använder rapporterade egenskaperna för att aktivera enheten dubbla frågor för att identifiera enheter och när de senast startas om.

1. Skapa en ny tom mapp som kallas **simDevice**.  I den **simDevice** mapp, skapa en package.json-fil med följande kommando vid en kommandotolk.  Acceptera alla standardvärden:

    ```cmd/sh
    npm init
    ```

1. Vid en kommandotolk i den **simDevice** mapp, kör följande kommando för att installera den **azure iot-enhet** och **azure-iot-enhet – mqtt** paket:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Med hjälp av en textredigerare, skapa en ny **simDevice.js** filen i den **simDevice** mapp.

1. Lägg till följande 'krävs, instruktioner i början av den **simDevice.js** fil:

    ```nodejs
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

1. Lägg till en **connectionString**-variabel och använd den för att skapa en **klientinstans**. Se till att ersätta platshållarna med värden som är lämpliga för din installation.

    ```nodejs
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Lägg till följande funktion att hantera den **lockDoor** metod.

    ```nodejs
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```

1. Lägg till följande kod för att registrera hanterare för den **lockDoor** metod.

    ```nodejs
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```

1. Spara och Stäng den **simDevice.js** fil.

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskoden bör du implementera principer för omförsök (till exempel en exponentiell backoff), vilket rekommenderas i MSDN-artikeln om [hantering av tillfälliga fel][lnk-transient-faults].

## <a name="run-the-apps"></a>Kör apparna

Nu är det dags att köra apparna.

1. I Kommandotolken i den **simDevice** mapp, kör följande kommando för att börja lyssna efter omstart direkta metoden.

    ```cmd/sh
    node simDevice.js
    ```

1. Kör C#-konsolapp **ScheduleJob** genom att högerklicka på den **ScheduleJob** projekt, sedan välja **felsöka** och **Starta ny instans**.

1. Du kan se utdata från både enheten och backend-appar.

    ![Köra appar för att schemalägga jobb][img-schedulejobs]

## <a name="next-steps"></a>Nästa steg

I den här kursen används ett jobb för att schemalägga en direkt metod för att en enhet och enheten-dubbla egenskaper att uppdatera.

Om du vill fortsätta komma igång med IoT-hubb och device management mönster, till exempel remote via luften firmware-uppdatering, läsa [Självstudier: hur du gör en firmware-uppdatering][lnk-fwupdate].

Läs om hur du distribuerar AI till enheter med Azure IoT kant i [komma igång med IoT kant][lnk-iot-edge].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-schedule-jobs/createnetapp.png
[img-schedulejobs]: media/iot-hub-csharp-node-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
