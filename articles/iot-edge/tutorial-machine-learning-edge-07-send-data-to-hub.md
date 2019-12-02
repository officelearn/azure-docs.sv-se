---
title: 'Självstudie: skicka data via gateway – Azure IoT Edge & Machine Learning'
description: 'Självstudie: Använd din utvecklings dator som en simulerad IoT Edge enhet för att skicka data till IoT Hub genom att gå igenom en enhet som kon figurer ATS som en transparent Gateway.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cdd1e1d83b1d184e6a904fc64e9c2395c2896d54
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665635"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Självstudie: skicka data via transparent Gateway

> [!NOTE]
> Den här artikeln ingår i en serie för självstudier om hur du använder Azure Machine Learning på IoT Edge. Om du har kommit till den här artikeln direkt rekommenderar vi att du börjar med den [första artikeln](tutorial-machine-learning-edge-01-intro.md) i serien för bästa möjliga resultat.

I den här artikeln använder vi utvecklings datorn igen som en simulerad enhet, men istället för att skicka data direkt till den IoT Hub enheten skickar data till den IoT Edge-enhet som kon figurer ATS som en transparent Gateway.

Vi övervakar IoT Edge enhetens funktion medan den simulerade enheten skickar data. När enheten har körts klart tittar vi på data i vårt lagrings konto för att kontrol lera att allt fungerade som förväntat.

Det här steget utförs vanligt vis av en moln-eller enhets utvecklare.

## <a name="review-device-harness"></a>Granska enhetens nät

Återanvänd [DeviceHarness-projektet](tutorial-machine-learning-edge-03-generate-data.md) för att simulera den underordnade enheten (eller löv). För att ansluta till den transparenta gatewayen krävs ytterligare två saker:

* Registrera certifikatet för att göra den underordnade enheten (i det här fallet vår utvecklings dator) förtroende för den certifikat utfärdare som används av den IoT Edge körningen.
* Lägg till det fullständigt kvalificerade domän namnet (FQDN) för Edge gateway i enhets anslutnings strängen.

Titta på koden för att se hur dessa två objekt implementeras.

1. Öppna Visual Studio Code på din utvecklings dator.

2. Använd **filen** > **Öppna mappen...** för att öppna C:\\source\\IoTEdgeAndMlSample\\DeviceHarness.

3. Titta på metoden InstallCertificate () i Program.cs.

4. Observera att om koden hittar certifikat Sök vägen anropas metoden CertificateManager. InstallCACert för att installera certifikatet på datorn.

5. Titta nu på GetIotHubDevice-metoden i TurbofanDevice-klassen.

6. När användaren anger det fullständiga domän namnet för gatewayen med alternativet "-g" skickas värdet till den här metoden som gatewayFqdn, som läggs till i enhets anslutnings strängen.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Skapa och kör löv enhet

1. När projektet DeviceHarness fortfarande är öppet i Visual Studio Code skapar du projektet (CTRL + SHIFT + B eller **Terminal** > **Kör skapa uppgift...** ) och väljer **skapa** i dialog rutan.

2. Hitta det fullständigt kvalificerade domän namnet (FQDN) för din Edge-Gateway genom att gå till din IoT Edge enhets virtuella dator på portalen och kopiera värdet för **DNS-namn** från översikten.

3. Öppna Visual Studio Code Terminal (**terminal** > **New Terminal**) och kör följande kommando och ersätt `<edge_device_fqdn>` med det DNS-namn som du kopierade från den virtuella datorn:

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

4. Programmet försöker installera certifikatet på din utvecklings dator. När det gör det accepterar du säkerhets varningen.

5. När du uppmanas att ange IoT Hub anslutnings sträng klickar du på ellipsen ( **...** ) på panelen Azure IoT Hub enheter och väljer **Kopiera IoT Hub anslutnings sträng**. Klistra in värdet i terminalen.

6. Utdata visas som:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Observera att tillägget "GatewayHostName" läggs till i enhets anslutnings strängen, vilket gör att enheten kommunicerar via IoT Hub via den IoT Edge transparenta gatewayen.

## <a name="check-output"></a>Kontrol lera utdata

### <a name="iot-edge-device-output"></a>IoT Edge enhets utdata

Utdata från avroFileWriter-modulen kan observeras enkelt genom att titta på den IoT Edge enheten.

1. SSH i din IoT Edge virtuella dator.

2. Sök efter filer som skrivs till disk.

   ```bash
   find /data/avrofiles -type f
   ```

3. Kommandots utdata ser ut som i följande exempel:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Du kan ha fler än en fil beroende på tidpunkten för körningen.

4. Var uppmärksam på tidsstämplar. AvroFileWriter-modulen överför filerna till molnet när den senaste ändrings tiden är mer än 10 minuter tidigare (se MODIFIERAd\_fil\_TIMEOUT i uploader.py i avroFileWriter-modulen).

5. När 10 minuter har förflutit ska modulen Ladda upp filerna. Om överföringen lyckas tas filerna bort från disken.

### <a name="azure-storage"></a>Azure Storage

Vi kan se resultatet av vår löv enhet som skickar data genom att titta på de lagrings konton där vi förväntar sig att data ska vidarebefordras.

1. Öppna Visual Studio Code på utvecklings datorn.

2. I panelen "AZURE STORAGE" i fönstret utforska navigerar du i trädet för att hitta ditt lagrings konto.

3. Expandera noden **BLOB-behållare** .

4. Från arbetet som vi gjorde i föregående del av själv studie kursen förväntar vi dig att **ruldata** -behållaren ska innehålla meddelanden med RUL. Expandera noden **ruldata** .

5. Du kommer att se en eller flera BLOB-filer som heter: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`.

6. Högerklicka på en av filerna och välj **Ladda ned BLOB** för att spara filen på din utvecklings dator.

7. Expandera sedan **uploadturbofanfiles** -noden. I föregående artikel anger vi den här platsen som mål för filer som laddats upp av avroFileWriter-modulen.

8. Högerklicka på filerna och välj **Hämta BLOB** för att spara den på din utvecklings dator.

### <a name="read-avro-file-contents"></a>Läsa innehållet i Avro-filen

Vi har inkluderat ett enkelt kommando rads verktyg för att läsa en Avro-fil och returnera en JSON-sträng för meddelandena i filen. I det här avsnittet ska vi installera och köra det.

1. Öppna en Terminal i Visual Studio Code (**terminal** > **New Terminal**).

2. Installera hubavroreader:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

3. Använd hubavroreader för att läsa Avro-filen som du laddade ned från **ruldata**.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

4. Observera att bröd texten i meddelandet ser ut som förväntat med enhets-ID och förutsägande RUL.

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

5. Kör samma kommando och skicka Avro-filen som du laddade ned från **uploadturbofanfiles**.

6. Som förväntat innehåller dessa meddelanden alla sensor data och drift inställningar från det ursprungliga meddelandet. Dessa data kan användas för att förbättra RUL-modellen på vår gräns enhet.

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

Om du planerar att utforska de resurser som används av den här självstudien väntar du tills du är klar för att rensa de resurser som du har skapat. Om du inte planerar att fortsätta använder du följande steg för att ta bort dem:

1. Ta bort de resurs grupper som har skapats för att lagra den virtuella dev-datorn, IoT Edge VM, IoT Hub, lagrings konto, Machine Learning-arbetsyta (och skapade resurser: container Registry, Application Insights, Key Vault, lagrings konto).

2. Ta bort Machine Learning-projektet i [Azure Notebooks](https://notebooks.azure.com).

3. Om du har klonat lagrings platsen lokalt, Stäng alla PowerShell-eller VS Code-fönster som refererar till den lokala lagrings platsen och ta sedan bort lagrings platsen-katalogen.

4. Om du har skapat certifikat lokalt, tar du bort mappen c:\\edgeCertificates.

## <a name="next-steps"></a>Nästa steg

I den här artikeln använde vi utvecklings datorn för att simulera en löv enhet som skickar sensor-och drift data till vår gräns enhet. Vi validerade att modulerna på enheten dirigerade, klassificerade, sparade och laddade upp data först genom att undersöka real tids åtgärden för gräns enheten och sedan titta på filerna som överförts till lagrings kontot.

Mer information finns på följande sidor:

* [Ansluta en underordnad enhet till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
* [Lagra data på gränsen med Azure Blob Storage på IoT Edge (förhands granskning)](how-to-store-data-blob.md)
