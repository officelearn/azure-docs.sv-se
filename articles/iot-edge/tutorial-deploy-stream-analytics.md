---
title: Självstudiekurs – Distribuera ASA-jobb till Azure IoT Edge-enheter | Microsoft Docs
description: I den här självstudien distribuerar du Azure Stream Analytics som en modul till en Iot Edge-enhet
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: b85fe520bf29d10bb3dac1246349abbdadaf6df3
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158745"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module-preview"></a>Självstudie: Distribuera Azure Stream Analytics som en IoT Edge-modul (förhandsversion)

Många IoT-lösningar använder analystjänster för att få information om data när de tas emot i molnet från IoT-enheter. Med Azure IoT Edge kan du använda logik från [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) och flytta den till själva enheten. Du kan minska hur mycket data som överförs och minska tiden det tar att reagera på insikter genom att bearbeta telemetriströmmar på gränsen.

Azure IoT Edge och Azure Stream Analytics är integrerade så att du kan skapa ett Azure Stream Analytics-jobb i Azure Portal och sedan distribuera det som en IoT Edge-modul utan ytterligare kod.  

Azure Stream Analytics tillhandahåller ett omfattande strukturerat frågesyntax för dataanalys både i molnet och på IoT Edge-enheter. Se [Dokumentation om Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md) för mer information om Azure Stream Analytics IoT Edge.

Stream Analytics-modulen i den här självstudien beräknar medeltemperaturen över ett rullande 30-sekundersschema. När medeltemperaturen når 70 grader skickar modulen en varning så att enheten kan vidta en åtgärd. I det här fallet är åtgärden att återställa den simulerade temperatursensorn. Du kan till exempel använda den här funktionen för att stänga av en dator eller vidta förebyggande åtgärder när temperaturen når farliga nivåer i en produktionsmiljö. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett Azure Stream Analytics-jobb för bearbetning av data på gränsen.
> * Anslut det nya Azure Stream Analytics-jobbet med andra IoT Edge-moduler.
> * Distribuera Azure Stream Analytics-jobbet till en IoT Edge-enhet från Azure Portal.

<center>
![Arkitekturdiagram för självstudien](./media/tutorial-deploy-stream-analytics/ASATutorialDiagram.png)
</center>

>[!NOTE]
>Azure Stream Analytics-moduler för IoT Edge finns i en [allmänt tillgänglig förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

En Azure IoT Edge-enhet:

* Du kan använda utvecklingsdatorn eller en virtuell dator som en gränsenhet genom att följa stegen i snabbstarten för [Linux-](quickstart-linux.md) eller [Windows-enheter](quickstart.md).

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure. 


## <a name="create-an-azure-stream-analytics-job"></a>Skapa ett Azure Stream Analytics-jobb

I det här avsnittet skapar du ett Azure Stream Analytics-jobb för att hämta data från IoT Hub, fråga de telemetridata som skickats från enheten och sedan skicka vidare resultaten till en Azure Blob Storage-container. 

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

När du skapar ett Azure Stream Analytics-jobb som ska köras på en IoT Edge-enhet måste det lagras på ett sätt som kan anropas från enheten. Du kan använda ett befintligt Azure Storage-konto eller skapa ett nytt nu. 

1. På Azure Portal går du till **Skapa en resurs** > **Lagring** > **Lagringskonto – blob, fil, tabell, kö**. 

1. Skapa lagringskontot genom att ange följande värden:

   | Fält | Värde |
   | ----- | ----- |
   | Namn | Ange ett unikt namn för lagringskontot. | 
   | Plats | Välj en plats i närheten av dig. |
   | Prenumeration | Välj samma prenumeration som din IoT-hubb. |
   | Resursgrupp | Vi rekommenderar att du använder samma resursgrupp för alla testresurser som du skapar i snabbstarterna och självstudierna om IoT Edge. Till exempel **IoTEdgeResources**. |

1. Behåll standardvärdena för de andra fälten och välj **Skapa**. 

### <a name="create-a-new-job"></a>Skapa ett nytt jobb

1. På Azure Portal går du till **Skapa en resurs** > **Sakernas Internet** > **Stream Analytics-jobb**.

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

   ![Azure Stream Analytics-indata](./media/tutorial-deploy-stream-analytics/asa_input.png)

1. Välj **Edge Hub** i listrutan.

1. Ange **temperatur** som inmatat alias i fönstret **Nya indata**. 

1. Behåll standardvärdena för de andra fälten och välj **Spara**.

1. Öppna **Utdata** under **Jobbtopologi** och välj sedan **Lägg till**.

   ![Azure Stream Analytics-utdata](./media/tutorial-deploy-stream-analytics/asa_output.png)

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

1. Under **Konfigurera** väljer du **Inställningar för lagringskonto**.

1. Välj **Lägg till lagringskonto**. 

1. Välj ditt **Lagringskonto** från den nedrullningsbara menyn.

1. För fältet **Container** väljer du **Skapa ny** och anger ett namn för lagringscontainern. 

1. Välj **Spara**. 

## <a name="deploy-the-job"></a>Distribuera jobbet

Du kan nu distribuera Azure Stream Analytics-jobbet till din IoT Edge-enhet. 

I det här avsnittet använder du guiden **Ange moduler** på Azure Portal för att skapa ett *distributionsmanifest*. Ett distributionsmanifest är en JSON-fil som beskriver alla moduler som ska distribueras till en enhet, containerregistren som lagrar modulavbildningarna, hur modulerna ska hanteras och hur modulerna kan kommunicera med varandra. Din IoT Edge-enhet hämtar distributionsmanifestet från IoT Hub och använder sedan informationen i det för att distribuera och konfigurera alla dess tilldelade moduler. 

I den här självstudien ska du distribuera två moduler. Den första modulen är **tempSensor**. Den här modulen simulerar en temperatur- och fuktighetssensor. Den andra modulen är ditt Stream Analytics-jobb. Sensormodulen tillhandahåller dataströmmen som din jobbfråga analyserar. 

1. I din IoT Hub i Azure Portal går du till **IoT Edge** och sedan öppnar du informationssidan för IoT Edge-enheter.

1. Välj **Ange moduler**.  

1. Om du tidigare har distribuerat tempSensor-modulen på den här enheten kanske den fylls i automatiskt. Om inte så lägger du till modulen genom att följa dessa steg:

   1. Klicka på **Lägg till** och välj **IoT Edge-modul**.
   1. Ange **tempSensor** som namn.
   1. Ange **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0** som URI för avbildning. 
   1. Lämna de andra inställningarna som de är och välj **Spara**.

1. Lägg till ditt Azure Stream Analytics Edge-jobb med följande steg:

   1. Klicka på **Lägg till** och välj **Azure Stream Analytics-modul**.
   1. Välj din prenumeration och Azure Stream Analytics Edge-jobbet som du skapade. 
   1. Välj **Spara**.

1. När ditt Stream Analytics-jobb har publicerats till lagringscontainern som du skapade klickar du på modulnamnet för att se hur en Stream Analytics-modul är strukturerad. 

   URI:n för avbildningen pekar på en Azure Stream Analytics-standardavbildning. Det här är samma avbildning som används för alla jobb som distribueras till en IoT Edge-enhet. 

   Modultvillingen konfigureras med egenskapen **ASAJobInfo**. Värdet för den egenskapen pekar på jobbdefinitionen i lagringscontainern. Den här egenskapen anger hur Stream Analytics-avbildningen konfigureras med din specifika jobbinformation. 

1. Stäng modulsidan.

1. Anteckna namnet på Stream Analytics-modulen eftersom du behöver det i nästa steg och välj sedan **Nästa** för att fortsätta.

1. Ersätt standardvärdet i **Vägar** med följande kod. Uppdatera alla tre instanser av _{moduleName}_ med namnet på Azure Stream Analytics-modulen. 

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

   De vägar som du deklarerar här definierar flödet av data genom IoT Edge-enheten. Telemetridata från tempSensor skickas till IoT Hub och till de **temperaturindata** som konfigurerats i Stream Analytics-jobbet. **Aviseringens utdatameddelanden** skickas till IoT Hub och till tempSensor-modulen för att utlösa återställningskommandot. 

1. Välj **Nästa**.

1. Välj **Skicka** i steget **Granska distribution**.

1. Återgå till informationssidan om enheten och välj sedan **Uppdatera**.  

    Du borde se att den nya Stream Analytics-modulen körs tillsammans med IoT Edge-agentmodulen och IoT Edge Hub.

    ![Utdata för modulen](./media/tutorial-deploy-stream-analytics/module_output2.png)

## <a name="view-data"></a>Visa data

Nu kan du gå till din IoT Edge-enhet för att se interaktionen mellan Azure Stream Analytics-modulen och tempSensor-modulen.

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

Du bör kunna se datortemperaturen öka gradvis till den når 70 grader i 30 sekunder. Då utlöser Stream Analytics-modulen en återställning och datortemperaturen går tillbaka till 21. 

   ![Dockerlogg](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Rensa resurser 

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet. 

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader. 
 
[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade du ett Azure Streaming Analytics-jobb för att analysera data från din IoT Edge-enhet. Sedan läste du in den här Azure Stream Analytics-modulen på din IoT Edge-enhet för att bearbeta och agera på ökningar i temperatur lokalt samt skicka den aggregerade dataströmmen till molnet. Om du vill se hur Azure IoT Edge kan skapa flera lösningar för ditt företag fortsätter du med de andra självstudiekurserna.

> [!div class="nextstepaction"] 
> [Distribuera en Azure Machine Learning-modell som en modul](tutorial-deploy-machine-learning.md)
