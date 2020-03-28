---
title: 'Självstudiekurs: Skicka enhetsdata via transparent gateway – Machine Learning på Azure IoT Edge'
description: Den här självstudien visar hur du kan använda utvecklingsdatorn som en simulerad IoT Edge-enhet för att skicka data till IoT Hub genom att gå igenom en enhet som konfigurerats som en transparent gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 50f339b257110f0a5dc0ac08b9f40043ee384afb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74706914"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Självstudiekurs: Skicka data via transparent gateway

> [!NOTE]
> Den här artikeln är en del av en serie för en självstudiekurs om hur du använder Azure Machine Learning på IoT Edge. Om du har kommit till den här artikeln direkt, uppmuntrar vi dig att börja med den [första artikeln](tutorial-machine-learning-edge-01-intro.md) i serien för bästa resultat.

I den här artikeln använder vi återigen utvecklingsdatorn som en simulerad enhet, men istället för att skicka data direkt till IoT Hub skickar enheten data till IoT Edge-enheten som konfigurerats som en transparent gateway.

Vi övervakar driften av IoT Edge-enheten medan den simulerade enheten skickar data. När enheten är klar när enheten är klar tittar vi på data i vårt lagringskonto för att validera allt som fungerade som förväntat.

Det här steget utförs vanligtvis av en moln- eller enhetsutvecklare.

## <a name="review-device-harness"></a>Granska enhetsselen

Återanvänd [DeviceHarness-projektet](tutorial-machine-learning-edge-03-generate-data.md) för att simulera nedströmsenheten (eller lövenheten). Att ansluta till den genomskinliga gatewayen kräver ytterligare två saker:

* Registrera certifikatet för att göra nedströmsenheten (i det här fallet vår utvecklingsmaskin) lita på certifikatutfärdaren som används av IoT Edge-körningen.
* Lägg till edge gateway fullt kvalificerat domännamn (FQDN) till enheten anslutningssträngen.

Titta på koden för att se hur dessa två objekt implementeras.

1. På din utvecklingsmaskin öppnar du Visual Studio Code.

2. Använd **File** > **Open Folder** ...\\\\för att öppna C:\\källa IoTEdgeAndMlSample DeviceHarness.

3. Titta på metoden InstallCertificate() i Program.cs.

4. Observera att om koden hittar certifikatsökvägen anropas metoden CertificateManager.InstallCACert för att installera certifikatet på datorn.

5. Titta nu på GetIotHubDevice-metoden på TurbofanDevice-klassen.

6. När användaren anger FQDN för gatewayen med alternativet "-g" skickas det värdet till den här metoden som gatewayFqdn, som läggs till i enhetsanslutningssträngen.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Skapa och köra lövenhet

1. När DeviceHarness-projektet fortfarande är öppet i Visual Studio-kod skapar du projektet (Ctrl + Skift + B eller **Terminal** > **Run Build Task...**) och väljer **Bygg** i dialogrutan.

2. Hitta det fullständigt kvalificerade domännamnet (FQDN) för din edge-gateway genom att navigera till den virtuella datorn för IoT Edge-enheten i portalen och kopiera värdet för **DNS-namnet** från översikten.

3. Öppna Visual Studio-kodterminalen **(Terminal** > **New terminal)** `<edge_device_fqdn>` och kör följande kommando och ersätt med det DNS-namn som du kopierade från den virtuella datorn:

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

4. Programmet försöker installera certifikatet på utvecklingsdatorn. När den gör det, acceptera säkerhetsvarningen.

5. När du uppmanas att ange anslutningssträngen för IoT Hub klickar du på ellipsen (**...**) på azure IoT Hub-enhetspanelen och välj **Kopiera IoT Hub Connection String**. Klistra in värdet i terminalen.

6. Du kommer att se utdata som:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Observera tillägget av "GatewayHostName" till enhetens anslutningssträng, vilket gör att enheten kommunicerar via IoT Hub via IoT Edge transparent gateway.

## <a name="check-output"></a>Kontrollera utdata

### <a name="iot-edge-device-output"></a>Utdata för IoT Edge-enhet

Utdata från avroFileWriter-modulen kan lätt observeras genom att titta på IoT Edge-enheten.

1. SSH i din virtuella IoT Edge-dator.

2. Leta efter filer som skrivits till disk.

   ```bash
   find /data/avrofiles -type f
   ```

3. Utdata från kommandot ser ut som följande exempel:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Du kan ha mer än en enda fil beroende på tidpunkten för körningen.

4. Var uppmärksam på tidsstämplarna. AvroFileWriter-modulen laddar upp filerna till molnet när den senaste ändringstiden är mer\_\_än 10 minuter tidigare (se ÄNDRAD FIL TIMEOUT i uploader.py i avroFileWriter-modulen).

5. När de 10 minuterna har förflutit bör modulen ladda upp filerna. Om överföringen lyckas tas filerna bort från disken.

### <a name="azure-storage"></a>Azure Storage

Vi kan observera resultaten av vår lövenhet som skickar data genom att titta på lagringskonton där vi förväntar oss att data dirigeras.

1. På utvecklingsmaskinen öppna Visual Studio Code.

2. På panelen "AZURE STORAGE" i utforska-fönstret navigerar du i trädet för att hitta ditt lagringskonto.

3. Expandera noden **Blob Containers.**

4. Från det arbete vi gjorde i den tidigare delen av handledningen förväntar vi oss att **ruldata-behållaren** ska innehålla meddelanden med RUL. Expandera **ruldata-noden.**

5. Du kommer att se en eller `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`flera blob-filer med namnet: .

6. Högerklicka på en av filerna och välj **Ladda ner Blob** för att spara filen till din utvecklingsmaskin.

7. Nästa expandera **uploadturbofanfiles** nod. I föregående artikel ställer vi in den här platsen som mål för filer som laddats upp av avroFileWriter-modulen.

8. Högerklicka på filerna och välj **Ladda ner Blob** för att spara den på din utvecklingsmaskin.

### <a name="read-avro-file-contents"></a>Läs Avro-filinnehåll

Vi inkluderade ett enkelt kommandoradsverktyg för att läsa en Avro-fil och returnera en JSON-sträng av meddelandena i filen. I det här avsnittet kommer vi att installera och köra det.

1. Öppna en terminal i Visual Studio Code **(Terminal** > **New terminal).**

2. Installera hubavroreader:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

3. Använd hubavroreader för att läsa Avro-filen som du hämtade från **ruldata**.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

4. Observera att meddelandets brödtext ser ut som förväntat med enhets-ID och förväntat RUL.

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

5. Kör samma kommando som skickar Avro-filen som du hämtade från **uploadturbofanfiles**.

6. Som förväntat innehåller dessa meddelanden alla sensordata och driftinställningar från det ursprungliga meddelandet. Dessa data kan användas för att förbättra RUL-modellen på vår kantenhet.

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
           "EnqueuedTimeUtc": "<time>"
   }
   ```

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att utforska de resurser som används av den här end-to-end-självstudien väntar du tills du är klar med att rensa de resurser som du har skapat. Om du inte planerar att fortsätta använder du följande steg för att ta bort dem:

1. Ta bort resursgruppen/resursgrupperna som skapats för att innehålla dev-virtuella datorer, IoT Edge-virtuella datorer, IoT Hub, lagringskonto, maskininlärningsarbetsyta (och skapade resurser: behållarregister, programinsikter, nyckelvalv, lagringskonto).

2. Ta bort maskininlärningsprojektet i [Azure-anteckningsböcker](https://notebooks.azure.com).

3. Om du klonade repo lokalt stänger du alla PowerShell- eller VS-kodfönster som refererar till den lokala repo-et och tar sedan bort borttagningskatalogen.

4. Om du skapade certifikat lokalt tar du\\bort mappen c: edgeCertificates.

## <a name="next-steps"></a>Nästa steg

I den här artikeln använde vi vår utvecklingsmaskin för att simulera en lövenhet som skickar sensor och driftdata till vår kantenhet. Vi validerade att modulerna på enheten dirigeras, klassificeras, beständiga och laddade upp data först genom att undersöka realtidsdriften av kantenheten och sedan genom att titta på de filer som laddas upp till lagringskontot.

Mer information finns på följande sidor:

* [Ansluta en underordnad enhet till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
* [Lagra data på gränsen med Azure Blob Storage på IoT Edge (förhandsversion)](how-to-store-data-blob.md)
