---
title: Självstudiekurs - Stream Analytics på gränsen med Azure IoT Edge
description: I den här självstudien distribuerar du Azure Stream Analytics som en modul till en IoT Edge-enhet
author: kgremban
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d8c3bde0f32c1df6c98f6a71f6ab830c21256903
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76906287"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Självstudiekurs: Distribuera Azure Stream Analytics som en IoT Edge-modul

Många IoT-lösningar använder analystjänster för att få insikt om data när de anländer till molnet från IoT-enheter. Med Azure IoT Edge kan du använda logik från [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) och flytta den till själva enheten. Du kan minska hur mycket data som överförs och minska tiden det tar att reagera på insikter genom att bearbeta telemetriströmmar på gränsen.

Azure IoT Edge och Azure Stream Analytics är integrerade för att förenkla din arbetsbelastningsutveckling. Du kan skapa ett Azure Stream Analytics-jobb i Azure-portalen och sedan distribuera det som en IoT Edge-modul utan ytterligare kod.  

Azure Stream Analytics tillhandahåller en strukturerad frågesyntax för dataanalys, både i molnet och på IoT Edge-enheter. Mer information finns i [Azure Stream Analytics-dokumentationen](../stream-analytics/stream-analytics-edge.md).

Stream Analytics-modulen i den här självstudien beräknar medeltemperaturen över ett rullande 30-sekundersschema. När medeltemperaturen når 70 grader skickar modulen en varning så att enheten kan vidta en åtgärd. I det här fallet är åtgärden att återställa den simulerade temperatursensorn. Du kan till exempel använda den här funktionen för att stänga av en dator eller vidta förebyggande åtgärder när temperaturen når farliga nivåer i en produktionsmiljö.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
>
> * Skapa ett Azure Stream Analytics-jobb för bearbetning av data på gränsen.
> * Anslut det nya Azure Stream Analytics-jobbet med andra IoT Edge-moduler.
> * Distribuera Azure Stream Analytics-jobbet till en IoT Edge-enhet från Azure Portal.

<center>

![Diagram - Självstudiearkitektur: arrangera och distribuera ASA-jobb](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

En Azure IoT Edge-enhet:

* Du kan använda en virtuell Azure-dator som en IoT Edge-enhet genom att följa stegen i snabbstarten för [Linux-](quickstart-linux.md) eller [Windows-enheter](quickstart.md).

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.

## <a name="create-an-azure-stream-analytics-job"></a>Skapa ett Azure Stream Analytics-jobb

I det här avsnittet skapar du ett Azure Stream Analytics-jobb som gör följande:

* Ta emot data från din IoT Edge-enhet.
* Fråga telemetridata för värden utanför ett börsintervall.
* Vidta åtgärder på IoT Edge-enheten baserat på frågeresultaten.

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

När du skapar ett Azure Stream Analytics-jobb som ska köras på en IoT Edge-enhet måste det lagras på ett sätt som kan anropas från enheten. Du kan använda ett befintligt Azure Storage-konto eller skapa ett nytt nu.

1. Gå till **Skapa ett** > **resurslagringslagringskonto** > **Storage account**i Azure-portalen .

1. Skapa lagringskontot genom att ange följande värden:

   | Field | Värde |
   | ----- | ----- |
   | Prenumeration | Välj samma prenumeration som din IoT-hubb. |
   | Resursgrupp | Vi rekommenderar att du använder samma resursgrupp för alla dina testresurser för snabbstarter och självstudier i IoT Edge. Till exempel **IoTEdgeResources**. |
   | Namn | Ange ett unikt namn för lagringskontot. |
   | Location | Välj en plats i närheten av dig. |

1. Behåll standardvärdena för de andra fälten och välj **Granska + Skapa**.

1. Granska dina inställningar och välj sedan **Skapa**.

### <a name="create-a-new-job"></a>Skapa ett nytt jobb

1. Gå till Skapa ett**resurs-Internet** >  **Create a resource** > of Things**Stream Analytics-jobb**i Azure-portalen .

1. Skapa jobbet genom att ange följande värden:

   | Field | Värde |
   | ----- | ----- |
   | Jobbnamn | Ange ett namn för ditt jobb. Till exempel **IoTEdgeJob** |
   | Prenumeration | Välj samma prenumeration som din IoT-hubb. |
   | Resursgrupp | Vi rekommenderar att du använder samma resursgrupp för alla testresurser som du skapar i snabbstarterna och självstudierna om IoT Edge. Till exempel **IoTEdgeResources**. |
   | Location | Välj en plats i närheten av dig. |
   | Värdmiljö | Välj **Gräns**. |

1. Välj **Skapa**.

### <a name="configure-your-job"></a>Konfigurera jobbet

När Stream Analytics-jobbet har skapats på Azure Portal kan du konfigurera det med indata, utdata och en fråga som ska köras mot de data som skickas.

I det här avsnittet används de tre elementen för indata, utdata och fråga för att skapa ett jobb som tar emot temperaturdata från IoT Edge-enheten. Dessa data analyseras i ett rullande 30-sekundersintervall. Om medeltemperaturen i intervallet överstiger 70 grader, skickas en avisering till IoT Edge-enheten. Du anger exakt var dessa data kommer från och vart de skickas i nästa avsnitt när du distribuerar jobbet.  

1. Gå till Stream Analytics-jobbet på Azure Portal.

1. Välj **Indata** under **Jobbtopologi** och välj sedan **Lägg till strömindata**.

   ![Azure Stream Analytics - lägg till indata](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. Välj **Edge Hub** i listrutan.

1. Ange **temperatur** som inmatat alias i fönstret **Nya indata**.

1. Behåll standardvärdena för de andra fälten och välj **Spara**.

1. Öppna **Utdata** under **Jobbtopologi** och välj sedan **Lägg till**.

   ![Azure Stream Analytics – lägg till utdata](./media/tutorial-deploy-stream-analytics/asa-output.png)

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

1. Under **Konfigurera**väljer du **Lagringskontoinställningar** och väljer sedan **Lägg till lagringskonto**.

   ![Azure Stream Analytics – lägg till lagringskonto](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. Välj det **lagringskonto** som du skapade i början av den här självstudien på den nedrullningsbara menyn.

1. För fältet **Container** väljer du **Skapa ny** och anger ett namn för lagringscontainern.

1. Välj **Spara**.

## <a name="deploy-the-job"></a>Distribuera jobbet

Du kan nu distribuera Azure Stream Analytics-jobbet till din IoT Edge-enhet.

I det här avsnittet använder du guiden **Ange moduler** på Azure Portal för att skapa ett *distributionsmanifest*. Ett distributionsmanifest är en JSON-fil som beskriver alla moduler som ska distribueras till en enhet, containerregistren som lagrar modulavbildningarna, hur modulerna ska hanteras och hur modulerna kan kommunicera med varandra. Din IoT Edge-enhet hämtar distributionsmanifestet från IoT Hub och använder sedan informationen i det för att distribuera och konfigurera alla dess tilldelade moduler.

I den här självstudien ska du distribuera två moduler. Den första är **SimulatedTemperatureSensor**, som är en modul som simulerar en temperatur- och fuktighetssensor. Den andra modulen är ditt Stream Analytics-jobb. Sensormodulen tillhandahåller dataströmmen som din jobbfråga analyserar.

1. Gå till din IoT-hubb på Azure Portal.

1. Gå till **IoT Edge**och öppna sedan informationssidan för din IoT Edge-enhet.

1. Välj **Ange moduler**.  

1. Om du tidigare har distribuerat modulen SimuleradTemperatureSensor på den här enheten kan den fyllas i automatiskt. Om inte så lägger du till modulen genom att följa dessa steg:

   1. Klicka på **Lägg till** och välj **IoT Edge-modul**.
   1. För namnet skriver du **SimulatedTemperatureSensor**.
   1. Ange **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0** som URI för avbildning.
   1. Lämna de andra inställningarna som de är och välj **Spara**.

1. Lägg till ditt Azure Stream Analytics Edge-jobb med följande steg:

   1. Klicka på **Lägg till** och välj **Azure Stream Analytics-modul**.
   1. Välj din prenumeration och Azure Stream Analytics Edge-jobbet som du skapade.
   1. Välj **Spara**.

   När du har sparat ändringarna publiceras informationen om ditt Stream Analytics-jobb i lagringsbehållaren som du har skapat.

1. När Stream Analytics-modulen läggs till i listan över moduler väljer du dess namn för att se hur den är strukturerad och uppdaterar dess inställningar på sidan **Uppdatera IoT Edge Module.**

   Fliken **Modulinställningar** har **den avbildnings-URI** som pekar på en standardavbildning i Azure Stream Analytics. Den här avbildningen används för varje Stream Analytics-modul som distribueras till en IoT Edge-enhet.

   På fliken **Modultvillinginställningar** visas den JSON som definierar egenskapen Azure Stream Analytics (ASA) som heter **ASAJobInfo**. Värdet för den egenskapen pekar på jobbdefinitionen i lagringscontainern. Den här egenskapen är hur Stream Analytics-avbildningen är konfigurerad med din specifika jobbinformation.

   Som standard tar Stream Analytics-modulen samma namn som det jobb som den baseras på. Du kan ändra modulnamnet på den här sidan om du vill, men det är inte nödvändigt.

1. Välj **Avbryt** eller **Spara**.

1. Anteckna namnet på Stream Analytics-modulen eftersom du behöver den i nästa steg och välj sedan **Nästa: Rutter** för att fortsätta.

1. På fliken **Rutter definierar** du hur meddelanden skickas mellan moduler och IoT Hub. Meddelanden skapas med namn-/värdepar. Ersätt `route` standard- `upstream` och namn och värden med de par som visas i följande tabell, följande namn/värde-par, som ersätter förekomster av _{moduleName}_ med namnet på din Azure Stream Analytics-modul.

    | Namn | Värde |
    | --- | --- |
    | `telemetryToCloud` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream` |
    | `alertsToCloud` | `FROM /messages/modules/{moduleName}/* INTO $upstream` |
    | `alertsToReset` | `FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint("/modules/SimulatedTemperatureSensor/inputs/control")` |
    | `telemetryToAsa` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint("/modules/{moduleName}/inputs/temperature")`|

    De vägar som du deklarerar här definierar flödet av data genom IoT Edge-enheten. Telemetridata från SimulatedTemperatureSensor skickas till IoT Hub och till **den temperaturindata** som konfigurerades i Stream Analytics-jobbet. Varningsutdatameddelandena skickas till IoT Hub och till modulen SimuleradTemperatureSensor för att utlösa återställningskommandot. **alert**

1. Välj **Nästa: Granska + Skapa**.

1. På fliken **Granska + Skapa** kan du se hur informationen som du angav i guiden konverteras till ett JSON-distributionsmanifest. När du är klar med granskningen av manifestet väljer du **Skapa**.

1. Du är tillbaka till sidan med enhetsinformation. Välj **Uppdatera**.  

    Du bör se den nya Stream Analytics-modulen körs, tillsammans med IoT Edge-agenten och IoT Edge-hubbmodulerna. Det kan ta några minuter innan informationen når din IoT Edge-enhet och sedan för de nya modulerna att starta. Om du inte ser modulerna som körs direkt fortsätter du att uppdatera sidan.

    ![SimulatedTemperatureSensor och ASA-modul rapporteras av enheten](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>Visa data

Nu kan du gå till din IoT Edge-enhet för att kolla in interaktionen mellan Azure Stream Analytics-modulen och modulen SimuleradTemperatureSensor.

1. Kontrollera att alla moduler körs i Docker:

   ```cmd/sh
   iotedge list  
   ```

1. Visa alla systemloggar och statistikdata. Använd Stream Analytics-modulnamnet:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. Visa återställningskommandot påverkar SimulatedTemperatureSensor genom att visa sensorloggarna:

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   Du kan titta på maskinens temperatur gradvis stiga tills den når 70 grader i 30 sekunder. Då utlöser Stream Analytics-modulen en återställning och datortemperaturen går tillbaka till 21.

   ![Återställa kommandoutdata till modulloggar](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och De Azure-resurser som du använde i den här artikeln för att undvika avgifter.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade du ett Azure Streaming Analytics-jobb för att analysera data från din IoT Edge-enhet. Sedan läste du in den här Azure Stream Analytics-modulen på din IoT Edge-enhet för att bearbeta och agera på ökningar i temperatur lokalt samt skicka den aggregerade dataströmmen till molnet. Om du vill se hur Azure IoT Edge kan skapa flera lösningar för ditt företag fortsätter du med de andra självstudiekurserna.

> [!div class="nextstepaction"]
> [Distribuera en Azure Machine Learning-modell som en modul](tutorial-deploy-machine-learning.md)
