---
title: "Självstudier för X.509-säkerhet i Azure IoT Hub | Microsoft Docs"
description: "Komma igång med X.509 baserat säkerheten i din Azure IoT-hubb i en simulerad miljö."
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2017
ms.author: dkshir
ms.openlocfilehash: 99fb80d5cafc8fd20f5048de305fef99bf49c286
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2017
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Ställ in X.509 säkerheten i din Azure IoT-hubb

Den här självstudiekursen simulerar steg du behöver för att skydda din Azure IoT-hubb med den *X.509 certifikatautentisering*. För bilden visar vi hur du använder verktyget öppen källkod OpenSSL för att skapa certifikat lokalt på din Windows-dator. Vi rekommenderar att du använder den här självstudiekursen för endast för testning. För produktionsmiljö bör du köpa certifikat från en *rot-certifikatutfärdare (CA)*. 

## <a name="prerequisites"></a>Krav
Den här självstudiekursen måste du ha redo följande resurser:

- Du har skapat en IoT-hubb med din Azure-prenumeration. Se [skapar en IoT-hubb via portalen](iot-hub-create-through-portal.md) detaljerade anvisningar. 
- Du har [Visual Studio 2015 eller Visual Studio 2017](https://www.visualstudio.com/vs/) installerat på datorn. 

<a id="getcerts"></a>

## <a name="get-x509-ca-certificates"></a>Hämta X.509-certifikat
X.509 certifikatbaserad säkerhet i IoT-hubben måste du börja med en [X.509 certifikatkedja](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), som innehåller rotcertifikatet, liksom alla mellanliggande certifikat fram till lövcertifikatet. 

Du kan välja något av följande sätt att få ditt certifikat:
- Köpa X.509-certifikat från en *rot-certifikatutfärdare (CA)*. Detta rekommenderas för produktionsmiljöer.
ELLER,
- Skapa din egen X.509-certifikat med ett verktyg från tredje part, till exempel [OpenSSL](https://www.openssl.org/). Det här är bra för testning och utveckling. I avsnitten om *skapa X.509-certifikat* och *skapa X.509 certifikatkedja* i artikeln [hur du skapar X.509-certifikat med hjälp av PowerShell](iot-hub-security-x509-create-certificates.md) vägleder dig med hjälp av OpenSSL och PowerShell via en PowerShell-exempelskript för att skapa certifikat. Om du föredrar att använda **Bash** gränssnitt i stället för PowerShell kan du läsa mer [hantera CA-certifikat exempel](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md). Resten av den här kursen använder OpenSSL miljön i det här *så* guiden leder dig igenom X.509-säkerhet för slutpunkt till slutpunkt i Azure IoT Hub.


<a id="registercerts"></a>

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Registrera X.509-certifikat till din IoT-hubb

Dessa steg visar hur du lägger till en ny certifikatutfärdare i din IoT-hubb via portalen.

1. Navigera till din IoT-hubb i Azure-portalen och öppna den **inställningar** > **certifikat** menyn. 
2. Klicka på **Lägg till** att lägga till ett nytt certifikat.
3. Ange ett eget namn till ditt certifikat. Välj på rotcertifikatfilen med namnet *RootCA.cer* skapade i föregående avsnitt från din dator. Klicka på **Överför**.
4. När du får ett meddelande om att ditt certifikat har överförts klickar du på **spara**.

    ![Överför certifikat](./media/iot-hub-security-x509-get-started/add-new-cert.png)  

   Det här alternativet visas certifikatet i den **certifikat Explorer** lista. Observera den **STATUS** av det här certifikatet är *Ej verifierat*.

5. Klicka på det certifikat som du lade till i föregående steg.

6. I den **certifikatinformation** bladet, klickar du på **generera Verifieringskod**.

7. Den skapar en **Verifieringskod** att validera certifikatet ägare. Kopiera koden till Urklipp. 

   ![Verifiera certifikat](./media/iot-hub-security-x509-get-started/verify-cert.png)  

8. Nu kan du behöva logga detta *Verifieringskod* med privat nyckel associeras med X.509 CA-certifikat, vilket genererar en signatur. Det finns tillgängliga för att utföra den här signeringsprocessen, till exempel OpenSSL verktyg. Detta kallas den [konceptbevis tillgång](https://tools.ietf.org/html/rfc5280#section-3.1). Om du har använt våra exempel PowerShell-skript i föregående avsnitt, och kör sedan skriptet som anges i avsnittet [konceptbevis tillgång X.509 Certifikatutfärdarens certifikat](iot-hub-security-x509-create-certificates.md#signverificationcode).
 
9. Överför den resulterande signaturen från steg 8 ovan för att din IoT-hubb i portalen. I den **certifikatinformation** bladet på Azure portal, navigerar du till den **verifieringscertifikat .pem eller .cer-fil**, och välj signatur, till exempel *VerifyCert4.cer*skapats exempel PowerShell kommando med den _Utforskaren_ ikonen förutom den.

10. När certifikatet har laddats upp, klickar du på **Kontrollera**. Den **STATUS** av ändringarna certifikat  **_verifierad_**  i den **certifikat** bladet. Klicka på **uppdatera** om den inte uppdateras automatiskt.

   ![Överför certifikatverifiering](./media/iot-hub-security-x509-get-started/upload-cert-verification.png)  


<a id="createdevice"></a>

## <a name="create-an-x509-device-for-your-iot-hub"></a>Skapa en X.509-enhet för din IoT-hubb

1. Navigera till din IoT-hubb i Azure-portalen **enheten Explorer**.

2. Klicka på **Lägg till** att lägga till en ny enhet. 

3. Ge ett eget namn för den **enhets-ID**, och välj  **_X.509 CA-signerat_**  som den **autentiseringstyp**. Klicka på **Spara**.

   ![Skapa X.509-enhet i portalen](./media/iot-hub-security-x509-get-started/create-x509-device.png)



<a id="authenticatedevice"></a>

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Autentisera enheten X.509 med X.509-certifikat

För att autentisera enheten X.509, måste du först registrerar enheten med CA-certifikatet. Signering av löv enheter görs normalt på tillverkningsanläggning där tillverkning verktyg har aktiverats i enlighet med detta. Eftersom enheten går från en tillverkare till en annan, avbildas signering åtgärd för varje tillverkare som en mellanliggande certifikat i kedjan. Slutresultatet är en certifikatkedja från CA-certifikatet till enhetens lövcertifikatet. Om du har använt våra PowerShell-skript i föregående avsnitt och du kan köra skript som anges i avsnittet *skapa lövmedlemmar X.509-certifikat för din enhet* i artikeln [PowerShell-skript till Hantera CA-signerat X.509-certifikat](iot-hub-security-x509-create-certificates.md) att simulera den här processen.

Därefter visas hur du skapar en C#-program att simulera X.509-enhet registrerad för din IoT-hubb. Vi skickar temperatur- och fuktighetskonsekvens värden från den simulerade enheten till din hubb. Observera att vi ska skapa enheten programmet i den här självstudiekursen. Den lämnas som Övning för att användas för att skapa IoT-hubb-tjänstprogram som skickas som svar på händelser som skickats av den här simulerade enheten. C#-program förutsätter att du har följt av PowerShell-skript som nämns i artikeln [PowerShell-skript för att hantera CA-signerat X.509-certifikat](iot-hub-security-x509-create-certificates.md)

1. Skapa ett nytt projekt i Visual C# klassiska skrivbordet med hjälp av projektmallen konsolprogram i Visual Studio. Namnge projektet **SimulateX509Device**.
   ![Skapa X.509 enheten projekt i Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project.png)

2. I Solution Explorer högerklickar du på den **SimulateX509Device** projektet och klicka sedan på **hantera NuGet-paket...** . Välj i NuGet Package Manager-fönstret **Bläddra** och Sök efter **microsoft.azure.devices.client**. Välj **installera** att installera den **Microsoft.Azure.Devices.Client** paketet och Godkänn användningsvillkoren. Den här proceduren hämtar, installerar och lägger till en referens till Azure IoT-enhet SDK NuGet-paketet och dess beroenden.
   ![Lägg till enhet SDK NuGet-paketet i Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

3. Lägg till följande rader med kod högst upp i den *Program.cs* fil:
    
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
   Använda egna enhetsnamnet som du använde i föregående avsnitt i stället för _< your_device_id >_ platshållare.

5. Lägg till följande funktion för att skapa slumptal för temperatur- och fuktighetskonsekvens och skicka dessa värden till hubben:
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

6. Slutligen lägger du till följande rader med kod till den **Main** funktion, ersätta platshållarna _enhetens id_, _din-iot-hubb-name_ och  _Absolute-Path-to-your-Device-PFX-File_ som krävs av din konfiguration.
    ```CSharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "123");
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
   Den här koden ansluter till din IoT-hubb genom att skapa anslutningssträngen för X.509-enhet. När du har anslutit den sedan skickar händelser för temperatur- och fuktighetskonsekvens för hubben och väntar på sitt svar. 
7. Eftersom det här programmet ansluter till en *.pfx* -fil, måste du köra den i *Admin* läge. Skapa Visual Studio-lösning. Öppna ett nytt kommandofönster som en **administratör**, och navigera till mappen som innehåller den här lösningen. Navigera till den *bin/Debug* sökväg i mappen lösning. Kör programmet **SimulateX509Device.exe** från den _Admin_ kommandofönstret. Du bör se din enhet har anslutning till hubben och skicka händelser. 
   ![Kör appen på enheten](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="see-also"></a>Se även
Mer information om hur du skyddar IoT-lösningen finns:

* [IoT-säkerhetsmetoder][lnk-security-best-practices]
* [IoT-säkerhetsarkitekturen][lnk-security-architecture]
* [Säkra din IoT-distribution][lnk-security-deployment]

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Simulera en enhet med Azure IoT kant][lnk-iotedge]

[lnk-security-best-practices]: iot-hub-security-best-practices.md
[lnk-security-architecture]: iot-hub-security-architecture.md
[lnk-security-deployment]: iot-hub-security-deployment.md

[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
