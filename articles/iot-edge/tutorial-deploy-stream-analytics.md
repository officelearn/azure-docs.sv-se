---
title: Distribuera Azure Stream Analytics med Azure IoT-Edge | Microsoft Docs
description: Distribuera Azure Stream Analytics som en modul till en insticksenhet
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0d19d1142cf15221f84692f7e613edd6b46b4083
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Distribuera Azure Stream Analytics som en gräns för IoT-modul - förhandsgranskning

IoT-enheter kan generera stora mängder data. Dessa data har ibland analyseras eller behandlas innan det nådde molnet att minska storleken på överförda data eller att ta bort en tillämplig insight fördröjningen.

[Azure Stream Analytics] [ azure-stream] (ASA) ger en mycket strukturerade frågesyntaxen för dataanalys, både i molnet och på IoT kant-enheter. Mer information om ASA IoT kant finns [ASA dokumentationen](../stream-analytics/stream-analytics-edge.md).

Den här självstudiekursen vägleder dig genom att skapa ett Azure Stream Analytics-jobb och dess distribution på en IoT-enhet för att behandla en dataström med lokala telemetri direkt på enheten och generera aviseringar till enheten omedelbara åtgärder på enheten.  Det finns två moduler som ingår i den här kursen. En simulerad temperatursensor modul (tempSensor) som genererar temperatur data från 20 till 120 grader ökar med 1 5 sekunder och en ASA-modul som filtrerar ut temperaturer som är större än 100 grader. Modulen ASA återställs även tempSensor när 30 sekunder medelvärdet når 100.

Lär dig att:

> [!div class="checklist"]
> * Skapa ett ASA jobb för bearbetning av data på kant
> * Anslut det nya jobbet ASA med andra IoT kant-moduler
> * Distribuera ASA jobbet till en IoT-enhet

## <a name="prerequisites"></a>Krav

* En IoT-hubb 
* Den enhet som du skapat och konfigurerat i Snabbstart eller distribuera Azure IoT kant om en simulerad enhet i [Windows] [ lnk-tutorial1-win] och [Linux] [ lnk-tutorial1-lin].
* Docker på enheten IoT kant
    * [Installera Docker på Windows] [ lnk-docker-windows] och se till att den körs.
    * [Installera Docker på Linux] [ lnk-docker-linux] och se till att den körs.
* Python 2.7.x på enheten IoT kant
    * [Installera Python 2.7 i Windows][lnk-python].
    * De flesta Linux-distributioner, inklusive Ubuntu, har redan Python 2.7 installerad.  Använd följande kommando för att kontrollera pip är installerad: `sudo apt-get install python-pip`.

> [!NOTE]
> Observera att din enhet anslutningssträngen och IoT kant enhets-ID blir den här kursen.

IoT-Edge drar nytta av de fördefinierade Azure Service IoT kant-moduler för snabbdistribution och Azure Stream Analytics (ASA) är en modul. Du kan skapa ett ASA jobb från en portal och sedan gå till IoT-hubb-portalen för att distribuera det som en modul för IoT-kant.  

Mer information om Azure Stream Analytics finns på **översikt** avsnitt i den [Stream Analytics dokumentationen][azure-stream].

## <a name="create-an-asa-job"></a>Skapa ett ASA-jobb

I det här avsnittet skapar du ett Azure Stream Analytics-jobb för att arbeta med data från IoT-hubb, fråga skickas telemetridata från enheten och vidarebefordra resultaten till en Azure Storage behållare (BLOB). Mer information finns i **översikt** avsnitt i den [Stream Analytics dokumentationen][azure-stream]. 

> [!NOTE]
> Ett Azure Storage-konto krävs för att tillhandahålla en slutpunkt som ska användas som utdata i ASA-jobbet. Exemplet nedan används BLOB storage.  Mer information finns i **Blobbar** avsnitt i den [Azure Storage-dokumentation][azure-storage].

1. I Azure-portalen går du till **skapa en resurs -> lagring**, klickar du på **se alla**, och klicka på **lagringskonto - blob, fil, tabell, kö**.

2. Ange ett namn för ditt lagringskonto och välj den plats där din IoT-hubb lagras. Klicka på **Skapa**. Notera namnet för senare.

    ![Nytt lagringskonto][1]

3. Navigera till lagringskontot som du skapade i Azure-portalen. Klicka på **Bläddra blobbar** under **Blob-tjänsten**. 
4. Skapa en ny behållare för ASA-modulen att lagra data. Anger åtkomsten till _behållare_. Klicka på **OK**.

    ![inställningar för lagring][10]

5. I Azure-portalen går du till **skapar du en resurs** > **Sakernas Internet** och välj **Stream Analytics-jobbet**.

2. Ange ett namn, Välj **kant** som värd-miljön och använda de återstående standardvärdena.  Klicka på **Skapa**.

    >[!NOTE]
    >För närvarande stöds ASA jobb på IoT Edge inte i oss West-2-region. Välj en annan plats.

    ![Skapa ASA][5]

2. Gå till jobbet skapats under **jobbet topologi**väljer **indata**, klickar du på **Lägg till**.

3. Ange namnet `temperature`, Välj **dataströmmen** som typ av datakälla och Använd standardvärden för de andra parametrarna. Klicka på **Skapa**.

    ![ASA indata][2]

    > [!NOTE]
    > Ytterligare indata kan innehålla specifika IoT kant-slutpunkter.

4. Under **jobbet topologi**väljer **utdata**, klickar du på **Lägg till**.

5. Ange namnet `alert` och Använd standardvärden. Klicka på **Skapa**.

    ![ASA utdata][3]

6. Under **jobbet topologi**väljer **frågan**, och ange följande:

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 100
    ```

## <a name="deploy-the-job"></a>Distribuera projektet

Du är nu redo att distribuera ASA jobbet på enheten IoT kant.

1. Navigera till i Azure-portalen i din IoT-hubb **IoT kant (förhandsgranskning)** och öppna din *{deviceId}*'s bladet.

1. Välj **ange moduler**och välj **Import Azure Service IoT kant Module**.

1. Välj prenumerationen och jobbet ASA kant som du skapade. Välj ditt lagringskonto. Klicka på **Spara**.

    ![set-modul][6]

1. Klicka på **Lägg till IoT kant modul** temperatursensor modulen ska läggas till. Ange _tempSensor_ för namn, `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` för bild-URL. Lämna de andra inställningarna oförändrad och klicka på **spara**.

    ![temperatur-modul][11]

1. Kopiera ASA modulens namn. Klicka på **nästa** att konfigurera flöden.

1. Kopiera följande **vägar**.  Ersätt _{moduleName}_ med modulnamnet som du kopierade:

    ```json
    {
        "routes": {                                                               
          "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream", 
          "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream", 
          "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")", 
          "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")" 
        }
    }
    ```

1. Klicka på **Nästa**.

1. I den **granska mallen** , klicka på **skicka**.

1. Gå tillbaka till informationssidan och klickar på **uppdatera**.  Du bör se den nya _{moduleName}_ modulen körs tillsammans med den **IoT kant agent** modulen och **kant för IoT-hubb**.

    ![modul-utdata][7]

## <a name="view-data"></a>Visa data

Nu kan du gå till din IoT gränsenheten för att kolla interaktionen mellan ASA modulen och modulen tempSensor.

1. Konfigurera körningen med anslutningssträngen kant för IoT-enhet i en kommandotolk:

    ```cmd/sh
    iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords  
    ```

1. Kör kommandot för att starta körningen:

    ```cmd/sh
    iotedgectl start  
    ```

1. Kör kommandot för att se de moduler som körs:

    ```cmd/sh
    docker ps  
    ```

    ![docker-utdata][8]

1. Kör kommandot för att se alla systemloggar och mått data. Använd Modulnamn ovan:

    ```cmd/sh
    docker logs -f {moduleName}  
    ```

    ![docker-logg][9]

1. I Azure-portalen på ditt lagringskonto under **Blob-tjänsten**, klickar du på **Bläddra blobbar**, Välj din behållare och välj nyligen skapade JSON-fil.

1. Klicka på **hämta** och visa resultatet.

## <a name="next-steps"></a>Nästa steg

I kursen får du har konfigurerat en Azure Storage-behållare och en Streaming Analytics-jobbet att analysera data från IoT-Edge-enhet.  Du laddat sedan en anpassad ASA-modul för att flytta data från enheten via stream, till en BLOB för hämtning.  Du kan fortsätta till andra självstudiekurser för att ytterligare kontrollera hur Azure IoT kant kan skapa lösningar för ditt företag.

> [!div class="nextstepaction"] 
> [Distribuera en Azure Machine Learning-modell som en modul][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[2]: ./media/tutorial-deploy-stream-analytics/asa_input.png
[3]: ./media/tutorial-deploy-stream-analytics/asa_output.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/en-us/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/en-us/azure/storage/
[azure-stream]: https://docs.microsoft.com/en-us/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/