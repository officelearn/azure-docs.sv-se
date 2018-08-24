---
title: Konfigurera ansluten fabrik-topologi | Microsoft Docs
description: Så här konfigurerar du topologin för en ansluten fabrik-lösningsaccelerator.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 8cb3cae396016545c5d78a2ff7ccde4a053c4cf1
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746987"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Konfigurera ansluten fabrik lösningsaccelerator

Ansluten fabrik lösningsaccelerator visar en simulerad instrumentpanel för det fiktiva företaget Contoso. Det här företaget har fabriker globalt i ett flertal globala platser.

Den här artikeln använder Contoso som ett exempel som beskriver hur du konfigurerar topologin för en ansluten fabrik-lösning.

## <a name="simulated-factories-configuration"></a>Simulerade fabriker konfiguration

Varje Contoso factory har produktionsrader som består av tre stationer. Varje station är en verklig OPC UA-server med en viss roll:

* Monteringsstation
* Testa station
* Förpackningsstation

Dessa OPC UA-servrar har OPC UA-noder och [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) skickar värdena för dessa noder till ansluten fabrik. Det här omfattar:

* Aktuella driftstatus, till exempel aktuell energiförbrukning.
* Produktionsinformation, till exempel hur många produkter gav.

Du kan använda instrumentpanelen för att öka detaljnivån i Contoso factory topologin från en global vy nedåt en nivå stationsvyn. Instrumentpanel för ansluten fabrik kan:

* Visualisering av OEE och KPI-värden för varje lager i topologin.
* Visualisering av aktuella värden för OPC UA-noder i stationer.
* Sammanställning av de OEE och KPI figurerna från station nivå till global nivå.
* Visualisering av aviseringar och åtgärder som ska utföras om värden når specifika tröskelvärden.

## <a name="connected-factory-topology"></a>Ansluten fabrik-topologi

Topologin för fabriker och produktionsrader stationer är hierarkiska:

* Global nivå har factory noder som underordnade.
* Fabriker har produktionslinje noder som underordnade.
* Produktionslinjerna ha station noder som underordnade.
* Stationer (OPC UA-servrar) ha OPC UA-noder som underordnade.

Varje nod i topologin har en gemensam uppsättning egenskaper som definierar:

* En unik identifierare för noden topologi.
* Ett namn.
* En beskrivning.
* En bild.
* De underordnade objekten för noden topologi.
* Minimum, mål och högsta värden för OEE och KPI-bilder och aviseringsåtgärder att köra.

## <a name="topology-configuration-file"></a>Konfigurationsfilen för topologi

Om du vill konfigurera de egenskaper som anges i föregående avsnitt, ansluten fabrik-lösningen använder en konfigurationsfil med namnet [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Du hittar den här filen i källkoden för lösningen i den `WebApp/Contoso/Topology` mapp.

Följande kodavsnitt visar en översikt över den `ContosoTopologyDescription.json` konfigurationsfil:

```json
{
  <global_configuration>,
  "Factories": [
    <factory_configuration>,
    "ProductionLines": [
      <production_line_configuration>,
      "Stations": [
        <station_configuration>,
        <more station_configurations>
      ],
      <more production_line_configurations>
    ]
    <more factory_configurations>
  ]
}
```

Gemensamma egenskaper för `<global_configuration>`, `<factory_configuration>`, `<production_line_configuration>`, och `<station_configuration>` är:

* **Namn på** (typen sträng)

  Definierar ett beskrivande namn, vilket ska vara bara ett ord som noden topologi ska visas i instrumentpanelen.

* **Beskrivning av** (typen sträng)

  Beskriver noden topologi i detalj.

* **Bild** (typen sträng)

  Sökvägen till en bild i WebApp-lösningen för att visa när information om noden topologin visas i instrumentpanelen.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (typ `<performance_definition>`)

  De här egenskaperna definiera minimal, mål och maximal värdena för den operativa bild används för att generera aviseringar. De här egenskaperna kan också definiera åtgärderna som ska köra om en avisering har identifierats.

Den `<factory_configuration>` och `<production_line_configuration>` objekt har en egenskap:

* **GUID** (typen sträng)

  Identifierar noden topologi.

`<factory_configuration>` har en egenskap:

* **Plats** (typ `<location_definition>`)

  Anger var fabriken finns.

`<station_configuration>` har egenskaper:

* **OpcUri** (typen sträng)

  Den här egenskapen måste anges till URI: N för OPC UA-program för OPC UA-servern.
  Eftersom det måste vara globalt unikt OPC UA-specifikationen, används den här egenskapen för att identifiera station topologi nod.

* **OpcNodes**, som är en matris med OPC UA-noder (typ `<opc_node_description>`)

`<location_definition>` har egenskaper:

* **Stad** (typen sträng)

  Namn på ort som är närmast platsen

* **Land** (typen sträng)

  Platsens land

* **Latitud** (typen double)

  Latitud för platsen

* **Longitud** (typen double)

  Longitud för platsen

`<performance_definition>` har egenskaper:

* **Minsta** (typen double)

  Lägre tröskelvärde värdet kan nå. Om det aktuella värdet är lägre än det här tröskelvärdet, genereras en avisering.

* **Target** (typen double)

  Perfekt målvärdet.

* **Maximal** (typen double)

  Övre tröskelvärde värdet kan nå. Om det aktuella värdet är över tröskeln, genereras en avisering.

* **MinimumAlertActions** (typ `<alert_action>`)

  Definierar de åtgärder som kan vidtas som svar på en avisering om minsta.

* **MaximumAlertActions** (typ `<alert_action>`)

  Definierar de åtgärder som kan vidtas som svar på en maximal avisering.

`<alert_action`> har egenskaper:

* **Typ** (typen sträng)

  Typ av aviseringsåtgärden. Följande typer är kända:

  * **AcknowledgeAlert**: status för aviseringen bör ändras till godkända.
  * **CloseAlert**: alla äldre aviseringar av samma typ bör inte längre visas i instrumentpanelen.
  * **CallOpcMethod**: en OPC UA-metod som ska anropas.
  * **OpenWebPage**: ska öppnas ett webbläsarfönster som visar ytterligare sammanhangsinformation.

* **Beskrivning av** (typen sträng)

  Beskrivning av åtgärden visas i instrumentpanelen.

* **Parametern** (typen sträng)

  Parametrar som krävs för att utföra åtgärden. Värdet beror på typen av.

  * **AcknowledgeAlert**: ingen parameter som krävs.
  * **CloseAlert**: ingen parameter som krävs.
  * **CallOpcMethod**: nodinformation och parametrar av OPC UA-metod för att anropa i formatet ”NodeId av överordnad nod NodeId för metoden ska anropas, URI: N för OPC UA-servern”.
  * **OpenWebPage**: URL: en ska visas i webbläsarfönstret.

`<opc_node_description>` innehåller information om OPC UA-noder i en station (OPC UA-servern). Noder som motsvarar ingen befintlig OPC UA-noder, men används som lagring i beräkningen logiken för ansluten fabrik kan användas. Den har följande egenskaper:

* **NodeId** (typen sträng)

  Adressen till OPC UA nod i stationens (OPC UA serverns)-adressutrymme. Syntax måste vara som angetts i OPC UA-specifikationen för en NodeId.

* **SymbolicName** (typen sträng)

  Namnet som ska visas i instrumentpanelen när värdet för den här OPC UA-noden visas.

* **Relevans** (matris av typen sträng)

  Anger beräkning av OEE- eller KPI-värde för OPC UA-noden är relevanta. Varje matriselement kan vara något av följande värden:

  * **OeeAvailability_Running**: värdet är relevant för beräkning av OEE-tillgänglighet.
  * **OeeAvailability_Fault**: värdet är relevant för beräkning av OEE-tillgänglighet.
  * **OeePerformance_Ideal**: värdet är relevant för beräkning av OEE-prestanda och är vanligtvis ett konstant värde.
  * **OeePerformance_Actual**: värdet är relevant för beräkning av OEE-prestanda.
  * **OeeQuality_Good**: värdet är relevant för beräkning av OEE-kvalitet.
  * **OeeQuality_Bad**: värdet är relevant för beräkning av OEE-kvalitet.
  * **Kpi1**: värdet är relevant för beräkning av KPI1.
  * **Kpi2**: värdet är relevant för beräkning av KPI2.

* **OpCode** (typen sträng)

  Anger hur värdet för OPC UA-noden hanteras i Time Series Insight frågor och OEE/KPI-beräkningar. Varje Time Series Insight-fråga riktar sig mot en specifik timespan, vilket är en parameter av frågan och ger ett resultat. OpCode styr hur resultatet är beräknad och kan vara något av följande värden:

  * **Diff**: skillnaden mellan sist och det första värdet i timespan.
  * **Genomsnittlig**: medelvärdet av alla värden i timespan.
  * **Summa**: summan av alla värden i timespan.
  * **Senaste**: används inte för närvarande.
  * **Antal**: antalet värden i timespan.
  * **Max**: maximal värdet i timespan.
  * **Min**: det minimala värdet i timespan.
  * **Konst**: resultatet är värdet som anges av egenskapen ConstValue.
  * **SubMaxMin**: skillnaden mellan maximalt och minimalt värde.
  * **TimeSpan**: hur lång.

* **Enheter** (typen sträng)

  Definierar en värdet för visning i instrumentpanelen.

* **Synliga** (typen boolean)

  Kontrollerar om värdet ska visas i instrumentpanelen.

* **ConstValue** (typen double)

  Om den **OpCode** är **konst**, och sedan på den här egenskapen är värdet för noden.

* **Minsta** (typen double)

  Om det aktuella värdet hamnar under detta värde, skapas en avisering om minsta.

* **Maximal** (typen double)

  Om det aktuella värdet genererar ovanför det här värdet, avisering en maximal.

* **MinimumAlertActions** (typ `<alert_action>`)

  Definierar de åtgärder som kan vidtas som svar på en avisering om minsta.

* **MaximumAlertActions** (typ `<alert_action>`)

  Definierar de åtgärder som kan vidtas som svar på en maximal avisering.

På nivån station du också se **simulering** objekt. De här objekten används endast för att konfigurera ansluten fabrik simuleringen och ska inte användas för att konfigurera en verklig topologi.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Hur konfigurationsdata används vid körning

Alla egenskaper som används i konfigurationsfilen kan grupperas i olika kategorier beroende på hur de används. Dessa typer är:

### <a name="visual-appearance"></a>Utseende

I den här kategorin definierar det visuella utseendet på instrumentpanelen för ansluten fabrik. Exempel:

* Namn
* Beskrivning
* Bild
* Plats
* Enheter
* Synlig

### <a name="internal-topology-tree-addressing"></a>Intern topologi trädet-adressering

Webbappen har en intern Dataordlista som innehåller information för alla noder i topologin. Egenskaperna **Guid** och **OpcUri** används som nycklar för att komma åt den här ordlistan och måste vara unikt.

### <a name="oeekpi-computation"></a>OEE/KPI-beräkning

OEE/KPI-siffror för ansluten fabrik simuleringen parametriseras genom att:

* OPC UA-nodvärden ska tas med i beräkningen.
* Hur bilden beräknas från telemetrivärden.

Ansluten fabrik använder OEE-formler som publicerats av den http://www.oeefoundation.org.

Objekt för OPC UA-noden i stationer aktivera tagga för användning i OEE/KPI-beräkning. Den **relevans** egenskapen anger för vilken OEE/KPI-bild OPC UA-noden värdet ska användas. Den **OpCode** egenskapen definierar hur värdet är med i beräkningen.

### <a name="alert-handling"></a>Avisera hantering

Ansluten fabrik stöder en mekanism för enkel gränsvärden tröskelbaserade genereringen. Det finns ett antal fördefinierade åtgärder som du kan konfigurera som svar på dessa aviseringar. Följande egenskaper styr den här mekanismen:

* Maximal
* Minimal
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Korrelera för telemetridata

För vissa åtgärder, till exempel visualisera det sista värdet eller skapa Time Series Insight frågor Webbappen behöver en adresseringsschema för insamlade telemetridata. Telemetri som skickas till ansluten fabrik måste också lagras i interna datastrukturer. De två egenskaperna som aktiverar de här åtgärderna är på station (OPC UA-server) och OPC UA-noden nivå:

* **OpcUri**

  Identifierar (globalt unikt) OPC UA-servern telemetri som kommer från. I de inmatade meddelandena skickas den här egenskapen som **ApplicationUri**.

* **NodeId**

  Identifierar Nodvärdet i OPC UA-servern. Formatet på egenskapen måste vara som angetts i OPC UA-specifikationen. I de inmatade meddelandena skickas den här egenskapen som **NodeId**.

Kontrollera [detta](https://github.com/Azure/iot-edge-opc-publisher) GitHub-sidan för mer information om hur dessa data matas in ansluten fabrik med hjälp av OPC Publisher.

## <a name="example-how-kpi1-is-calculated"></a>Exempel: Hur KPI1 beräknas

Konfigurationen i den `ContosoTopologyDescription.json` filen styr hur OEE/KPI-bilder beräknas. I följande exempel visas hur egenskaper i den här filen styra beräkning av KPI1.

I ansluten fabrik KPI1 används för att mäta antalet har tillverkad produkter under den senaste timmen. Varje station (OPC UA-server) i simuleringen ansluten fabrik innehåller en OPC UA-noden (`NodeId: "ns=2;i=385"`), som tillhandahåller telemetri att beräkna denna KPI.

Konfigurationen för den här OPC UA-noden som ser ut som följande kodavsnitt:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Den här konfigurationen möjliggör frågekörning för telemetrivärden i den här noden med Time Series Insights. Time Series Insights-fråga hämtar:

* Antalet värden.
* Det minimala värdet.
* Maximal värde.
* Medelvärdet av alla värden.
* Summan av alla värden för alla unika **OpcUri** (**ApplicationUri**), **NodeId** par i ett visst tidsintervall.

En egenskap för den **NumberOfManufactureredProducts** nodvärde är att den endast ökar. För att beräkna antalet produkter som tillverkas på timespan, ansluten fabrik använder den **OpCode** **SubMaxMin**. Beräkningen hämtar det minsta värdet i början av tidsintervallet och det högsta värdet i slutet av tidsintervallet.

Den **OpCode** konfigurerar beräkning logik för att beräkna resultatet av skillnaden mellan de högsta och lägsta värde i konfigurationen. Dessa resultat är sedan ackumuleras längst ned till rotnivå (global) och visas i instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

Ett förslag på nästa steg är att lära dig hur du [distribuera en gateway på Windows eller Linux för ansluten fabrik lösningsaccelerator](iot-accelerators-connected-factory-gateway-deployment.md).
