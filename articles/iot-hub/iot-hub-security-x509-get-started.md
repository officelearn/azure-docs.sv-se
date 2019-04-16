---
title: Självstudie för X.509-säkerhet i Azure IoT Hub | Microsoft Docs
description: Kom igång med X.509-baserade säkerheten i Azure IoT hub i en simulerad miljö.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.openlocfilehash: 5795cde35d53a64620c4fdb6c3af99a7f56b12d9
ms.sourcegitcommit: e89b9a75e3710559a9d2c705801c306c4e3de16c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59571145"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Konfigurera X.509 säkerheten i Azure IoT hub

Den här självstudien simulerar stegen som du behöver för att skydda din Azure IoT hub med hjälp av den *X.509 certifikatautentisering*. I syfte att bilden visar vi hur du använder verktyg med öppen källkod OpenSSL för att skapa certifikat lokalt på din Windows-dator. Vi rekommenderar att du använder den här självstudien endast i testsyfte. För produktionsmiljö bör du köpa certifikat från en *certifikatutfärdare (CA) för rotmappen*.

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien måste du ha redo följande resurser:

* Du har skapat en IoT-hubb med din Azure-prenumeration. Se [skapar en IoT hub via portalen](iot-hub-create-through-portal.md) detaljerade anvisningar.

* Du har [Visual Studio 2017 eller Visual Studio 2019](https://www.visualstudio.com/vs/) installerat på datorn.

## <a name="get-x509-ca-certificates"></a>Hämta X.509 CA-certifikat

X.509-certifikatbaserad säkerheten i IoT Hub måste du börja med en [X.509-certifikatkedja](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), som innehåller rotcertifikatet samt eventuella mellanliggande certifikat fram lövcertifikatet.

Du kan välja något av följande sätt att få ditt certifikat:

* Köpa X.509-certifikat från en *certifikatutfärdare (CA) för rotmappen*. Detta rekommenderas för produktionsmiljöer.

* Skapa dina egna X.509-certifikat med ett verktyg från tredje part som [OpenSSL](https://www.openssl.org/). Det här är bra för testning och utveckling. Se [hantera test CA-certifikat för exempel och självstudier](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) information om hur du skapar testa CA-certifikat med hjälp av PowerShell eller Bash. Resten av den här självstudien använder CA-testcertifikat som genereras genom att följa instruktionerna i [hantera test CA-certifikat för exempel och självstudier](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Registrera X.509 CA-certifikat till din IoT hub

De här stegen visar hur du lägger till en ny certifikatutfärdare till din IoT hub via portalen.

1. Navigera till din IoT-hubb i Azure-portalen och öppna den **inställningar** > **certifikat** menyn.

2. Klicka på **Lägg till** att lägga till ett nytt certifikat.

3. Ange ett eget namn till ditt certifikat. Välj filen med rotcertifikatet med namnet *RootCA.cer* skapade i föregående avsnitt, från din dator. Klicka på **Överför**.

4. När du får ett meddelande om att ditt certifikat har laddats upp, klickar du på **spara**.

    ![Överför certifikat](./media/iot-hub-security-x509-get-started/add-new-cert.png)  

   Det här alternativet visas certifikatet i den **Certifikatutforskare** lista. Obs den **STATUS** av det här certifikatet är *inte verifierad*.

5. Klicka på det certifikat som du lade till i föregående steg.

6. I den **certifikatinformation** bladet klickar du på **generera Verifieringskod**.

7. Den skapar en **Verifieringskod** att verifiera ägarskapet för certifikatet. Kopiera koden till Urklipp.

   ![Verifiera certifikat](./media/iot-hub-security-x509-get-started/verify-cert.png)  

8. Nu kan du behöva logga in detta *Verifieringskod* med det privata nyckeln associerat med X.509 CA-certifikat som genererar en signatur. Det finns verktyg som är tillgängliga för att utföra den här signering processen, till exempel OpenSSL. Detta kallas den [bevis på tillgång](https://tools.ietf.org/html/rfc5280#section-3.1). Steg 3 i [hantera test CA-certifikat för exempel och självstudier](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) genererar en Verifieringskod.

9. Ladda upp den resulterande signaturen från steg 8 ovan till IoT-hubben i portalen. I den **certifikatinformation** bladet på Azure-portalen, navigera till den **verifieringscertifikatet .pem eller .cer-fil**, och välj signatur, till exempel *VerifyCert4.cer*skapats av exemplet PowerShell kommando med den _Utforskaren_ ikonen bredvid den.

10. När certifikatet har laddats upp, klickar du på **Kontrollera**. Den **STATUS** av ändringarna certifikat **_verifierad_** i den **certifikat** bladet. Klicka på **uppdatera** om det inte uppdateras automatiskt.

    ![Ladda upp certifikatverifiering](./media/iot-hub-security-x509-get-started/upload-cert-verification.png)  

## <a name="create-an-x509-device-for-your-iot-hub"></a>Skapa en X.509-enhet för din IoT hub

1. I Azure-portalen går du till din IoT-hubb **Utforskare > IoT-enheter** sidan.

2. Klicka på **+ Lägg till** att lägga till en ny enhet.

3. Ge ett eget namn för den **enhets-ID**, och välj **_X.509 CA-signerat_** som den **autentiseringstyp**. Klicka på **Spara**.

   ![Skapa X.509-enhet i portalen](./media/iot-hub-security-x509-get-started/create-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Autentisera din X.509-enhet med X.509-certifikat

För att autentisera din X.509-enhet, måste du först registrera enheten med CA-certifikatet. Signering av lövenheter görs normalt på tillverkningsanläggning där tillverkning verktyg har aktiverats i enlighet med detta. När enheten går från en tillverkare till en annan, avbildas varje tillverkares signering åtgärd som ett mellanliggande certifikat i kedjan. Slutresultatet är en certifikatkedja från CA-certifikatet till enhetens lövcertifikatet. Steg 4 i [hantera test CA-certifikat för exempel och självstudier](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) genererar ett certifikat.

Därefter visas hur du skapar en C#-program att simulera X.509-enhet registrerad för din IoT-hubb. Vi skickar temperatur och fuktighet värden från den simulerade enheten till hubben. Observera att vi ska skapa enhet programmet i den här självstudien. Det är värt som en övning läsarna att skapa program för IoT Hub-tjänsten som skickas som svar på händelser som skickas av den här simulerade enheten. C#-programmet förutsätter att du har följt stegen i [hantera test CA-certifikat för exempel och självstudier](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

1. Skapa ett nytt Visual C# Windows Classic Desktop-projekt med hjälp av projektmallen konsolprogram i Visual Studio. Ge projektet namnet **SimulateX509Device**.

   ![Skapa X.509-enhet-projekt i Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project.png)

2. I Solution Explorer högerklickar du på den **SimulateX509Device** projektet och klicka sedan på **hantera NuGet-paket...** . I NuGet Package Manager-fönstret väljer **Bläddra** och Sök efter **microsoft.azure.devices.client**. Välj **installera** att installera den **Microsoft.Azure.Devices.Client** paketet och Godkänn användningsvillkoren. Den här proceduren hämtar, installerar och lägger till en referens till Azure IoT device SDK NuGet-paketet och dess beroenden.

   ![Lägg till enhet SDK NuGet-paketet i Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

3. Lägg till följande rader med kod högst upp på den *Program.cs* fil:

    ```CSharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

4. Lägg till följande rader med kod i den **programmet** klass:

    ```CSharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

     Använd egna enhetsnamnet som du använde i föregående avsnitt i stället för _< your_device_id >_ platshållare.

5. Lägg till följande funktion för att skapa slumptal för temperatur och fuktighet och skickar de värdena till hubben:

    ```CSharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

6. Slutligen lägger du till följande rader med kod till den **Main** funktion, ersätta platshållarna _enhet-id_, _your-iot-hub-name_ och  _Absolute-Path-to-your-Device-PFX-File_ som krävs för din konfiguration.

    ```CSharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

        if (deviceClient == null)
        {
            Console.WriteLine("Failed to create DeviceClient!");
        }
        else
        {
            Console.WriteLine("Successfully created DeviceClient!");
            SendEvent(deviceClient).Wait();
        }

        Console.WriteLine("Exiting...\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

   Den här koden ansluter till din IoT-hubb genom att skapa anslutningssträngen för din X.509-enhet. När du har anslutit den sedan skickar temperatur och fuktighet händelser till hubben och väntar på sitt svar. 
7. Eftersom det här programmet har åtkomst till en *.pfx* fil, du kan behöva köra programmet i *Admin* läge. Skapa Visual Studio-lösningen. Öppna ett nytt kommandofönster som en **administratör**, och navigera till mappen som innehåller den här lösningen. Navigera till den *bin/Debug* sökvägen i lösningsmappen. Kör programmet **SimulateX509Device.exe** från den _Admin_ kommandofönstret. Du bör se din enhet har anslutning till hubben och skicka händelserna. 

   ![Kör app för enheter](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du skyddar din IoT-lösning finns:

* [Metodtips för IoT-säkerhet](../iot-fundamentals/iot-security-best-practices.md)

* [Arkitektur för IoT-säkerhet](../iot-fundamentals/iot-security-architecture.md)

* [Skydda distributionen av IoT](../iot-fundamentals/iot-security-deployment.md)

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
