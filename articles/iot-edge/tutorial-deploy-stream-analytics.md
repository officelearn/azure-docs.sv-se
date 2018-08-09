---
title: Självstudiekurs – Distribuera ASA-jobb till Azure IoT Edge-enheter | Microsoft Docs
description: I den här självstudien distribuerar du Azure Stream Analytics som en modul till en Iot Edge-enhet
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/25/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: afbdf2171c1fc1eef95514526a509d171e262d4a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435690"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module-preview"></a>Självstudie: Distribuera Azure Stream Analytics som en IoT Edge-modul (förhandsversion)

Många IoT-lösningar använder analystjänster för att få information om data när de tas emot i molnet från IoT-enheter. Med Azure IoT Edge kan du använda logik från [Azure Stream Analytics][azure-stream] och flytta den till själva enheten. Du kan minska hur mycket data som överförs och minska tiden det tar att reagera på insikter genom att bearbeta telemetriströmmar på gränsen.

Azure IoT Edge och Azure Stream Analytics är integrerade så att du kan skapa ett Azure Stream Analytics-jobb i Azure Portal och sedan distribuera det som en IoT Edge-modul utan ytterligare kod.  

Azure Stream Analytics tillhandahåller ett omfattande strukturerat frågesyntax för dataanalys både i molnet och på IoT Edge-enheter. Se [Dokumentation om Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md) för mer information om Azure Stream Analytics IoT Edge.

Stream Analytics-modulen i den här självstudien beräknar medeltemperaturen över ett rullande 30-sekundersschema. När den genomsnittliga temperaturen når 70 skickar modulen en varning så att enheten kan vidta en åtgärd. I det här fallet är åtgärden att återställa den simulerade temperatursensorn. Du kan till exempel använda den här funktionen för att stänga av en dator eller vidta förebyggande åtgärder när temperaturen når farliga nivåer i en produktionsmiljö. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett Azure Stream Analytics-jobb för bearbetning av data på gränsen.
> * Anslut det nya Azure Stream Analytics-jobbet med andra IoT Edge-moduler.
> * Distribuera Azure Stream Analytics-jobbet till en IoT Edge-enhet från Azure Portal.

>[!NOTE]
>Azure Stream Analytics-moduler för IoT Edge finns i en [allmänt tillgänglig förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

En Azure IoT Edge-enhet:

* Du kan använda utvecklingsdatorn eller en virtuell dator som en gränsenhet genom att följa stegen i snabbstarten för [Linux-](quickstart-linux.md) eller [Windows-enheter](quickstart.md).
* Azure Machine Learning-modulen stöder inte ARM-processorer.

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på standardnivå i Azure. 


## <a name="create-an-azure-stream-analytics-job"></a>Skapa ett Azure Stream Analytics-jobb

I det här avsnittet skapar du ett Azure Stream Analytics-jobb för att hämta data från IoT Hub, fråga de telemetridata som skickats från enheten och sedan skicka vidare resultaten till en Azure Blob Storage-container. Mer information finns i avsnittet ”Översikt” i [Stream Analytics-dokumentationen][azure-stream]. 

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Ett Azure Storage-konto krävs för att Azure Stream Analytics-jobb ska fungera. Det fungerar som en slutpunkt för jobbets utdata. Exemplet i det här avsnittet använder Blob Storage-typen. Mer information finns i avsnittet ”Blobar” i [Azure Storage-dokumentationen][azure-storage].

1. Gå till **Skapa en resurs** i Azure Portal, ange **Lagringskonto** i sökrutan och välj sedan **Lagringskonto – blob, fil, tabell, kö**.

1. Ange ett namn för ditt lagringskonto i fönstret **Skapa lagringskonto**, välj den plats där IoT Hub lagras, välj samma resursgrupp som din IoT Hub och sedan **Skapa**. Spara namnet för senare användning.

    ![skapar ett lagringskonto][1]


### <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

1. Gå till **Skapa en resurs** > **Sakernas Internet** och välj sedan **Stream Analytics-jobb** i Azure Portal.

1. Utför följande steg i fönstret **Nytt Stream Analytics-jobb**:

   1. Ange ett jobbnamn i rutan **jobbnamn**.
   
   1. Använd samma **Resursgrupp** och **Plats** som används av IoT Hub. 

      > [!NOTE]
      > För närvarande stöds inte Azure Stream Analytics-jobb på IoT Edge i regionen USA, västra 2. 

   1. Välj **Edge** under **Värdmiljö**.
    
1. Välj **Skapa**.

1. Öppna **Indata** under **Jobbtopologi** i det skapade jobbet.

   ![Azure Stream Analytics-indata](./media/tutorial-deploy-stream-analytics/asa_input.png)

1. Välj **Lägg till strömindata**och sedan **Edge Hub**.

1. Ange **temperatur** som inmatat alias i fönstret **Nya indata**. 

1. Välj **Spara**.

1. Öppna **Utdata** under **Jobbtopologi**.

   ![Azure Stream Analytics-utdata](./media/tutorial-deploy-stream-analytics/asa_output.png)

1. Välj **Lägg till**och sedan **Edge Hub**.

1. I rutan **Nya utdata** anger du **avisering** som utdataalias. 

1. Välj **Spara**.

1. Välj **Fråga** under **Jobbtopologi** och ersätt standardtexten med följande fråga som skapar en avisering om den genomsnittliga datortemperaturen når 70 grader inom en 30-sekundersperiod:

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

1. Välj **Spara**.

1. Under **Konfigurera** väljer du **inställningar för IoT Edge**.

1. Välj ditt **Lagringskonto** från den nedrullningsbara menyn.

1. För fältet **Container** väljer du **Skapa ny** och anger ett namn för lagringscontainern. 

1. Välj **Spara**. 


## <a name="deploy-the-job"></a>Distribuera jobbet

Du kan nu distribuera Azure Stream Analytics-jobbet till din IoT Edge-enhet.

1. I din IoT Hub i Azure Portal går du till **IoT Edge** och sedan öppnar du informationssidan för IoT Edge-enheter.

1. Välj **Ange moduler**.  

   Om du tidigare har distribuerat tempSensor-modulen på den här enheten kanske den fylls i automatiskt. Om inte så lägger du till modulen med följande steg:

   1. Klicka på **Lägg till** och välj **IoT Edge-modul**.
   1. Ange **tempSensor** som namn.
   1. Ange **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0** som URI för avbildning. 
   1. Lämna de andra inställningarna oförändrade.
   1. Välj **Spara**.

1. Lägg till ditt Azure Stream Analytics Edge-jobb med följande steg:

   1. Klicka på **Lägg till** och välj **Azure Stream Analytics-modul**.
   1. Välj din prenumeration och Azure Stream Analytics Edge-jobbet som du skapade. 
   1. Välj **Spara**.

1. Välj **Nästa**.

1. Ersätt standardvärdet i **Vägar** med följande kod. Uppdatera _{moduleName}_ med namnet på din Azure Stream Analytics-modul. Modulen ska ha samma namn som jobbet som det skapades från. 

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

1. Välj **Nästa**.

1. Välj **Skicka** i steget **Granska distribution**.

1. Återgå till informationssidan om enheten och välj sedan **Uppdatera**.  

    Du borde se att den nya Stream Analytics-modulen körs tillsammans med IoT Edge-agentmodulen och IoT Edge Hub.

    ![Utdata för modulen][7]

## <a name="view-data"></a>Visa data

Nu kan du gå till din IoT Edge-enhet för att se interaktionen mellan Azure Stream Analytics-modulen och tempSensor-modulen.

1. Kontrollera att alla moduler körs i Docker:

   ```cmd/sh
   iotedge list  
   ```
<!--
   ![Docker output][8]
-->
1. Visa alla systemloggar och statistikdata. Använd Stream Analytics-modulnamnet:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

Du bör kunna se datortemperaturen öka gradvis till den når 70 grader i 30 sekunder. Då utlöser Stream Analytics-modulen en återställning och datortemperaturen går tillbaka till 21. 

   ![Dockerlogg][9]

## <a name="clean-up-resources"></a>Rensa resurser 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Om du fortsätter till nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du redan har skapat och återanvända dem.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som skapats i den här artikeln om du vill undvika kostnader. 

> [!IMPORTANT]
> Det går inte att ångra borttagning av Azure-resurser och resursgrupper. När resursgruppen och alla resurser som ingår i den tas bort är de borta permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat IoT Hub:en inuti en befintlig resursgrupp som innehåller resurser som du vill behålla, ta bara bort själva IoT Hub-resursen i stället för att ta bort resursgruppen.
>

Om du endast vill ta bort körningen av IoT Hub kan du använda följande kommando med namnet på hubben och resursgruppens namn:

```azurecli-interactive
az iot hub delete --name {hub_name} --resource-group IoTEdgeResources
```


Ta bort hela resursgruppen med namnet:

1. Logga in på [Azure Portal](https://portal.azure.com) och klicka på **Resursgrupper**.

1. I textrutan **Filtrera efter namn ...** , skriver du namnet på resursgruppen som innehåller din IoT Hub. 

1. Till höger av din resursgrupp i resultatlistan klickar du på **...** och därefter **Ta bort resursgrupp**.

<!--
   ![Delete](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)
-->
1. Du blir ombedd att bekräfta borttagningen av resursgruppen. Skriv namnet på din resursgrupp igen för att bekräfta och klicka sedan på **Ta bort**. Efter en liten stund tas resursgruppen och resurser som finns i den bort.

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade du ett Azure Streaming Analytics-jobb för att analysera data från din IoT Edge-enhet. Sedan läste du in den här Azure Stream Analytics-modulen på din IoT Edge-enhet för att bearbeta och agera på ökningar i temperatur lokalt samt skicka den aggregerade dataströmmen till molnet. Om du vill se hur Azure IoT Edge kan skapa flera lösningar för ditt företag fortsätter du med de andra självstudiekurserna.

> [!div class="nextstepaction"] 
> [Distribuera en Azure Machine Learning-modell som en modul][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output2.png
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
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

