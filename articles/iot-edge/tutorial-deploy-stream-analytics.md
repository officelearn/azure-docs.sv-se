---
title: Distribuera Azure Stream Analytics med Azure IoT-Edge | Microsoft Docs
description: Distribuera Azure Stream Analytics som en modul till en insticksenhet
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/28/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5a143bbf7abb5304ac51782d517c02ec184a05a2
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Distribuera Azure Stream Analytics som en gräns för IoT-modul - förhandsgranskning

IoT-enheter kan generera stora mängder data. Dessa data har ibland analyseras eller behandlas innan det nådde molnet att minska storleken på överförda data eller att ta bort en tillämplig insight fördröjningen.

IoT-Edge drar nytta av de fördefinierade Azure Service IoT kant-moduler för snabbdistribution och [Azure Stream Analytics] [ azure-stream] (ASA) är en modul. Du kan skapa ett ASA jobb från en portal och sedan gå till IoT-hubb-portalen för att distribuera det som en modul för IoT-kant.  

Azure Stream Analytics ger en mycket strukturerade frågesyntaxen för dataanalys, både i molnet och på IoT kant enheter. Mer information om ASA IoT kant finns [ASA dokumentationen](../stream-analytics/stream-analytics-edge.md).

Den här självstudiekursen vägleder dig genom att skapa ett Azure Stream Analytics-jobb och dess distribution på en IoT-enhet för att behandla en dataström med lokala telemetri direkt på enheten och generera aviseringar till enheten omedelbara åtgärder på enheten.  Det finns två moduler som ingår i den här kursen. En simulerad temperatursensor-modul (tempSensor) genererar temperatur data från 20 till 120 grader, ökar med 1 5 sekunder. En Stream Analytics-modul återställer tempSensor när 30 sekunder medelvärdet når 70. Den här funktionen kan användas att stänga av en dator eller vidta förebyggande åtgärder när temperaturen når farliga nivåer i en produktionsmiljö. 

Lär dig att:

> [!div class="checklist"]
> * Skapa ett ASA jobb för bearbetning av data på kant
> * Anslut det nya jobbet ASA med andra IoT kant-moduler
> * Distribuera ASA jobbet till en IoT-enhet

## <a name="prerequisites"></a>Krav

* En IoT-hubb 
* Den enhet som du skapat och konfigurerat i Snabbstart eller distribuera Azure IoT kant om en simulerad enhet i [Windows] [ lnk-tutorial1-win] och [Linux] [ lnk-tutorial1-lin]. Du behöver veta anslutningsnyckel enhet och enheten-ID. 
* Docker som körs på enheten IoT kant
    * [Installera Docker i Windows][lnk-docker-windows]
    * [Installera Docker på Linux][lnk-docker-linux]
* Python 2.7.x på enheten IoT kant
    * [Installera Python 2.7 i Windows][lnk-python].
    * De flesta Linux-distributioner, inklusive Ubuntu, har redan Python 2.7 installerad.  Använd följande kommando för att kontrollera pip är installerad: `sudo apt-get install python-pip`.


## <a name="create-an-asa-job"></a>Skapa ett ASA-jobb

I det här avsnittet skapar du ett Azure Stream Analytics-jobb för att arbeta med data från IoT-hubb, fråga skickas telemetridata från enheten och vidarebefordra resultaten till en Azure Storage behållare (BLOB). Mer information finns i **översikt** avsnitt i den [Stream Analytics dokumentationen][azure-stream]. 

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Ett Azure Storage-konto krävs för att tillhandahålla en slutpunkt som ska användas som utdata i ASA-jobbet. Exemplet nedan används BLOB storage.  Mer information finns i **Blobbar** avsnitt i den [Azure Storage-dokumentation][azure-storage].

1. I Azure-portalen går du till **skapar du en resurs** och ange `Storage account` i sökfältet. Välj **lagringskonto - blob, fil, tabell, kö**.

2. Ange ett namn för ditt lagringskonto och välj den plats där din IoT-hubb finns. Klicka på **Skapa**. Kom ihåg namnet för senare.

    ![Nytt lagringskonto][1]

3. Navigera till lagringskontot som du nyss skapade. Klicka på **Bläddra blobbar**. 
4. Skapa en ny behållare för ASA-modulen att lagra data. Anger åtkomsten till **behållare**. Klicka på **OK**.

    ![inställningar för lagring][10]

### <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

1. I Azure-portalen går du till **skapar du en resurs** > **Sakernas Internet** och välj **Stream Analytics-jobbet**.

2. Ange ett namn, Välj **kant** som värd-miljön och använda de återstående standardvärdena.  Klicka på **Skapa**.

    >[!NOTE]
    >ASA jobb på IoT kant stöds för närvarande inte i regionen västra USA 2. 

3. Gå till skapade jobbet. Välj **indata** Klicka **Lägg till**.

4. Inmatningsaliaset ange `temperature`, ange typen av datakälla till **dataströmmen**, och använda standardvärden för de andra parametrarna. Klicka på **Skapa**.

   ![ASA indata](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. Välj **utdata** Klicka **Lägg till**.

6. Aliaset utdata ange `alert`, och använda standardvärden för de andra parametrarna. Klicka på **Skapa**.

   ![ASA utdata](./media/tutorial-deploy-stream-analytics/asa_output.png)


7. Välj **frågan**.
8. Ersätt standardtexten med följande fråga:

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```
9. Klicka på **Spara**.

## <a name="deploy-the-job"></a>Distribuera projektet

Du är nu redo att distribuera ASA jobbet på enheten IoT kant.

1. Navigera till i Azure-portalen i din IoT-hubb **IoT kant (förhandsgranskning)** och öppna sidan för din IoT-Edge-enhet.
1. Välj **ange moduler**.
1. Om du tidigare har distribuerat modulen tempSensor på den här enheten kan autopopulate. Om inte, Använd följande steg för att modulen ska läggas till:
   1. Klicka på **lägga till kant för IoT-modul**
   1. Ange `tempSensor` som namn, och `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` för bild-URI. 
   1. Lämna de andra inställningarna oförändrad och klicka på **spara**.
1. Lägg till ASA Edge-jobb, markera **Import Azure Stream Analytics IoT kant Module**.
1. Välj din prenumeration och jobbet ASA kant som du skapade. 
1. Välj din prenumeration och lagringskontot som du skapade. Klicka på **Spara**.

    ![set-modul][6]

1. Kopiera det namn som har genererats automatiskt för ASA-modulen. 

    ![temperatur-modul][11]

1. Klicka på **nästa** att konfigurera flöden.
1. Kopiera följande **vägar**.  Ersätt _{moduleName}_ namnet på den modul som du kopierade:

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

1. Gå tillbaka till informationssidan och klickar på **uppdatera**.  Du bör se den nya Stream Analytics-modulen körs tillsammans med den **IoT kant agent** modulen och **kant för IoT-hubb**.

    ![modul-utdata][7]

## <a name="view-data"></a>Visa data

Nu kan du gå till din IoT gränsenheten för att kolla interaktionen mellan ASA modulen och modulen tempSensor.

Kontrollera att alla moduler som körs i Docker:

   ```cmd/sh
   docker ps  
   ```

   ![docker-utdata][8]

Se alla loggar och mått systemdata. Använd Stream Analytics Modulnamn:

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

Du ska kunna titta på den datorn temperatur gradvis öka tills det når 70 grader i 30 sekunder. Sedan modulen Stream Analytics utlöser en återställning och datorn temperatur släpper tillbaka till 21. 

   ![docker-logg][9]


## <a name="next-steps"></a>Nästa steg

I kursen får du har konfigurerat en Azure Storage-behållare och en Streaming Analytics-jobbet att analysera data från IoT-Edge-enhet.  Du laddat sedan en anpassad ASA-modul för att flytta data från enheten via stream, till en BLOB för hämtning.  Du kan fortsätta till andra självstudiekurser för att ytterligare kontrollera hur Azure IoT kant kan skapa lösningar för ditt företag.

> [!div class="nextstepaction"] 
> [Distribuera en Azure Machine Learning-modell som en modul][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
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