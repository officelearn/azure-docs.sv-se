---
title: Utöka Azure IoT Central med anpassade analytics | Microsoft Docs
description: Konfigurera ett IoT Central-program för att göra anpassade för analys och visualisering som lösningsutvecklare. Den här lösningen använder Azure Databricks.
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e039e2b8d9c183b5bfee1bee47e4addc4c873bf7
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743447"
---
# <a name="extend-azure-iot-central-with-custom-analytics"></a>Utöka Azure IoT Central med anpassade analytics

Den här guiden visar dig, som en för lösningsutvecklare, hur du utökar din IoT Central-App med anpassade för analys och visualisering. I exemplet används en [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) arbetsytan att analysera telemetriströmmen IoT Central och för att skapa visualiseringar som [rutan områden](https://wikipedia.org/wiki/Box_plot).

Den här guiden visar hur du utökar IoT Central utöver vad den redan gör med den [inbyggda analysverktyg](howto-create-analytics.md).

I den här guiden lär du dig hur du:

* Stream telemetri från en IoT Central-program med *löpande dataexport*.
* Skapa en Azure Databricks-miljö för att analysera och rita enhetstelemetri.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra stegen i den här guiden behöver du en aktiv Azure-prenumeration.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

### <a name="iot-central-application"></a>IoT Central-program

Skapa en IoT Central-App från den [Azure IoT Central - Mina program](https://aka.ms/iotcentral) sidan med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Betalningsplan | Betala per användning |
| Programmall | Exemplet Contoso |
| Programnamn | Acceptera standardinställningarna eller välj namnet på din egen |
| URL | Acceptera standardinställningarna eller välj en egen unik URL-prefix |
| Katalog | Azure Active Directory-klient |
| Azure-prenumeration | Din Azure-prenumeration |
| Region | Östra USA |

Exempel och skärmdumpar i den här artikeln i **USA, östra** region. Välj en plats nära dig och se till att skapa alla resurser i samma region.

### <a name="resource-group"></a>Resursgrupp

Använd den [Azure portal för att skapa en resursgrupp](https://portal.azure.com/#create/Microsoft.ResourceGroup) kallas **IoTCentralAnalysis** som innehåller de resurser som du skapar. Skapa dina Azure-resurser på samma plats som programmet IoT Central.

### <a name="event-hubs-namespace"></a>Event Hubs-namnområde

Använd den [Azure portal för att skapa ett namnområde för Event Hubs](https://portal.azure.com/#create/Microsoft.EventHub) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn    | Välj namnet på ditt namnområde |
| Prisnivå | Basic |
| Prenumeration | Din prenumeration |
| Resursgrupp | IoTCentralAnalysis |
| Location | Östra USA |
| Genomflödesenheter | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks-arbetsyta

Använd den [Azure portal för att skapa en Azure Databricks-tjänst](https://portal.azure.com/#create/Microsoft.Databricks) med följande inställningar:

| Inställning | Värde |
| ------- | ----- |
| Namn på arbetsyta    | Välj namnet på din arbetsyta |
| Prenumeration | Din prenumeration |
| Resursgrupp | IoTCentralAnalysis |
| Location | Östra USA |
| Prisnivå | Standard |

När du har skapat resurserna som krävs, din **IoTCentralAnalysis** resursgrupp ser ut som följande skärmbild:

![Resursgrupp för IoT Central-analys](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Du kan konfigurera ett IoT Central-program för att exportera telemetri kontinuerligt till en händelsehubb. I det här avsnittet skapar du en händelsehubb för att ta emot telemetri från IoT Central-program. Event hub levererar telemetri till ditt Stream Analytics-jobb för bearbetning.

1. Navigera till Event Hubs-namnområdet i Azure-portalen och välj **+ Event Hub**.
1. Namnge din event hub **centralexport**, och välj **skapa**.
1. Välj i listan över event hubs i namnområdet, **centralexport**. Välj sedan **principer för delad åtkomst**.
1. Välj **+ Lägg till**. Skapa en princip med namnet **lyssna** med den **lyssna** anspråk.
1. När principen är klar markerar du det i listan och sedan kopiera den **anslutningssträng – primär nyckel** värde.
1. Anteckna den här anslutningssträngen, du använda det senare när du konfigurerar din Databricks-anteckningsbok för att läsa från event hub.

Event Hubs-namnområdet ser ut som på följande skärmbild:

![Event Hubs-namnområde](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Konfigurera export i IoT Central

Navigera till den [IoT Central programmet](https://aka.ms/iotcentral) du skapade från Contoso-mall. I det här avsnittet ska konfigurera du programmet att strömma telemetri från dess simulerade enheter till din event hub. Konfigurera export:

1. Navigera till den **löpande Export av Data** väljer **+ ny**, och sedan **Azure Event Hubs**.
1. Använd följande inställningar för att konfigurera exporten och välj sedan **spara**:

    | Inställning | Värde |
    | ------- | ----- |
    | Visningsnamn | Exportera till Event Hubs |
    | Enabled | På |
    | Event Hubs-namnområde | Namnet på ditt Event Hubs-namnområde |
    | Händelsehubb | centralexport |
    | Mått | På |
    | Enheter | Av |
    | Enhetsmallar | Av |

![Konfiguration för löpande export](media/howto-create-custom-analytics/cde-configuration.png)

Vänta tills statusen export är **kör** innan du fortsätter.

## <a name="configure-databricks-workspace"></a>Konfigurera Databricks-arbetsyta

Gå till din Azure Databricks-tjänst i Azure-portalen och välj **Starta arbetsyta**. En ny flik öppnas i webbläsaren och loggar du in på din arbetsyta.

### <a name="create-a-cluster"></a>Skapa ett kluster

På den **Azure Databricks** sidan under listan över vanliga aktiviteter väljer **nytt kluster**.

Använd informationen i följande tabell för att skapa klustret:

| Inställning | Värde |
| ------- | ----- |
| Klusternamn | centralanalysis |
| Klusterläge | Standard |
| Databricks-körningsversion | 5.3 (Scala 2.11, Spark 2.4.0) |
| Python Version | 3 |
| Aktivera automatisk skalning | Nej |
| Avsluta efter antal minuters inaktivitet | 30 |
| Arbetartyp | Standard_DS3_v2 |
| Arbetare | 1 |
| Drivrutinstyp | Samma som arbetare |

Skapa ett kluster kan det ta flera minuter, vänta på att skapa ett kluster ska slutföras innan du fortsätter.

### <a name="install-libraries"></a>Installera bibliotek

På den **kluster** väntar du tills klusterstatusen är **kör**.

Följande steg visar hur du importerar ditt exempel måste biblioteket till klustret:

1. På den **kluster** väntar du tills tillståndet för den **centralanalysis** interaktiva klustret är **kör**.

1. Välj klustret och välj sedan den **bibliotek** fliken.

1. På den **bibliotek** fliken **installera nya**.

1. På den **installera biblioteket** väljer **Maven** som bibliotekskälla.

1. I den **samordnar** textrutan anger du följande värde: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Välj **installera** installera biblioteket i klustret.

1. Statusen för biblioteket är nu **installerad**:

    ![Biblioteket som är installerat](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Importera en Databricks notebook

Använd följande steg för att importera en Databricks notebook med Python-kod för att analysera och visualisera din IoT Central-telemetri:

1. Navigera till den **arbetsytan** sidan i Databricks-miljön. Välj i listrutan bredvid namnet på ditt konto och välj sedan **Import**.

1. Välj att importera från en URL och ange följande adress: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Om du vill importera anteckningsboken väljer **importera**.

1. Välj den **arbetsytan** att visa importerade anteckningsboken:

    ![Importerade notebook](media/howto-create-custom-analytics/import-notebook.png)

1. Redigera koden i den första Python-cellen för att lägga till Event Hubs-anslutningssträngen som du sparade tidigare:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Köra analys

Om du vill köra analysen, måste du koppla anteckningsboken i klustret:

1. Välj **Detached** och välj sedan den **centralanalysis** kluster.
1. Starta om klustret inte körs.
1. Välj knappen Kör för att du startar den bärbara datorn.

Du kan se ett fel i den sista cellen. I så, fall Kontrollera tidigare cellerna körs, vänta en minut för vissa data kan skrivas till storage och kör sedan den sista cellen igen.

### <a name="view-smoothed-data"></a>Visa utjämnade data

Rulla ned till cellen 14 för att se en rityta för löpande genomsnittlig fuktighet efter enhetstyp i anteckningsboken. Den här diagram uppdaterar kontinuerligt när strömmande telemetri anländer:

![Utjämnat telemetri diagram](media/howto-create-custom-analytics/telemetry-plot.png)

Du kan ändra storlek på diagrammet i anteckningsboken.

### <a name="view-box-plots"></a>Visa rutan ritar

I anteckningsboken rulla cell 20 att se den [rutan områden](https://en.wikipedia.org/wiki/Box_plot). Box-områden baseras på statiska data, så om du vill uppdatera dem måste du köra cellen:

![Box-områden](media/howto-create-custom-analytics/box-plots.png)

Du kan ändra storlek på områden i anteckningsboken.

## <a name="tidy-up"></a>Standardhuvudgrenen

Standardhuvudgrenen efter den här anvisningen och undvika onödiga kostnader genom att ta bort den **IoTCentralAnalysis** resursgrupp i Azure-portalen.

Du kan ta bort IoT Central-programmet från den **Management** sidan i programmet.

## <a name="next-steps"></a>Nästa steg

I den här guiden beskrivs hur du:

* Stream telemetri från en IoT Central-program med *löpande dataexport*.
* Skapa en Azure Databricks-miljö för att analysera och rita telemetridata.

Nu när du vet hur du skapar anpassade analytics föreslagna nästa steg är att lära dig hur du [visualisera och analysera dina Azure IoT Central data i en Power BI-instrumentpanel](howto-connect-powerbi.md).
