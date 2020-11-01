---
title: Använd Time Series Insights för att lagra och analysera IoT Plug and Play enhets telemetri | Microsoft Docs
description: Konfigurera en Time Series Insights miljö och Anslut IoT Hub för att visa och analysera telemetri från dina IoT Plug and Play-enheter.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 0d4c24ed771b4d112a448ceec0e9fc2f9e8ac359
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146435"
---
# <a name="tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>Självstudie: skapa och Anslut till Time Series Insights Gen2 för att lagra, visualisera och analysera IoT Plug and Play Device-telemetri

I den här självstudien får du lära dig hur du skapar och konfigurerar en [Azure Time Series Insights Gen2](https://docs.microsoft.com/azure/time-series-insights/overview-what-is-tsi) -miljö (TSD) så att den integreras med din IoT plug and Play-lösning. Med TSD kan du samla in, bearbeta, lagra, fråga och visualisera Time Series-data på Sakernas Internet (IoT).

Först etablerar du en TSD-miljö och ansluter IoT Hub som en händelse källa för strömning. Sedan kommer du att arbeta med modell synkroniseringen för att skapa en miljö för en tids serie modell i TSD-miljön som baseras på de [DTDL-exempel (Digital based Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) som du använde för temperatur styrenheten och termostat enheter.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Du kan undvika kravet på att installera Azure CLI lokalt genom att använda Azure Cloud Shell för att konfigurera moln tjänsterna.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="time-series-id-selection"></a>Val av tids serie-ID

Vid etablering av TSD-miljön måste du välja ett Time Series-ID. Att välja rätt tids serie-ID är kritiskt eftersom egenskapen är oföränderlig och inte kan ändras efter att den har angetts. Att välja ett Time Series-ID är som att välja en partitionsnyckel för en databas. Ditt TS-ID bör vanligt vis vara noden lövnod i din till gångs modell. Med andra ord vill du normalt välja egenskapen ID för den mest detaljerade till gång eller sensor som avger telemetri.

Som IoT Plug and Play-användare är den relevanta frågan för att välja ditt TS-ID förekomsten av [komponenterna](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#component) i enhets modellerna. 

![Val av TS-ID](./media/tutorial-configure-tsi/ts-id-selection-pnp.png)

* Om du har utfört snabb starten och IoT Hub enheten representerar [termostat](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json)använder du `iot-hub-connection-device-id` som ditt TS-ID.

* Om du har gjort någon av självstudierna för [TemperatureController](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json)med flera komponenter använder du en sammansatt nyckel i avsnittet nedan, skrivet som  `iot-hub-connection-device-id, dt-subject`

## <a name="provision-your-azure-time-series-insights-gen2-environment"></a>Etablera din Azure Time Series Insights Gen2-miljö

Kommandot nedan gör följande:

* Skapar ett Azure Storage-konto för din miljös [kall butik](https://docs.microsoft.com/azure/time-series-insights/concepts-storage#cold-store), utformad för långsiktig kvarhållning och analys över historiska data.
  * Ersätt `mytsicoldstore` med ett unikt namn för ditt konto.
* Skapar en Azure Time Series Insights Gen2-miljö, inklusive varm lagring med en kvarhållningsperiod på 7 dagar och ett kall Arkiv för oändlig kvarhållning. 
  * Ersätt `my-tsi-env` med ett unikt namn för din TSD-miljö 
  * Ersätt `my-pnp-resourcegroup` med namnet på den resurs grupp som du använde när du konfigurerade
  * Ersätt `my-ts-id-property` med ditt TS-ID egenskaps värde baserat på urvalskriterierna ovan

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties my-ts-id-property --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

Nu ska du konfigurera IoT Hub som du skapade tidigare som din miljös [händelse källa](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingestion-event-sources). När din händelse källa är ansluten börjar TSD indexera händelser från din hubb, med början från den tidigaste händelsen i kön.

Skapa först en unik konsument grupp på din IoT Hub för din TSD-miljö. Ersätt `my-pnp-hub` med namnet på den IoT Hub som du använde tidigare.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group 
```

Anslut IoT Hub. Ersätt `my-pnp-resourcegroup` , `my-pnp-hub` och `my-tsi-env` med dina respektive värden.

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```
Navigera till din resurs grupp i [Azure Portal](https://portal.azure.com) och välj den nyligen skapade Time Series Insightss miljön. Besök den *Time Series Insights Explorer-URL* som visas i instans översikten.

![Portalöversiktssida](./media/tutorial-configure-tsi/view-environment.png)

I Utforskaren bör du se dina två termostater under "alla hierarkier". Härnäst ska du granska din tids serie modell som baseras på din enhets modell.

![Explorer-vy 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="model-synchronization-between-digital-twins-definition-language-and-time-series-insights-gen2"></a>Synkronisering av modeller mellan digitala dubbla definitions språk och Time Series Insights Gen2

Härnäst ska du översätta din DTDL-enhets modell till till gångs modellen i Azure Time Series Insights (TSD). TSD: s tids serie modell är ett semantiskt modellerings verktyg för data contextualization i TSD. Tids serie modellen har tre huvud komponenter:

* [Tids serie modell instanser](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-instances). Instanser är virtuella representationer av själva tids serien. Instanser identifieras unikt av ditt TS-ID.
* [Hierarkier för tids serie modell](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-hierarchies). Hierarkier organiserar instanser genom att ange egenskaps namn och deras relationer.
* [Modell typer för tids serier](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-types). Med hjälp av typer kan du definiera [variabler](https://docs.microsoft.com/azure/time-series-insights/concepts-variables) eller formler för att utföra beräkningar. Typer är associerade med en angiven instans.

> [!NOTE]
> I exemplen nedan finns TemperatureController för flera komponenter.

### <a name="define-your-types"></a>Definiera dina typer

Du kan börja mata in data i Azure Time Series Insights Gen2 utan att ha en fördefinierad modell. När telemetri kommer försöker TSD att automatiskt lösa Time Series-instanser baserat på egenskap svärdet för TS-ID. Alla instanser tilldelas *standard typen* . Du måste skapa en ny typ manuellt för att representera dina modeller. Bilden nedan visar en enkel metod för att synkronisera en DTDL-modell och en TSM-typ:

![DTDL till TSM-typ](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

* Den digitala, dubbla modell identifieraren (DTMI) blir ditt typ-ID
* Typ namnet kan antingen vara modellens namn eller visnings namn
* Modell beskrivningen blir typens Beskrivning
* Minst en typ variabel skapas för varje telemetri-komponent som har ett numeriskt schema. 
  * Det går bara att använda numeriska data typer för variabler, men om ett värde skickas som en sträng som kan parsas, `"0"` kan du till exempel använda en [konverterings](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) funktion som `toDouble`
* Variabel namnet kan antingen vara namnet på telemetri eller visnings namnet
* När du definierar uttryck för variabeln Time Series (TSX), se telemetrins namn på kabeln och dess datatyp.

> [!NOTE]
> I det här exemplet visas endast två variabler – en agg regering och ett numeriskt värde, men varje typ kan ha upp till 100. Olika variabler kan referera till samma telemetri-värde för att utföra olika calucaultions vid behov. För en fullständig lista över filter, agg regeringar och skalära funktioner visas [syntaxen Time Series Insights Gen2 Time Series Expression syntax](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) .

Öppna valfri text redigerare och spara JSON-filen nedan till din lokala enhet:

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:Thermostat;1",
      "name": "Thermostat",
      "description": "Reports current temperature and provides desired temperature control.",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.temperature.Double"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

I Time Series Insights Explorer navigerar du till fliken modell genom att klicka på modell ikonen till vänster. Klicka på **typer** och klicka på **överför JSON** :

![Ladda upp](./media/tutorial-configure-tsi/upload-type.png)

Välj **Välj fil** , Välj den JSON som du sparade tidigare och klicka på **överför**

Du bör se den nyligen definierade termostat-typen.

### <a name="optional---create-a-hierarchy"></a>Valfritt – skapa en hierarki

Du kan också skapa en hierarki för att organisera de två termostat-komponenterna under deras TemeraptureController-överordnade.

Klicka på *hierarkier* och välj *Lägg till en hierarki* . Ange `Device Fleet` som namn och skapa en nivå med namnet `Device Name` och klicka sedan på *Spara* .

![Lägg till en hierarki](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Tilldela dina instanser till rätt typ

Härnäst ska du ändra typ av instanser och eventuellt Situate dem i hierarkin

Välj fliken *instanser* och klicka på ikonen *Redigera* längst till höger.

![Redigera instanser](./media/tutorial-configure-tsi/edit-instance.png)

Klicka på list rutan typ och välj `Thermostat` . 

![Ändra instans typ](./media/tutorial-configure-tsi/change-type.png)

Om du har skapat en hierarki väljer du *instans fält* och markerar `Device Fleet` rutan. Ange `Temperature Controller` som värde för den överordnade enheten och klicka sedan på *Spara* .

![Tilldela till hierarki](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Upprepa stegen ovan för din andra termostat.

### <a name="view-your-data"></a>Visa dina data

Gå tillbaka till diagram fönstret och expandera enhets flottan och TemperatureController. Klicka på thermostat1, Välj `Temperature` variabeln och klicka sedan på *Lägg till* diagrammets värde. Gör samma sak för thermostat2.

![Ändra instans typ](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>Nästa steg

* Om du vill veta mer om olika diagram alternativ, inklusive intervall storlek och kontroll för Y-axeln, kan du läsa dokumentationen för [Azure Time Series Insights Explorer ](https://docs.microsoft.com/azure/time-series-insights/concepts-ux-panels) .

* En djupgående översikt över din miljös tids serie modell finns i [den här](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview) artikeln.

* [Välj](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis)om du vill gå in i fråge-API: erna och syntaxen för Time Series-uttryck.




