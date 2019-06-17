---
title: Skicka enhetsdata via transparent gateway – Machine Learning på Azure IoT Edge | Microsoft Docs
description: Använd en utvecklingsdator som en simulerad IoT Edge-enhet för att skicka data till IoT Hub enheten genom att gå via en enhet som har konfigurerats som en transparent gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 12793ff28bf13f26bc2cc3d436b644601fc48ac8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081387"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Självstudier: Skicka data via en transparent gateway

> [!NOTE]
> Den här artikeln ingår i en serie med en självstudiekurs om hur du använder Azure Machine Learning på IoT Edge. Om du har hamnat på den här artikeln direkt, rekommenderar vi att du börja med den [först artikel](tutorial-machine-learning-edge-01-intro.md) i serien för bästa resultat.

Vi använder utvecklingsdator igen som en simulerad enhet i den här artikeln, men i stället för att skicka data direkt till IoT-hubben som enheten skickar data till IoT Edge-enhet som har konfigurerats som en transparent gateway.

Vi kan övervaka hur IoT Edge-enhet medan den simulerade enheten skickar data. När enheten är klar kör kan vi titta på data i våra storage-konto för att verifiera allt fungerade som förväntat.

Det här steget utförs normalt av en utvecklare i molnet eller enhet.

## <a name="review-device-harness"></a>Granska enheten skydd

Återanvända den [DeviceHarness projekt](tutorial-machine-learning-edge-03-generate-data.md) att simulera det underordnade (eller blad) enheten. Ansluta till en transparent gateway kräver två ytterligare saker:

* Registrera certifikat för att göra underordnad enhet (i det här fallet vår utvecklingsdator) lita på certifikat som används av IoT Edge-körningen.
* Lägg till det edge gateway fullständigt kvalificerade domännamnet (FQDN) till enhetens anslutningssträng.

Titta på koden och se hur dessa två objekt implementeras.

1. Öppna Visual Studio Code på utvecklingsdatorn.

2. Använd **filen** > **Öppna mapp...**  att öppna C:\\källa\\IoTEdgeAndMlSample\\DeviceHarness.

3. Titta på InstallCertificate()-metod i Program.cs.

4. Observera att om koden hittar certifieringssökvägen, anropar metoden CertificateManager.InstallCACert för att installera certifikatet på datorn.

5. Nu vill titta på GetIotHubDevice-metoden i klassen TurbofanDevice.

6. När användaren anger FQDN för en gateway med hjälp av den ”-g” alternativ är att värdet skickas till den här metoden som gatewayFqdn som kommer att läggas till enhetens anslutningssträng.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Skapa och köra löv-enhet

1. Skapa projektet med det DeviceHarness projektet i Visual Studio Code (Ctrl + Skift + B eller **Terminal** > **kör Skapa uppgift...** ) och välj **skapa** från dialogrutan.

2. Hitta det fullständigt kvalificerade domännamnet (FQDN) för din edge-gateway genom att gå till din IoT Edge-enhet virtuell dator i portalen och kopiera värdet för **DNS-namnet** från en översikt.

3. Öppna Visual Studio Code-terminalen (**Terminal** > **ny terminal**) och kör följande kommando, ersätta `<edge_device_fqdn>` med DNS-namnet som du kopierade från den virtuella datorn:

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

4. Programmet försöker att installera certifikatet till din utvecklingsdator. När detta sker kan du acceptera säkerhetsvarningen.

5. När du tillfrågas om IoT Hub anslutning sträng Klicka på ellipsen ( **...** ) på Azure IoT Hub-enheter panelen och välj **kopia IoT Hub-anslutningssträngen**. Klistra in värdet i terminalen.

6. Du ser utdata som liknar:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Observera att lägga till ”GatewayHostName” till enhetens anslutningssträng, vilket gör att enheten att kommunicera via IoT Hub via transparent IoT Edge-gateway.

## <a name="check-output"></a>Kontrollera utdata

### <a name="iot-edge-device-output"></a>IoT Edge-enheter

Utdata från modulen avroFileWriter kan lätt uppfattas genom att titta på IoT Edge-enhet.

1. SSH till din IoT Edge-dator.

2. Leta efter filerna som skrivits till disk.

   ```bash
   find /data/avrofiles -type f
   ```

3. Resultatet av kommandot ska se ut som i följande exempel:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Du kan ha fler än en enda fil beroende på tidpunkten för körningen.

4. Uppmärksam på tidsstämplarna. Modulen avroFileWriter Överför filer till molnet när den senaste ändringstiden är mer än 10 minuter tidigare (se ändrade\_filen\_TIDSGRÄNS i uploader.py i modulen avroFileWriter).

5. När de 10 minuterna har förflutit modulen ska ladda upp filerna. Om överföringen lyckas tar bort filer från disken.

### <a name="azure-storage"></a>Azure-lagring

Vi kan se resultatet av vår löv-enhet som skickar data genom att titta på storage-konton där vi förväntar oss data ska dirigeras.

1. Öppna Visual Studio Code på utvecklingsdator.

2. Navigera i trädet för att hitta ditt storage-konto på panelen ”AZURE STORAGE” i fönstret utforska.

3. Expandera den **Blobbehållare** noden.

4. Från det arbetet som vi gjorde i föregående del av självstudiekursen vi förväntar oss som den **ruldata** behållare ska innehålla meddelanden med RUL. Expandera den **ruldata** noden.

5. En eller flera blob-filer med namnet som visas: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`.

6. Högerklicka på en av filerna och välj **hämta Blob** att spara filen till din utvecklingsdator.

7. Expandera sedan den **uploadturbofanfiles** noden. I föregående artikel vi anger den här platsen som mål för filer som laddats upp av modulen avroFileWriter.

8. Högerklicka på filerna och välj **hämta Blob** att spara den till din utvecklingsdator.

### <a name="read-avro-file-contents"></a>Läs Avro-filens innehåll

Vi ingår ett enkelt kommandoradsverktyg för att läsa en Avro-fil och returnerar en JSON-sträng av meddelanden i filen. I det här avsnittet ska vi installera och köra den.

1. Öppna en terminal i Visual Studio Code (**Terminal** > **ny terminal**).

2. Installera hubavroreader:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

3. Använda hubavroreader för att läsa Avro-filen som du hämtade från **ruldata**.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

4. Observera att brödtexten i meddelandet ser ut när vi förväntade med enhets-ID och förväntad RUL.

   ```json
   {
       "Body": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CycleTime": 1.0,
           "PredictedRul": 170.1723693909444
       },
       "EnqueuedTimeUtc": "<time>",
       "Properties": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CreationTimeUtc": "01/01/0001 00:00:00",
           "EnqueuedTimeUtc": "01/01/0001 00:00:00"
       },
       "SystemProperties": {
           "connectionAuthMethod": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
           "connectionDeviceGenerationId": "636857841798304970",
           "connectionDeviceId": "aaTurbofanEdgeDevice",
           "connectionModuleId": "turbofanRouter",
           "contentEncoding": "utf-8",
           "contentType": "application/json",
           "correlationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "enqueuedTime": "<time>",
           "iotHubName": "mledgeiotwalkthroughhub"
       }
   }
   ```

5. Kör samma kommando skicka Avro-filen som du hämtade från **uploadturbofanfiles**.

6. Som förväntat, innehåller dessa meddelanden alla sensordata och driftinställningar från det ursprungliga meddelandet. Dessa data kan användas för att förbättra RUL-modellen på vår edge-enhet.

   ```json
   {
       "Body": {
           "CycleTime": 1.0,
           "OperationalSetting1": -0.0005000000237487257,
           "OperationalSetting2": 0.00039999998989515007,
           "OperationalSetting3": 100.0,
           "PredictedRul": 170.17236328125,
           "Sensor1": 518.6699829101562,
           "Sensor10": 1.2999999523162842,
           "Sensor11": 47.29999923706055,
           "Sensor12": 522.3099975585938,
           "Sensor13": 2388.010009765625,
           "Sensor14": 8145.31982421875,
           "Sensor15": 8.424599647521973,
           "Sensor16": 0.029999999329447746,
           "Sensor17": 391.0,
           "Sensor18": 2388.0,
           "Sensor19": 100.0,
           "Sensor2": 642.3599853515625,
           "Sensor20": 39.11000061035156,
           "Sensor21": 23.353700637817383,
           "Sensor3": 1583.22998046875,
           "Sensor4": 1396.8399658203125,
           "Sensor5": 14.619999885559082,
           "Sensor6": 21.610000610351562,
           "Sensor7": 553.969970703125,
           "Sensor8": 2387.9599609375,
           "Sensor9": 9062.169921875
       },
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "70df0c98-0958-4c8f-a422-77c2a599594f",
           "CreationTimeUtc": "0001-01-01T00:00:00+00:00",
           "EnqueuedTimeUtc": “<time>”
   }
   ```

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att utforska de resurser som används av den här självstudien för slutpunkt till slutpunkt, vänta tills du är klar för att rensa de resurser som du skapade. Om du inte tänker fortsätta kan du använda följande steg för att ta bort dem:

1. Ta bort resursgrupperna som skapats för att innehålla Dev VM, IoT Edge-VM, IoT Hub, storage-konto, machine learning-arbetsyta-tjänsten (och skapade resurser: behållarregister, programinsikter, nyckelvalvet, storage-konto).

2. Ta bort machine learning-projekt i [Azure anteckningsböcker](https://notebooks.azure.com).

3. Om du har klonat lagringsplatsen lokalt, Stäng alla fönster för PowerShell eller i VS Code som refererar till den lokala lagringsplatsen, och ta sedan bort lagringsplatskatalogen.

4. Om du har skapat certifikat lokalt kan du ta bort mappen c:\\edgeCertificates.

## <a name="next-steps"></a>Nästa steg

I den här artikeln används vi vår utvecklingsdator för att simulera en löv enheten skickar sensor- och användningsdata till vår edge-enhet. Vi har verifierat att moduler på enheten dirigeras, klassificeras, sparas och överfört data först genom att undersöka i realtid driften av edge-enhet och sedan genom att titta på de filer som överförts till lagringskontot.

Mer information finns på följande sidor:

* [En underordnad ansluts till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
* [Store data på gränsen med Azure Blob Storage på IoT Edge (förhandsversion)](how-to-store-data-blob.md)
