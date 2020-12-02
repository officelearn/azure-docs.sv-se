---
title: Använd Time Series Insights för att lagra och analysera Azure IoT Plug and Play-telemetri för enheter | Microsoft Docs
description: Konfigurera en Time Series Insights-miljö och Anslut IoT Hub för att visa och analysera telemetri från IoT Plug and Play-enheter.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ad5c6f205fc832eb125e52b4135990fc58742e62
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453236"
---
# <a name="preview-tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>För hands versions Självstudier: skapa och Anslut till Time Series Insights Gen2 för att lagra, visualisera och analysera IoT Plug and Play Device-telemetri

I den här självstudien får du lära dig hur du skapar och konfigurerar en [Azure Time Series Insights Gen2](../time-series-insights/overview-what-is-tsi.md) -miljö (TSD) så att den integreras med din IoT plug and Play-lösning. Använd TSD för att samla in, bearbeta, lagra, fråga och visualisera Time Series-data på Sakernas Internet (IoT).

Först etablerar du en TSD-miljö och ansluter din IoT Hub som en händelse källa för strömmande data. Sedan arbetar du med synkronisering av modeller för att redigera din [tids serie modell](../time-series-insights/concepts-model-overview.md) baserat på [DTDL-exempelfilerna (Digital based Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) som du använde för temperatur styrenheten och termostat enheter.

> [!NOTE]
> Den här integrationen är i för hands version. Hur DTDL enhets modeller mappas till tids serie modellen kan ändras.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

I det här läget har du:

* En Azure IoT-hubb.
* En DPS-instans som är länkad till din IoT Hub med en individuell enhets registrering för din IoT Plug and Play-enhet.
* En anslutning till din IoT-hubb från en enda eller enhet med flera komponenter, som strömmar simulerade data.

Du kan undvika kravet på att installera Azure CLI lokalt genom att använda Azure Cloud Shell för att konfigurera moln tjänsterna.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>Förbered din händelse källa

IoT-hubben som du skapade tidigare är din TSD-Miljös [händelse källa](../time-series-insights/concepts-streaming-ingestion-event-sources.md).

> [!IMPORTANT]
> Inaktivera alla befintliga IoT Hub vägar. Det finns ett känt problem när du använder en IoT-hubb som en händelse källa för TSD med konfigurerad [routning](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints) . Inaktivera tillfälligt alla Dirigerings slut punkter och när IoT-hubben är ansluten till TSD kan du återaktivera dem.

Skapa en unik konsument grupp på IoT-hubben för att använda TSD. Ersätt `my-pnp-hub` med namnet på den IoT-hubb som du tidigare använde:

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-your-time-series-id"></a>Välj tids serie-ID

När du etablerar en TSD-miljö måste du välja ett *Time Series-ID*. Det är viktigt att välja rätt tids serie-ID eftersom den här egenskapen är oföränderlig och inte kan ändras efter att den har angetts. Ett Time Series-ID är som en databas partitions nyckel. Time Series-ID fungerar som primär nyckel för din tids serie modell. Mer information finns i [metod tips för att välja ett Time Series-ID](../time-series-insights/how-to-select-tsid.md).

Som IoT Plug and Play-användare anger du en _sammansatt nyckel_ som består av `iothub-connection-device-id` och `dt-subject` som tids serie-ID. IoT Hub lägger till dessa system egenskaper som innehåller ditt IoT Plug and Play-enhets-ID och enhets komponent namnen.

Även om din IoT Plug and Play enhets modeller för närvarande inte använder komponenter, bör du inkludera `dt-subject` som en del av en sammansatt nyckel så att du kan använda dem i framtiden. Eftersom din Time Series-ID är oföränderlig rekommenderar Microsoft att du aktiverar det här alternativet om du behöver det i framtiden.

> [!NOTE]
> Exemplen nedan gäller för **TemperatureController** -enheten för flera komponenter, men begreppen är desamma för termostat-enheten (No-Component **Thermostat** ).

## <a name="provision-your-tsi-environment"></a>Etablera en TSD-miljö

I det här avsnittet beskrivs hur du etablerar din Azure Time Series Insights Gen2-miljö.

Följande kommando:

* Skapar ett Azure Storage-konto för din miljös [kall butik](../time-series-insights/concepts-storage.md#cold-store), utformad för långsiktig kvarhållning och analys över historiska data.
  * Ersätt `mytsicoldstore` med ett unikt namn för ditt kalla lagrings konto.
* Skapar en Azure Time Series Insights Gen2-miljö, inklusive varm lagring med en kvarhållningsperiod på sju dagar och kall lagring för oändlig kvarhållning.
  * Ersätt `my-tsi-env` med ett unikt namn för din TSD-miljö.
  * Ersätt `my-pnp-resourcegroup` med namnet på den resurs grupp som du använde när du konfigurerade.
  * `iothub-connection-device-id, dt-subject` är din Time Series ID-egenskap.

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties iothub-connection-device-id, dt-subject --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

Anslut din IoT Hub händelse källa. Ersätt `my-pnp-resourcegroup` , `my-pnp-hub` och `my-tsi-env` med de värden som du har valt. Följande kommando hänvisar till konsument gruppen för TSD som du skapade tidigare:

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```

Navigera till resurs gruppen i [Azure Portal](https://portal.azure.com) och välj den nya Time Series Insightss miljön. Besök *URL: en för Time Series Insights Explorer* som visas i instans översikten:

![Portalöversiktssida](./media/tutorial-configure-tsi/view-environment.png)

I Utforskaren ser du tre instanser:

* &lt;ditt PnP-enhets-ID &gt; , thermostat1
* &lt;ditt PnP-enhets-ID &gt; , thermostat2
* &lt;ditt PnP-enhets-ID &gt; , `null`

> [!NOTE]
> Den tredje taggen representerar telemetri från själva **TemperatureController** , till exempel arbets minnet för enhets minnet. Eftersom det här är en egenskap på den översta nivån är värdet för komponent namnet null. I ett senare steg uppdaterar du detta till ett mer användarvänligt namn.

![Explorer-vy 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>Konfigurera modell Översättning

Sedan översätter du din DTDL-enhets modell till till gångs modellen i Azure Time Series Insights (TSD). TSD: s tids serie modell är ett semantiskt modellerings verktyg för data contextualization i TSD. Tids serie modellen har tre huvud komponenter:

* [Tids serie modell instanser](../time-series-insights/concepts-model-overview.md#time-series-model-instances). Instanser är virtuella representationer av själva tids serien. Instanser identifieras unikt av ditt Time Series ID.
* [Hierarkier för tids serie modell](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies). Hierarkier organiserar instanser genom att ange egenskaps namn och deras relationer.
* [Modell typer för tids serier](../time-series-insights/concepts-model-overview.md#time-series-model-types). Med hjälp av typer kan du definiera [variabler](../time-series-insights/concepts-variables.md) eller formler för beräkningar. Typer är associerade med en angiven instans.

### <a name="define-your-types"></a>Definiera dina typer

Du kan börja mata in data i Azure Time Series Insights Gen2 utan att ha en fördefinierad modell. När telemetri kommer försöker underavsnittet att lösa Time Series-instanser baserat på dina tids serie-ID-egenskaps värden. Alla instanser tilldelas *standard typen*. Du måste skapa en ny typ manuellt för att kunna kategorisera dina instanser. Följande information visar den enklaste metoden för att synkronisera enhets DTDL modeller med dina tids serie modell typer:

* Den digitala, dubbla modell identifieraren blir ditt typ-ID.
* Typ namnet kan vara antingen modell namnet eller visnings namnet.
* Modell beskrivningen blir typens beskrivning.
* Minst en typ variabel skapas för varje telemetri med ett numeriskt schema.
  * Endast numeriska data typer kan användas för variabler, men om ett värde skickas som en annan typ som kan konverteras, `"0"` kan du använda en [konverterings](/rest/api/time-series-insights/reference-time-series-expression-syntax.md#conversion-functions) funktion som t `toDouble` . ex..
* Variabel namnet kan antingen vara telemetri-namnet eller visnings namnet.
* När du definierar ett variabelt tids serie uttryck kan du se telemetrins namn på kabeln och dess datatyp.

| DTDL-JSON | JSON-typ för tids serie modell | Exempelvärde |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |  
|`contents` lagringsmatriser| `variables` jobbobjektet  | Visa exemplet nedan

![DTDL till tids serie modell typ](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> Det här exemplet visar tre variabler, men varje typ kan ha upp till 100. Olika variabler kan referera till samma telemetri-värde för att utföra olika beräkningar vid behov. En fullständig lista över filter, agg regeringar och skalära funktioner finns [Time Series Insights Gen2 Time Series Expression syntax](/rest/api/time-series-insights/reference-time-series-expression-syntax.md).

Öppna en text redigerare och spara följande JSON-fil på din lokala enhet:

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:TemperatureController;1",
      "name": "Temperature Controller",
      "description": "Device with two thermostats and remote reboot.",
      "variables": {
        "workingSet": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.workingSet.Long, toLong($event.workingSet.Double))"
          }, 
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.temperature.Long, toLong($event.temperature.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "eventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        }
      }
    }
  ]
}
```

I Time Series Insights Explorer navigerar du till fliken **modell** genom att välja modell ikonen till vänster. Välj **typer** och välj sedan **Ladda upp JSON**:

![Ladda upp](./media/tutorial-configure-tsi/upload-type.png)

Välj **Välj fil**, Välj den JSON som du sparade tidigare och välj **Ladda upp**

Du ser den nyligen definierade typen av **temperatur kontrollant** .

### <a name="create-a-hierarchy"></a>Skapa en hierarki

Skapa en hierarki för att organisera taggarna under deras **TemperatureController** överordnade objekt. Följande enkla exempel har en nivå, men IoT-lösningar har ofta många nivåer av kapsling till sätta-Taggar i deras fysiska och semantiska position inom en organisation.

Välj **hierarkier** och välj **Lägg till hierarki**. Ange *enhets flottan* som namn och skapa en nivå som heter *enhets namn*. Välj sedan **Spara**.

![Lägg till en hierarki](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Tilldela dina instanser till rätt typ

Sedan ändrar du typ av instanser och placerar dem i hierarkin.

Välj fliken **instanser** , leta upp den instans som representerar enhetens arbets minne och välj ikonen **Redigera** längst till höger:

![Redigera instanser](./media/tutorial-configure-tsi/edit-instance.png)

Välj List rutan **typ** och välj **temperatur styrenhet**. Ange *defaultComponent <your device name>* för att uppdatera namnet på instansen som representerar alla översta taggar som är associerade med din enhet.

![Ändra instans typ](./media/tutorial-configure-tsi/change-type.png)

Innan du väljer Spara väljer du fliken **instans fält** och kontrollerar **enhets flottan i rutan enhets flottan** . Ange `<your device name> - Temp Controller` för att gruppera Telemetrin tillsammans och välj sedan **Spara**.

![Tilldela till hierarki](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Upprepa föregående steg för att tilldela termostat-taggarna rätt typ och hierarki.

## <a name="view-your-data"></a>Visa dina data

Gå tillbaka till diagram fönstret och expandera **enhets flottan > enheten**. Välj **thermostat1**, Välj **temperatur** variabeln och välj sedan **Lägg till** i diagrammet värdet. Gör samma sak för **thermostat2** och **defaultComponent** för det **aktiva** värdet.

![Ändra instans typ för thermostat2](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om olika diagram alternativ, inklusive intervall storlek och kontroll av Y-axeln, finns i [Azure Time Series Insights Explorer](../time-series-insights/concepts-ux-panels.md).

* En djupgående översikt över din miljös tids serie modell finns i [tids serie modell i Azure Time Series Insights Gen2](../time-series-insights/concepts-model-overview.md) -artikeln.

* Information om API: n för frågor och tids serie uttryck finns i [Azure Time Series Insights Gen2-fråge-API: er](/rest/api/time-series-insights/reference-query-apis.md).
