---
title: Självstudiekurs för X.509-säkerhet i Azure IoT Hub | Microsoft-dokument
description: Kom igång med den X.509-baserade säkerheten i azure IoT-hubben i en simulerad miljö.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp
ms.openlocfilehash: ac45cf42ed174d3e9423b4ea39cadf16b84897ef
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759647"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Konfigurera X.509-baserad säkerhet i din Azure IoT-hubb

Den här självstudien visar de steg du behöver för att skydda din Azure IoT-hubb med *X.509-certifikatautentiseringen*. Som illustration använder vi openssl-verktyget med öppen källkod för att skapa certifikat lokalt på din Windows-dator. Vi rekommenderar att du endast använder den här självstudien i testsyfte. För produktionsmiljön bör du köpa certifikaten från en *rotcertifikatutfärdare*.

## <a name="prerequisites"></a>Krav

Den här självstudien kräver att du har följande resurser redo:

* Du har skapat en IoT-hubb med din Azure-prenumeration. Mer information finns [i Skapa en IoT-hubb via portalen.](iot-hub-create-through-portal.md)

* Du har [Installerat Visual Studio 2017 eller Visual Studio 2019.](https://www.visualstudio.com/vs/)

## <a name="get-x509-ca-certificates"></a>Hämta X.509 CA-certifikat

Den certifikatbaserade säkerheten X.509 i IoT Hub kräver att du börjar med en [X.509-certifikatkedja](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), som innehåller rotcertifikatet samt eventuella mellanliggande certifikat fram till lövcertifikatet.

Du kan välja något av följande sätt att hämta dina certifikat:

* Köpa X.509-certifikat från en *rotcertifikatutfärdare*. Den här metoden rekommenderas för produktionsmiljöer.

* Skapa dina egna X.509-certifikat med hjälp av ett verktyg från tredje part, till exempel [OpenSSL](https://www.openssl.org/). Denna teknik är bra för test- och utvecklingsändamål. Mer information om hur du genererar certifikatutfärdare med Hjälp av PowerShell eller Bash finns i [Hantera certifikatutfärdarcertifikat för testcertifikat.](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) Resten av den här självstudien använder testcertifikatutfärdare som genereras genom att följa instruktionerna i [Hantera certifikatutfärdarcertifikat för testcertifikat för exempel och självstudier](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

* Generera ett [X.509-intermediärt ca-certifikat](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust) som signerats av ett befintligt rotcertifikatutfärdarcertifikat och överför det till navet. När det mellanliggande certifikatet har laddats upp och verifierats, enligt instruktionerna nedan, kan det användas i stället för ett rotcertifikatutfärdarcertifikat som nämns nedan. Verktyg som OpenSSL ([openssl req](https://www.openssl.org/docs/man1.1.0/man1/req.html) och [openssl ca](https://www.openssl.org/docs/man1.1.0/man1/ca.html)) kan användas för att generera och signera ett mellanliggande CA-certifikat.

> [!NOTE]
> Ladda inte upp roten från tredje part om den inte är unik för dig eftersom det skulle göra det möjligt för andra kunder i tredje part att ansluta sina enheter till din IoT Hub.

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Registrera X.509 CA-certifikat till din IoT-hubb

De här stegen visar hur du lägger till en ny certifikatutfärdare i IoT-hubben via portalen.

1. I Azure-portalen navigerar du till din IoT-hubb och väljer **Inställningar** > **certifikat** för navet.

1. Välj **Lägg till** om du vill lägga till ett nytt certifikat.

1. Ange ett eget visningsnamn i **Certifikatnamn**och välj den certifikatfil som du skapade i föregående avsnitt från datorn.

1. När du har fått ett meddelande om att certifikatet har laddats upp väljer du **Spara**.

    ![Överför certifikat](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   Certifikatet visas i certifikatlistan med **statusen Overifierad**.

1. Markera det certifikat som du just har lagt till för att visa **certifikatinformation**och välj sedan **Generera verifieringskod**.

   ![Verifiera certifikat](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. Kopiera **verifieringskoden** till Urklipp. Du använder den för att verifiera certifikatägarskapet.

1. Följ steg 3 i [Hantera certifikatutfärdare för testcertifikat för exempel och självstudier](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).  Den här processen signerar din verifieringskod med den privata nyckeln som associerar med ditt X.509 CA-certifikat, som genererar en signatur. Det finns verktyg för att utföra den här signeringsprocessen, till exempel OpenSSL. Denna process kallas [bevis på innehav](https://tools.ietf.org/html/rfc5280#section-3.1).

1. I **Certifikatinformation**hittar och öppnar signaturfilen under **Verifieringscertifikat .pem eller .cer..** Välj sedan **Verifiera**.

   Statusen för certifikatet **ändras**till Verified . Välj **Uppdatera** om certifikatet inte uppdateras automatiskt.

## <a name="create-an-x509-device-for-your-iot-hub"></a>Skapa en X.509-enhet för din IoT-hubb

1. I Azure-portalen navigerar du till din IoT-hubb och väljer sedan **Utforskare** > **IoT-enheter**.

1. Välj **Ny** om du vill lägga till en ny enhet.

1. Ange ett eget **visningsnamn i Enhets-ID.** För **autentiseringstyp**väljer du **X.509 CA Signerad**och väljer sedan **Spara**.

   ![Skapa X.509-enhet i portalen](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Autentisera din X.509-enhet med X.509-certifikaten

Om du vill autentisera X.509-enheten måste du först signera enheten med certifikatutfärdarcertifikatet. Signering av bladenheter görs normalt vid tillverkningsanläggningen, där tillverkningsverktyg har aktiverats i enlighet med detta. Eftersom enheten går från en tillverkare till en annan, fångas varje tillverkares signeringsåtgärd som ett mellanliggande certifikat i kedjan. Resultatet är en certifikatkedja från certifikatutfärdarcertifikatet till enhetens lövcertifikat. Steg 4 i [Hantera certifikatutfärdare för testcertifikat för exempel och självstudier](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) genererar ett enhetscertifikat.

Därefter visar vi dig hur du skapar ett C#-program för att simulera X.509-enheten som är registrerad för din IoT-hubb. Vi skickar temperatur- och fuktighetsvärden från den simulerade enheten till ditt nav. I den här självstudien skapar vi bara enhetsprogrammet. Det lämnas som en övning till läsarna för att skapa IoT Hub-tjänstprogrammet som skickar svar på de händelser som skickas av den här simulerade enheten. C#-programmet förutsätter att du har följt stegen i [Hantera certifikatutfärdare för testcertifikat för exempel och självstudier](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

1. Öppna Visual Studio, välj **Skapa ett nytt projekt**och välj sedan projektmallen Console App **(.NET Framework).** Välj **Nästa**.

1. I **Konfigurera det nya projektet**namnger du projektet *SimulateX509Device*och väljer sedan **Skapa**.

   ![Skapa X.509-enhetsprojekt i Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. Högerklicka på **Projektet SimuleraX509Device** i Solution Explorer och välj sedan **Hantera NuGet-paket**.

1. I **NuGet Package Manager**väljer du **Bläddra** och sök efter och välj **Microsoft.Azure.Devices.Client**. Välj **Installera**.

   ![Lägg till enhet SDK NuGet-paket i Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Det här steget hämtar, installerar och lägger till en referens till Azure IoT-enheten SDK NuGet-paketet och dess beroenden.

1. Lägg till `using` följande satser högst upp i **Program.cs-filen:**

    ```csharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. Lägg till följande fält i klassen **Program:**

    ```csharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    Använd det eget enhetsnamn som du använde i föregående avsnitt i stället för _<your_device_id>_.

1. Lägg till följande funktion för att skapa slumptal för temperatur och luftfuktighet och skicka dessa värden till navet:

    ```csharp
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

1. Lägg slutligen till följande kodrader i **huvudfunktionen** och ersätter platshållarenhets-id, _ditt-iot-hub-name_och _absolut sökväg till din-enhet-pfx-fil_ enligt vad som krävs av konfigurationen. _device-id_

    ```csharp
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

   Den här koden ansluter till IoT-hubben genom att skapa anslutningssträngen för din X.509-enhet. När den har anslutits skickar den sedan temperatur- och fuktighetshändelser till navet och väntar på dess svar.

1. Kör appen. Eftersom det här programmet kommer åt en *PFX-fil* kan du behöva köra den här appen som administratör.

   1. Bygg Visual Studio-lösningen.

   1. Öppna ett nytt kommandotolksfönster med hjälp av **Kör som administratör**.  

   1. Navigera till mappen som innehåller din lösning och navigera sedan till sökvägen mellan *lagerplats och felsökning* i lösningsmappen.

   1. Kör programmet **SimulateX509Device.exe** från kommandotolken.

   Du bör se din enhet framgångsrikt ansluta till navet och skicka händelserna.

   ![Kör enhetsapp](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du skyddar din IoT-lösning finns i:

* [Metodtips för IoT-säkerhet](../iot-fundamentals/iot-security-best-practices.md)

* [IoT-säkerhetsarkitektur](../iot-fundamentals/iot-security-architecture.md)

* [Skydda distributionen av IoT](../iot-fundamentals/iot-security-deployment.md)

Mer information om hur du utforskar funktionerna i IoT Hub finns i:

* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
