---
title: "Använda Azure IoT Hub direkt metoder (.NET/.NET) | Microsoft Docs"
description: "Hur du använder Azure IoT Hub direkt metoder. Du kan använda Azure IoT-enhet SDK för .NET för att implementera en simulerad enhetsapp som innehåller en direkt metod och Azure IoT SDK för .NET att implementera ett service-appen som anropar metoden direkt."
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: dkshir
ms.openlocfilehash: 9ce1fbebb6417c10618aa182e3c1d9ddf8132fb6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-netnet"></a>Använda direkt metoder (.NET/.NET)
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

I den här kursen ska vi utveckla appar med två .NET-konsolen:

* **CallMethodOnDevice**, en backend-app, som anropar en metod i appen simulerade enheten och visar svaret.
* **SimulateDeviceMethods**, en konsolapp som simulerar en enhet som ansluter till din IoT-hubb med enhetens identitet skapade tidigare och svarar på metoden som anropas av molnet.

> [!NOTE]
> Artikeln om [Azure IoT SDK:er][lnk-hub-sdks] innehåller information om Azure IoT SDK:er som du kan använda för att skapa båda apparna så att de kan köras på enheter och på lösningens backend-servrar.
> 
> 

För att slutföra den här kursen behöver du:

* Visual Studio 2015 eller Visual Studio 2017.
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnadsfritt konto][lnk-free-trial] på bara några minuter.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Om du vill skapa enhetens identitet programmässigt i stället läsa motsvarande avsnitt i den [ansluta din simulerade enhet till din IoT-hubb med hjälp av .NET] [ lnk-device-identity-csharp] artikel.


## <a name="create-a-simulated-device-app"></a>Skapa en simulerad enhetsapp
I det här avsnittet skapar du en .NET-konsolapp som svarar på en metod som anropas av lösningen tillbaka slutet.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Namnge projektet **SimulateDeviceMethods**.
   
    ![Ny Visual C# Windows Classic enhetsapp][img-createdeviceapp]
    
1. I Solution Explorer högerklickar du på den **SimulateDeviceMethods** projektet och klicka sedan på **hantera NuGet-paket...** .
1. I den **NuGet Package Manager** väljer **Bläddra** och Sök efter **microsoft.azure.devices.client**. Välj **installera** att installera den **Microsoft.Azure.Devices.Client** paketet och Godkänn användningsvillkoren. Den här proceduren hämtar, installerar och lägger till en referens till den [Azure IoT-enhet SDK] [ lnk-nuget-client-sdk] NuGet-paketet och dess beroenden.
   
    ![NuGet-Pakethanteraren fönstret-klientappen][img-clientnuget]
1. Lägg till följande `using`-uttryck överst i **Program.cs**-filen:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;

1. Lägg till följande fält i klassen **Program**. Ersätt platshållaren värdet med den anslutningssträng för enheten som du antecknade i föregående avsnitt.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

1. Lägg till följande om du vill implementera metoden direkt på enheten:

        static Task<MethodResponse> WriteLineToConsole(MethodRequest methodRequest, object userContext)
        {
            Console.WriteLine();
            Console.WriteLine("\t{0}", methodRequest.DataAsJson);
            Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

            string result = "'Input was written to log.'";
            return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
        }

1. Slutligen lägger du till följande kod i **Main** metod för att öppna anslutningen till din IoT-hubb och initiera lyssnaren för metoden:
   
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

            // setup callback for "writeLine" method
            Client.SetMethodHandlerAsync("writeLine", WriteLineToConsole, null).Wait();
            Console.WriteLine("Waiting for direct method call\n Press enter to exit.");
            Console.ReadLine();

            Console.WriteLine("Exiting...");

            // as a good practice, remove the "writeLine" handler
            Client.SetMethodHandlerAsync("writeLine", null, null).Wait();
            Client.CloseAsync().Wait();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
        
1. Högerklicka på lösningen i Visual Studio Solution Explorer och klicka sedan på **ange Startprojekt...** . Välj **enda Startprojekt**, och välj sedan den **SimulateDeviceMethods** projekt i den nedrullningsbara menyn.        

> [!NOTE]
> För att göra det så enkelt som möjligt implementerar vi ingen princip för omförsök i den här självstudiekursen. I produktionskod, bör du implementera försök principer (till exempel anslutning försök), enligt förslaget i MSDN-artikel [hantering av tillfälliga fel][lnk-transient-faults].
> 
> 

## <a name="call-a-direct-method-on-a-device"></a>Anropa en metod som direkt på en enhet
I det här avsnittet skapar du en .NET-konsolapp som anropar en metod i appen simulerade enheten och sedan visar svaret.

1. I Visual Studio lägger du till ett Visual C# Classic Desktop-projekt i den aktuella lösningen med hjälp av projektmallen **Konsolprogram**. Kontrollera att .NET Framework-versionen är 4.5.1 eller senare. Namnge projektet **CallMethodOnDevice**.
   
    ![Nytt Visual C# Windows Classic Desktop-projekt][img-createserviceapp]
2. I Solution Explorer högerklickar du på den **CallMethodOnDevice** projektet och klicka sedan på **hantera NuGet-paket...** .
3. Välj **Bläddra** i fönstret för **NuGet-pakethanteraren**, leta upp **microsoft.azure.devices**, välj **Installera** för att installera **Microsoft.Azure.Devices**-paketet och godkänn användningsvillkoren. Denna procedur hämtar, installerar och lägger till en referens för [NuGet-paketetet SDK för Azure IoT-tjänster][lnk-nuget-service-sdk] och dess beroenden.
   
    ![Fönstret för NuGet-pakethanteraren][img-servicenuget]

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

1. Högerklicka på lösningen i Visual Studio Solution Explorer och klicka sedan på **ange Startprojekt...** . Välj **enda Startprojekt**, och välj sedan den **CallMethodOnDevice** projekt i den nedrullningsbara menyn.

## <a name="run-the-applications"></a>Köra programmen
Nu är det dags att köra programmen.

1. Kör .NET enhetsapp **SimulateDeviceMethods**. Den ska börja lyssna efter metodanrop från din IoT-hubb: 

    ![Enhetsapp kör][img-deviceapprun]
1. Nu när enheten är ansluten och väntar på att metoden anrop kör .NET **CallMethodOnDevice** app att anropa metoden i appen simulerade enheten. Du bör se svaret från enheten som har skrivits i konsolen.
   
    ![Kör Service-appen][img-serviceapprun]
1. Enheten reagerar sedan till metoden genom att skriva ut det här meddelandet:
   
    ![Direkta metoden anropas på enheten][img-directmethodinvoked]

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen konfigurerade du en ny IoT Hub på Azure Portal och skapade sedan en enhetsidentitet i IoT-hubbens identitetsregister. Denna enhetsidentitet använde för att aktivera appen simulerade enheten att ta hänsyn till metoder som anropas av molnet. Du kan också skapat en app som anropar metoder på enheten och visar svaret från enheten. 

Mer information om hur du kan komma igång med IoT Hub och utforska andra IoT-scenarier finns här:

* [Kom igång med IoT-hubb]
* [Schema-jobb på flera enheter][lnk-devguide-jobs]

Information om hur du utökar din IoT-lösningen och schema metodanrop på flera enheter finns i [schema och broadcast jobb] [ lnk-tutorial-jobs] kursen.

<!-- Images. -->
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-direct-methods/create-device-app.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-direct-methods/device-app-nuget.png
[img-createserviceapp]: ./media/iot-hub-csharp-csharp-direct-methods/create-service-app.png
[img-servicenuget]: ./media/iot-hub-csharp-csharp-direct-methods/service-app-nuget.png
[img-deviceapprun]: ./media/iot-hub-csharp-csharp-direct-methods/run-device-app.png
[img-serviceapprun]: ./media/iot-hub-csharp-csharp-direct-methods/run-service-app.png
[img-directmethodinvoked]: ./media/iot-hub-csharp-csharp-direct-methods/direct-method-invoked.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-device-identity-csharp]: iot-hub-csharp-csharp-getstarted.md#DeviceIdentity_csharp

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[Kom igång med IoT-hubb]: iot-hub-node-node-getstarted.md
