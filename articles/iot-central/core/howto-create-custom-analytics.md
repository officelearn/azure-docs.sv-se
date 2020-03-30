---
title: Utöka Azure IoT Central med anpassad analys | Microsoft-dokument
description: Som lösningsutvecklare konfigurerar du ett IoT Central-program för att göra anpassade analyser och visualiseringar. Den här lösningen använder Azure Databricks.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 7c2c14a937b4ef55d0e5f71e7b20214428ecd68c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158205"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Utöka Azure IoT Central med anpassad analys med Azure Databricks

Den här guiden visar hur du som lösningsutvecklare utökar ditt IoT Central-program med anpassade analyser och visualiseringar. I exemplet används en [Azure Databricks-arbetsyta](https://docs.microsoft.com/azure/azure-databricks/) för att analysera telemetriströmmen IoT Central och för att generera visualiseringar som [rutar ritplaner](https://wikipedia.org/wiki/Box_plot).

Den här guiden visar hur du utökar IoT Central utöver vad den redan kan göra med de [inbyggda analysverktygen](./howto-create-custom-analytics.md).

I den här guiden lär du dig hur du:

* Strömma telemetri från ett IoT Central-program med hjälp av *kontinuerlig dataexport*.
* Skapa en Azure Databricks-miljö för att analysera och rita enhetstelemetri.

## <a name="prerequisites"></a>Krav

För att kunna slutföra stegen i den här programguiden behöver du en aktiv Azure-prenumeration.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

### <a name="iot-central-application"></a>IoT Central ansökan

Skapa ett IoT Central-program på Azure [IoT Central application manager-webbplatsen](https://aka.ms/iotcentral) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Prisplan | Standard |
| Programmall | Analys i butik – tillståndsövervakning |
| Programnamn | Acceptera standardvärdet eller välj ditt eget namn |
| URL | Acceptera standard- eller välj ditt eget unika URL-prefix |
| Katalog | Din Azure Active Directory-klient |
| Azure-prenumeration | Din Azure-prenumeration |
| Region | Din närmaste region |

Exemplen och skärmbilderna i den här artikeln använder **usa-regionen.** Välj en plats nära dig och se till att du skapar alla dina resurser i samma region.

Den här programmallen innehåller två simulerade termostatenheter som skickar telemetri.

### <a name="resource-group"></a>Resursgrupp

Använd [Azure-portalen för att skapa en resursgrupp](https://portal.azure.com/#create/Microsoft.ResourceGroup) som heter **IoTCentralAnalysis** för att innehålla andra resurser som du skapar. Skapa dina Azure-resurser på samma plats som ditt IoT Central-program.

### <a name="event-hubs-namespace"></a>Event Hubs-namnområde

Använd [Azure-portalen för att skapa ett namnområde för eventhubbar](https://portal.azure.com/#create/Microsoft.EventHub) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn    | Välj namnområdesnamn |
| Prisnivå | Basic |
| Prenumeration | Din prenumeration |
| Resursgrupp | IoTCentralAnalysis |
| Location | USA, östra |
| Genomflödesenheter | 1 |

### <a name="azure-databricks-workspace"></a>Arbetsytan Azure Databricks

Använd [Azure-portalen för att skapa en Azure Databricks-tjänst](https://portal.azure.com/#create/Microsoft.Databricks) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn på arbetsyta    | Välj namn på arbetsytan |
| Prenumeration | Din prenumeration |
| Resursgrupp | IoTCentralAnalysis |
| Location | USA, östra |
| Prisnivå | Standard |

När du har skapat de resurser som krävs ser **resursgruppen IoTCentralAnalysis** ut som följande skärmbild:

![Resursgrupp för IoT Central analys](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Du kan konfigurera ett IoT Central-program för att kontinuerligt exportera telemetri till en händelsehubb. I det här avsnittet skapar du en händelsehubb för att ta emot telemetri från ditt IoT Central-program. Händelsehubben levererar telemetrin till ditt Stream Analytics-jobb för bearbetning.

1. I Azure-portalen navigerar du till namnområdet Event Hubs och väljer **+ Event Hub**.
1. Namnge ditt händelsenav **centralexport**och välj **Skapa**.
1. Välj **centralexport**i listan över händelsehubbar i namnområdet . Välj sedan **Principer för delad åtkomst**.
1. Välj **+ Lägg till**. Skapa en princip med namnet **Lyssna** med **lyssningsanspråket.**
1. När principen är klar markerar du den i listan och kopierar sedan värdet **För anslutningssträngprimärnyckel.**
1. Anteckna den här anslutningssträngen, du använder den senare när du konfigurerar din Databricks-anteckningsbok för att läsa från händelsehubben.

Namnområdet Event Hubs ser ut så här:

![Event Hubs-namnområde](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Konfigurera export i IoT Central

På azure [IoT Central application manager-webbplatsen](https://aka.ms/iotcentral) navigerar du till det IoT Central-program som du skapade från Contoso-mallen. I det här avsnittet konfigurerar du programmet för att strömma telemetrin från dess simulerade enheter till händelsehubben. Så här konfigurerar du exporten:

1. Navigera till sidan **Dataexport,** välj **+ Nytt**och sedan **Azure Event Hubs**.
1. Använd följande inställningar för att konfigurera exporten och välj sedan **Spara:**

    | Inställning | Värde |
    | ------- | ----- |
    | Visningsnamn | Exportera till händelsehubbar |
    | Enabled | På |
    | Event Hubs-namnområde | Namnområdesnamn för händelsehubbar |
    | Händelsehubb | centralexport |
    | Mått | På |
    | Enheter | Av |
    | Enhetsmallar | Av |

![Konfiguration för dataexport](media/howto-create-custom-analytics/cde-configuration.png)

Vänta tills exportstatusen **körs** innan du fortsätter.

## <a name="configure-databricks-workspace"></a>Konfigurera Databricks arbetsyta

I Azure-portalen navigerar du till din Azure Databricks-tjänst och väljer **Starta arbetsyta**. En ny flik öppnas i webbläsaren och loggar in dig på arbetsytan.

### <a name="create-a-cluster"></a>Skapa ett kluster

Välj **Nytt kluster**under listan över vanliga uppgifter på sidan **Azure Databricks** .

Använd informationen i följande tabell för att skapa ditt kluster:

| Inställning | Värde |
| ------- | ----- |
| Klusternamn | centralanalys |
| Klusterläge | Standard |
| Databricks Runtime Version | 5.5 LTS (Scala 2.11, Spark 2.4.3) |
| Python-version | 3 |
| Aktivera automatisk skalning | Inga |
| Avsluta efter minuter av inaktivitet | 30 |
| Arbetstyp | Standard_DS3_v2 |
| Arbetstagare | 1 |
| Typ av drivrutin | Samma som arbetare |

Det kan ta flera minuter att skapa ett kluster och vänta tills klustergenereringen har slutförts innan du fortsätter.

### <a name="install-libraries"></a>Installera bibliotek

Vänta **tills** klustertillståndet **körs**på sidan Kluster .

Följande steg visar hur du importerar det bibliotek som du behöver i klustret:

1. På sidan Kluster väntar du tills tillståndet för det interaktiva **klustret** för **centralanalys** **körs**.

1. Markera klustret och välj sedan fliken **Bibliotek.**

1. Välj **Installera nytt**på fliken **Bibliotek** .

1. På sidan **Installera bibliotek** väljer du **Maven** som bibliotekskälla.

1. Ange följande värde i textrutan **Koordinater:**`com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Välj **Installera för** att installera biblioteket i klustret.

1. Bibliotekets status är nu **installerad:**

    ![Biblioteket installerat](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Importera en databricks-anteckningsbok

Använd följande steg för att importera en Databricks-anteckningsbok som innehåller Python-koden för att analysera och visualisera din IoT Central-telemetri:

1. Navigera till **arbetsytan** i din Databricks-miljö. Markera listrutan bredvid ditt kontonamn och välj sedan **Importera**.

1. Välj att importera från en URL och ange följande adress:[https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Om du vill importera anteckningsboken väljer du **Importera**.

1. Välj **arbetsytan** om du vill visa den importerade anteckningsboken:

    ![Importerad anteckningsbok](media/howto-create-custom-analytics/import-notebook.png)

1. Redigera koden i den första Python-cellen för att lägga till anslutningssträngen Event Hubs som du sparade tidigare:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Kör analys

Om du vill köra analysen måste du koppla anteckningsboken till klustret:

1. Välj **Fristående** och välj sedan det centrala analysklustret. **centralanalysis**
1. Om klustret inte körs startar du det.
1. Om du vill starta anteckningsboken väljer du körknappen.

Du kan se ett fel i den sista cellen. Om så är fallet, kontrollera de tidigare cellerna körs, vänta en minut för vissa data som ska skrivas till lagring och sedan köra den sista cellen igen.

### <a name="view-smoothed-data"></a>Visa utjämnade data

Rulla nedåt till cell 14 i anteckningsboken för att se en ritom med rullande genomsnittlig fuktighet efter enhetstyp. Den här ritytan uppdateras kontinuerligt när strömmande telemetri anländer:

![Utjämnad telemetritomt](media/howto-create-custom-analytics/telemetry-plot.png)

Du kan ändra storlek på diagrammet i anteckningsboken.

### <a name="view-box-plots"></a>Visa ruta ritplaner

Rulla ned till cell 20 i anteckningsboken för att se [rutdiagram.](https://en.wikipedia.org/wiki/Box_plot) Rutan ritdiagram är baserade på statiska data så att uppdatera dem måste du köra cellen igen:

![Box tomter](media/howto-create-custom-analytics/box-plots.png)

Du kan ändra storlek på diagramerna i anteckningsboken.

## <a name="tidy-up"></a>Städa upp

Om du vill städa upp efter den här how-to-to-kostnaden tar du bort **resursgruppen IoTCentralAnalysis** i Azure-portalen.

Du kan ta bort IoT Central-programmet från **sidan Hantering** i programmet.

## <a name="next-steps"></a>Nästa steg

I den här guiden lärde du dig att:

* Strömma telemetri från ett IoT Central-program med hjälp av *kontinuerlig dataexport*.
* Skapa en Azure Databricks-miljö för att analysera och rita telemetridata.

Nu när du vet hur du skapar anpassade analyser är det föreslagna nästa steget att lära dig hur du [hanterar ditt program](howto-administer.md).
