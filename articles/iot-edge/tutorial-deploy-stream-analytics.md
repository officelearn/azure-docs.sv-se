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
ms.openlocfilehash: f40fb81fc03e796b906db12bf3bf6904b27b46eb
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2018
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Distribuera Azure Stream Analytics som en gräns för IoT-modul - förhandsgranskning

IoT-enheter kan generera stora mängder data. Minska mängden överförda data eller eliminera fördröjningen av en tillämplig insikt måste ibland data analyseras eller bearbetas innan den når molnet.

Azure IoT-Edge utnyttjar förskapad Azure service IoT kant moduler för snabbdistribution. [Azure Stream Analytics] [ azure-stream] är en modul. Du kan skapa ett Azure Stream Analytics-jobb från en portal och gå sedan till den Azure IoT Hub-portalen för att distribuera det som en IoT-Edge-modul. 

Azure Stream Analytics ger en mycket strukturerade frågesyntaxen för dataanalys, både i molnet och på IoT kant enheter. Mer information om Azure Stream Analytics IoT kant finns [Azure Stream Analytics dokumentationen](../stream-analytics/stream-analytics-edge.md).

Den här självstudiekursen vägleder dig genom att skapa ett Azure Stream Analytics-jobb och distribuera den på en IoT-enhet. På så sätt kan bearbeta en dataström med lokala telemetri direkt på enheten och generera aviseringar som enhet omedelbara åtgärder på enheten. 

Kursen visar två moduler: 
* En simulerad temperatursensor modul (tempSensor) som genererar temperatur data från 20 till 120 grader, ökar med 1 5 sekunder. 
* En Stream Analytics-modul som återställer tempSensor när 30 sekunder medelvärdet når 70. Du kan använda den här funktionen att stänga av en dator eller vidta förebyggande åtgärder när temperaturen når farliga nivåer i en produktionsmiljö. 

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa ett Azure Stream Analytics-jobb för bearbetning av data på gränsen.
> * Anslut det nya Azure Stream Analytics-jobbet med andra IoT kant-moduler.
> * Distribuera Azure Stream Analytics-jobbet till en IoT-enhet.

## <a name="prerequisites"></a>Förutsättningar

* En IoT-hubb. 
* Den enhet som du skapat och konfigurerat i Snabbstart eller i artiklar om hur du distribuerar Azure IoT kanten på en simulerad enhet i [Windows] [ lnk-tutorial1-win] eller i [Linux] [ lnk-tutorial1-lin]. Du behöver veta anslutningsnyckel enhet och enheten-ID. 
* Docker körs på enheten IoT kant.
    * [Installera Docker på Windows][lnk-docker-windows].
    * [Installera Docker på Linux][lnk-docker-linux].
* Python 2.7.x på enheten IoT kant.
    * [Installera Python 2.7 i Windows][lnk-python].
    * De flesta Linux-distributioner, inklusive Ubuntu, har redan Python 2.7 installerad. För att säkerställa att pip är installerad, använder du följande kommando: `sudo apt-get install python-pip`.

## <a name="create-an-azure-stream-analytics-job"></a>Skapa ett Azure Stream Analytics-jobb

I det här avsnittet skapar du ett Azure Stream Analytics-jobb för att hämtar data från IoT-hubb, fråga skickas telemetridata från enheten och sedan skicka resultaten till en Azure Blob storage-behållare. Mer information finns i avsnittet ”Overview” i den [Stream Analytics dokumentationen][azure-stream]. 

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Ett Azure Storage-konto krävs för att tillhandahålla en slutpunkt som ska användas som utdata i Azure Stream Analytics-jobbet. Exemplet i det här avsnittet använder Blob storage-datatyp. Mer information finns i avsnittet ”BLOB” i den [Azure Storage-dokumentationen][azure-storage].

1. I Azure-portalen går du till **skapar du en resurs**, ange **lagringskonto** i sökrutan och väljer sedan **lagringskonto - blob, fil, tabell, kö**.

2. I den **skapa lagringskonto** rutan Ange ett namn för ditt lagringskonto, Välj samma plats där din IoT-hubb lagras och välj sedan **skapa**. Notera namnet för senare användning.

    ![skapar ett lagringskonto][1]

3. Gå till lagringskontot som du just skapat och välj sedan **Bläddra blobbar**. 

4. Skapa en ny behållare för Azure Stream Analytics-modulen att lagra data genom att ange åtkomst till **behållare**, och välj sedan **OK**.

    ![inställningar för lagring][10]

### <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

1. I Azure-portalen går du till **skapar du en resurs** > **Sakernas Internet**, och välj sedan **Stream Analytics-jobbet**.

2. I den **nya Stream Analytics-jobbet** rutan gör du följande:

    a. I den **jobbnamn** skriver ett jobbnamn.
    
    b. Under **värd miljö**väljer **kant**.
    
    c. Använd standardvärdena i de återstående fälten.

    > [!NOTE]
    > Azure Stream Analytics-jobb på IoT kant stöds för närvarande inte i regionen västra USA 2. 

3. Välj **Skapa**.

4. Jobb som skapats under **jobbet topologi**väljer **indata**, och välj sedan **Lägg till**.

5. I den **nya indata** rutan gör du följande:

    a. I den **indata alias** ange **temperatur**.
    
    b. I den **källtypen** väljer **dataströmmen**.
    
    c. Använd standardvärdena i de återstående fälten.

   ![Azure Stream Analytics-indata](./media/tutorial-deploy-stream-analytics/asa_input.png)

6. Välj **Skapa**.

7. Under **jobbet topologi**väljer **utdata**, och välj sedan **Lägg till**.

8. I den **nya utdata** rutan gör du följande:

    a. I den **utdataalias** skriver **avisering**.
    
    b. Använd standardvärdena i de återstående fälten. 
    
    c. Välj **Skapa**.

   ![Azure Stream Analytics-utdata](./media/tutorial-deploy-stream-analytics/asa_output.png)


9. Under **jobbet topologi**väljer **frågan**, och ersätt standardtexten med följande fråga:

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

10. Välj **Spara**.

## <a name="deploy-the-job"></a>Distribuera projektet

Du är nu redo att distribuera Azure Stream Analytics-jobbet på enheten IoT kant.

1. I Azure-portalen i din IoT-hubb går du till **IoT kant (förhandsgranskning)**, och sedan öppna informationssidan för enheten IoT kant.

2. Välj **ange moduler**.  
    Om du tidigare har distribuerat modulen tempSensor på den här enheten kan autopopulate. Om det inte att lägga till modulen genom att göra följande:

   a. Välj **lägga till IoT kant modul**.

   b. Namnet på, skriver **tempSensor**.
    
   c. Avbildningen URI, ange **microsoft/azureiotedge-simulerade-temperatur-sensor: 1.0-preview**. 

   d. Lämna de andra inställningarna oförändrade.
   
   e. Välj **Spara**.

3. Om du vill lägga till kant för Azure Stream Analytics-jobb, Välj **Import Azure Stream Analytics IoT kant Module**.

4. Välj din prenumeration och Azure Stream Analytics Edge jobbet som du skapade. 

5. Välj din prenumeration och lagringskontot som du skapade och välj sedan **spara**.

    ![set-modul][6]

6. Kopiera namnet på Azure Stream Analytics-modul. 

    ![temperatur-modul][11]

7. Om du vill konfigurera vägar, Välj **nästa**.

8. Kopiera följande kod **vägar**. Ersätt _{moduleName}_ namnet på den modul som du kopierade:

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

9. Välj **nästa**.

10. I den **granska mallen** steg, Välj **skicka**.

11. Gå tillbaka till informationssidan om enheten och välj sedan **uppdatera**.  
    Du bör se den nya Stream Analytics-modulen körs tillsammans med modulen IoT kant agent och kant för IoT-hubben.

    ![modul-utdata][7]

## <a name="view-data"></a>Visa data

Nu kan du gå till din IoT gränsenheten för att kolla interaktionen mellan Azure Stream Analytics-modulen och modulen tempSensor.

1. Kontrollera att alla moduler som körs i Docker:

   ```cmd/sh
   docker ps  
   ```

   ![docker-utdata][8]

2. Visa alla loggar och mått systemdata. Använd Stream Analytics Modulnamn:

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

Du ska kunna titta på den datorn temperatur gradvis öka tills det når 70 grader i 30 sekunder. Sedan modulen Stream Analytics utlöser en återställning och datorn temperatur släpper tillbaka till 21. 

   ![docker-logg][9]


## <a name="next-steps"></a>Nästa steg

I kursen får du har konfigurerat en Azure storage-behållare och en Streaming Analytics-jobbet att analysera data från IoT-Edge-enhet. Du laddat sedan en anpassad Azure Stream Analytics-modul för att flytta data från enheten via stream, till en blob för hämtning. Om du vill se hur Azure IoT kant kan skapa flera lösningar för ditt företag, fortsätter du med andra självstudiekurser.

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
[azure-iot]: https://docs.microsoft.com/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/azure/storage/
[azure-stream]: https://docs.microsoft.com/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/
