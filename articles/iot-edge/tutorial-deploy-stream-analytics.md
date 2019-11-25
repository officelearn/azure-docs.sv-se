---
title: 'Tutorial: Stream Analytics at the edge - Azure IoT Edge'
description: In this tutorial, you deploy Azure Stream Analytics as a module to an IoT Edge device
author: kgremban
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 7fbbe32efcedd4fa2635db1cc21f7ce98557515b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452526"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Tutorial: Deploy Azure Stream Analytics as an IoT Edge module

Many IoT solutions use analytics services to gain insight about data as it arrives in the cloud from IoT devices. Med Azure IoT Edge kan du använda logik från [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) och flytta den till själva enheten. Du kan minska hur mycket data som överförs och minska tiden det tar att reagera på insikter genom att bearbeta telemetriströmmar på gränsen.

Azure IoT Edge och Azure Stream Analytics är integrerade så att du kan skapa ett Azure Stream Analytics-jobb i Azure Portal och sedan distribuera det som en IoT Edge-modul utan ytterligare kod.  

Azure Stream Analytics provides a richly structured query syntax for data analysis, both in the cloud and on IoT Edge devices. For more information, see [Azure Stream Analytics documentation](../stream-analytics/stream-analytics-edge.md).

Stream Analytics-modulen i den här självstudien beräknar medeltemperaturen över ett rullande 30-sekundersschema. När medeltemperaturen når 70 grader skickar modulen en varning så att enheten kan vidta en åtgärd. I det här fallet är åtgärden att återställa den simulerade temperatursensorn. Du kan till exempel använda den här funktionen för att stänga av en dator eller vidta förebyggande åtgärder när temperaturen når farliga nivåer i en produktionsmiljö. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett Azure Stream Analytics-jobb för bearbetning av data på gränsen.
> * Anslut det nya Azure Stream Analytics-jobbet med andra IoT Edge-moduler.
> * Distribuera Azure Stream Analytics-jobbet till en IoT Edge-enhet från Azure Portal.

<center>

![Diagram - Tutorial architecture, stage and deploy ASA job](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

En Azure IoT Edge-enhet:

* You can use an Azure virtual machine as an IoT Edge device by following the steps in the quickstart for [Linux](quickstart-linux.md) or [Windows devices](quickstart.md).

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure. 


## <a name="create-an-azure-stream-analytics-job"></a>Skapa ett Azure Stream Analytics-jobb

In this section, you create an Azure Stream Analytics job that will do the following steps:
* Receive data from your IoT Edge device.
* Query the telemetry data for values outside a set range.
* Take action on the IoT Edge device based on the query results. 

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

När du skapar ett Azure Stream Analytics-jobb som ska köras på en IoT Edge-enhet måste det lagras på ett sätt som kan anropas från enheten. Du kan använda ett befintligt Azure Storage-konto eller skapa ett nytt nu. 

1. In the Azure portal, go to **Create a resource** > **Storage** > **Storage account**. 

1. Skapa lagringskontot genom att ange följande värden:

   | Fält | Värde |
   | ----- | ----- |
   | Prenumeration | Välj samma prenumeration som din IoT-hubb. |
   | Resursgrupp | Vi rekommenderar att du använder samma resursgrupp för alla testresurser som du skapar i snabbstarterna och självstudierna om IoT Edge. Till exempel **IoTEdgeResources**. |
   | Namn | Ange ett unikt namn för lagringskontot. | 
   | Plats | Välj en plats i närheten av dig. |


1. Keep the default values for the other fields and select **Review + Create**.

1. Review your settings then select **Create**.

### <a name="create-a-new-job"></a>Skapa ett nytt jobb

1. In the Azure portal, go to **Create a resource** > **Internet of Things** > **Stream Analytics job**.

1. Skapa jobbet genom att ange följande värden:

   | Fält | Värde |
   | ----- | ----- |
   | Jobbnamn | Ange ett namn för ditt jobb. Till exempel **IoTEdgeJob** | 
   | Prenumeration | Välj samma prenumeration som din IoT-hubb. |
   | Resursgrupp | Vi rekommenderar att du använder samma resursgrupp för alla testresurser som du skapar i snabbstarterna och självstudierna om IoT Edge. Till exempel **IoTEdgeResources**. |
   | Plats | Välj en plats i närheten av dig. | 
   | Värdmiljö | Välj **Gräns**. |
 
1. Välj **Skapa**.

### <a name="configure-your-job"></a>Konfigurera jobbet

När Stream Analytics-jobbet har skapats på Azure Portal kan du konfigurera det med indata, utdata och en fråga som ska köras mot de data som skickas. 

I det här avsnittet används de tre elementen för indata, utdata och fråga för att skapa ett jobb som tar emot temperaturdata från IoT Edge-enheten. Dessa data analyseras i ett rullande 30-sekundersintervall. Om medeltemperaturen i intervallet överstiger 70 grader, skickas en avisering till IoT Edge-enheten. Du anger exakt var dessa data kommer från och vart de skickas i nästa avsnitt när du distribuerar jobbet.  

1. Gå till Stream Analytics-jobbet på Azure Portal. 

1. Välj **Indata** under **Jobbtopologi** och välj sedan **Lägg till strömindata**.

   ![Azure Stream Analytics - add input](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. Välj **Edge Hub** i listrutan.

1. Ange **temperatur** som inmatat alias i fönstret **Nya indata**. 

1. Behåll standardvärdena för de andra fälten och välj **Spara**.

1. Öppna **Utdata** under **Jobbtopologi** och välj sedan **Lägg till**.

   ![Azure Stream Analytics - add output](./media/tutorial-deploy-stream-analytics/asa-output.png)

1. Välj **Edge Hub** i listrutan.

1. I rutan **Nya utdata** anger du **avisering** som utdataalias. 

1. Behåll standardvärdena för de andra fälten och välj **Spara**.

1. Välj **Fråga** under **Jobbtopologi**.

1. Ersätt standardtexten med följande fråga. SQL-koden skickar ett återställningskommando till aviseringens utdata om medeltemperaturen under ett 30-sekundersintervall når 70 grader. Återställningskommandot har förprogrammerats i sensorn som en åtgärd som kan utföras. 

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

### <a name="configure-iot-edge-settings"></a>Konfigurera IoT Edge-inställningar

För att förbereda Stream Analytics-jobbet för distribution till en IoT Edge-enhet måste du associera jobbet med en container i ett lagringskonto. När du sedan distribuerar jobbet exporteras jobbdefinitionen till lagringscontainern. 

1. Under **Configure**, select **Storage account settings** then select **Add storage account**. 

   ![Azure Stream Analytics - add storage account](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. Select the **Storage account** that you created at the beginning of this tutorial from the drop-down menu.

1. För fältet **Container** väljer du **Skapa ny** och anger ett namn för lagringscontainern. 

1. Välj **Spara**. 

## <a name="deploy-the-job"></a>Distribuera jobbet

Du kan nu distribuera Azure Stream Analytics-jobbet till din IoT Edge-enhet. 

I det här avsnittet använder du guiden **Ange moduler** på Azure Portal för att skapa ett *distributionsmanifest*. Ett distributionsmanifest är en JSON-fil som beskriver alla moduler som ska distribueras till en enhet, containerregistren som lagrar modulavbildningarna, hur modulerna ska hanteras och hur modulerna kan kommunicera med varandra. Din IoT Edge-enhet hämtar distributionsmanifestet från IoT Hub och använder sedan informationen i det för att distribuera och konfigurera alla dess tilldelade moduler. 

I den här självstudien ska du distribuera två moduler. The first is **SimulatedTemperatureSensor**, which is a module that simulates a temperature and humidity sensor. Den andra modulen är ditt Stream Analytics-jobb. Sensormodulen tillhandahåller dataströmmen som din jobbfråga analyserar. 

1. Gå till din IoT-hubb på Azure Portal.

1. Go to **IoT Edge**, and then open the details page for your IoT Edge device.

1. Välj **Ange moduler**.  

1. If you previously deployed the SimulatedTemperatureSensor module on this device, it might autopopulate. Om inte så lägger du till modulen genom att följa dessa steg:

   1. Klicka på **Lägg till** och välj **IoT Edge-modul**.
   1. For the name, type **SimulatedTemperatureSensor**.
   1. Ange **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0** som URI för avbildning. 
   1. Lämna de andra inställningarna som de är och välj **Spara**.

1. Lägg till ditt Azure Stream Analytics Edge-jobb med följande steg:

   1. Klicka på **Lägg till** och välj **Azure Stream Analytics-modul**.
   1. Välj din prenumeration och Azure Stream Analytics Edge-jobbet som du skapade. 
   1. Välj **Spara**.

   Once you save your changes, the details of your Stream Analytics job are published to the storage container that you created. 

1. When the Stream Analytics module is added to the list of modules, select **Configure** to see how it's structured. 

   URI:n för avbildningen pekar på en Azure Stream Analytics-standardavbildning. This one image is used for every Stream Analytics module that gets deployed to an IoT Edge device. 

   Modultvillingen konfigureras med egenskapen **ASAJobInfo**. Värdet för den egenskapen pekar på jobbdefinitionen i lagringscontainern. This property is how the Stream Analytics image is configured with your specific job details. 

   By default, the Stream Analytics module takes the same name as the job it's based on. You can change the module name on this page if you like, but it's not necessary. 

1. Close the module configuration page.

1. Anteckna namnet på Stream Analytics-modulen eftersom du behöver det i nästa steg och välj sedan **Nästa** för att fortsätta.

1. Ersätt standardvärdet i **Vägar** med följande kod. Uppdatera alla tre instanser av _{moduleName}_ med namnet på Azure Stream Analytics-modulen. 

    ```json
    {
        "routes": {
            "telemetryToCloud": "FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream",
            "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream",
            "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/SimulatedTemperatureSensor/inputs/control\")",
            "telemetryToAsa": "FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")"
        }
    }
    ```

   De vägar som du deklarerar här definierar flödet av data genom IoT Edge-enheten. The telemetry data from SimulatedTemperatureSensor are sent to IoT Hub and to the **temperature** input that was configured in the Stream Analytics job. The **alert** output messages are sent to IoT Hub and to the SimulatedTemperatureSensor module to trigger the reset command. 

1. Välj **Nästa**.

1. In the **Review Deployment** step, you can see how the information you provided in the wizard is converted into a JSON deployment manifest. When you're done reviewing the manifest, select **Submit**.

1. Återgå till informationssidan om enheten och välj sedan **Uppdatera**.  

    You should see the new Stream Analytics module running, along with the IoT Edge agent and IoT Edge hub modules. It may take a few minutes for the information to reach your IoT Edge device, and then for the new modules to start. If you don't see the modules running right away, continue refreshing the page.

    ![SimulatedTemperatureSensor and ASA module reported by device](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>Visa data

Now you can go to your IoT Edge device to check out the interaction between the Azure Stream Analytics module and the SimulatedTemperatureSensor module.

1. Kontrollera att alla moduler körs i Docker:

   ```cmd/sh
   iotedge list  
   ```
   <!--
   ![Docker output](./media/tutorial-deploy-stream-analytics/docker_output.png)
   -->
1. Visa alla systemloggar och statistikdata. Använd Stream Analytics-modulnamnet:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. View the reset command affect the SimulatedTemperatureSensor by viewing the sensor logs:

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   You can watch the machine's temperature gradually rise until it reaches 70 degrees for 30 seconds. Då utlöser Stream Analytics-modulen en återställning och datortemperaturen går tillbaka till 21. 

   ![Återställa kommandoutdata till modulloggar](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Rensa resurser 

Om du tänker fortsätta till nästa rekommenderade artikel kan du behålla de resurser och konfigurationer du har skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet. 

Otherwise, you can delete the local configurations and the Azure resources that you used in this article to avoid charges. 
 
[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade du ett Azure Streaming Analytics-jobb för att analysera data från din IoT Edge-enhet. Sedan läste du in den här Azure Stream Analytics-modulen på din IoT Edge-enhet för att bearbeta och agera på ökningar i temperatur lokalt samt skicka den aggregerade dataströmmen till molnet. Om du vill se hur Azure IoT Edge kan skapa flera lösningar för ditt företag fortsätter du med de andra självstudiekurserna.

> [!div class="nextstepaction"] 
> [Distribuera en Azure Machine Learning-modell som en modul](tutorial-deploy-machine-learning.md)
