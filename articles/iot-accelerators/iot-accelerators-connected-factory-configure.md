---
title: Konfigurera den anslutna Factory topologin | Microsoft Docs
description: Hur du konfigurerar en ansluten Factory solution accelerator topologi.
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 1674ffefb3ee12584771e27d785e014830f84642
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Konfigurera anslutna Factory solution accelerator

Ansluten Factory solution accelerator visar en simulerad instrumentpanel för fiktiva företaget Contoso. Det här företaget har fabriker i flera globala platser globalt.

Den här artikeln använder Contoso som ett exempel som beskriver hur du konfigurerar topologin för en ansluten Factory-lösning.

## <a name="simulated-factories-configuration"></a>Simulerade fabriker konfiguration

Varje Contoso factory har produktion rader som består av tre stationer. Varje station är en verklig OPC UA-server med en viss roll:

* Sammansättningen station
* Testa station
* Paketering station

Servrarna OPC UA har OPC UA noder och [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) skickar värdena för dessa noder till anslutna fabriken. Det här omfattar:

* Driftstatus, till exempel aktuell energiförbrukning.
* Resulterade i produktionsinformation, till exempel antalet produkter.

Du kan använda instrumentpanelen till Contoso factory topologi från en global vy till vyn för station mer detaljerat. Ansluten Factory-instrumentpanelen kan:

* Visualisering av OEE och KPI siffror för varje lager i topologin.
* Visualisering av aktuella värden för OPC UA noder i stationer.
* Sammanställning av OEE och KPI siffror från station nivå på global nivå.
* Visualisering av aviseringar och åtgärder som ska utföras om värden nå specifika tröskelvärden.

## <a name="connected-factory-topology"></a>Anslutna Factory-topologi

Topologin för fabriker och produktion rader stationer är hierarkisk:

* Global nivå har factory noder som underordnade.
* Fabrikerna har produktionen noder som underordnade.
* Produktion raderna har station noder som underordnade.
* Stationer (OPC UA servrar) ha OPC UA noder som underordnade objekt.

Alla noder i topologin har en gemensam uppsättning egenskaper som definierar:

* En unik identifierare för noden topologi.
* Ett namn.
* En beskrivning.
* En bild.
* Underordnade noden topologi.
* Minimum, mål och högsta värden för OEE och KPI-bilder och aviseringsåtgärder ska köras.

## <a name="topology-configuration-file"></a>Topologi konfigurationsfilen

Om du vill konfigurera de egenskaper som anges i föregående avsnitt, ansluten Factory-lösningen använder en konfigurationsfil som kallas [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Du hittar den här filen i källkoden lösning i den `WebApp/Contoso/Topology` mapp.

Följande utdrag visar en översikt över de `ContosoTopologyDescription.json` konfigurationsfil:

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

Allmänna egenskaper för `<global_configuration>`, `<factory_configuration>`, `<production_line_configuration>`, och `<station_configuration>` är:

* **Namnet** (typen sträng)

  Definierar ett beskrivande namn, ska bara en av topologi noden ska visas i instrumentpanelen.

* **Beskrivning** (typen sträng)

  Beskriver noden topologi i detalj.

* **Bild** (typen sträng)

  Sökvägen till en bild i WebApp-lösningen ska visas när information om noden topologin visas i instrumentpanelen.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (typen `<performance_definition>`)

  Dessa egenskaper definiera minimal, mål och maximal värdena i den operativa bild används för att generera aviseringar. De här egenskaperna kan också definiera åtgärder för att köra om en avisering har identifierats.

Den `<factory_configuration>` och `<production_line_configuration>` objekt har en egenskap:

* **GUID** (typen sträng)

  Identifierar noden topologi.

`<factory_configuration>` har en egenskap:

* **Plats** (typen `<location_definition>`)

  Anger var fabriken är placerad.

`<station_configuration>` har egenskaper:

* **OpcUri** (typen sträng)

  Den här egenskapen måste anges till OPC UA program-URI för OPC UA-servern.
  Eftersom det måste vara globalt unika OPC UA-specifikationen, används den här egenskapen för att identifiera station topologi nod.

* **OpcNodes**, som är en matris med OPC UA noder (typen `<opc_node_description>`)

`<location_definition>` har egenskaper:

* **Stad** (typen sträng)

  Namnet på staden som är närmast platsen

* **Land** (typen sträng)

  Land för platsen

* **Latitud** (typen double)

  Latitud för platsen

* **Longitud** (typen double)

  Longituden för platsen

`<performance_definition>` har egenskaper:

* **Minsta** (typen double)

  Lägre tröskelvärde värdet kan komma åt. Om det aktuella värdet är under tröskelvärdet, skapas en avisering.

* **Målet** (typen double)

  Idealiskt målvärdet.

* **Maximal** (typen double)

  Övre tröskelvärde värdet kan komma åt. Om det aktuella värdet är över tröskeln, skapas en avisering.

* **MinimumAlertActions** (typen `<alert_action>`)

  Definierar de åtgärder som kan vidtas som svar på en avisering om minsta.

* **MaximumAlertActions** (typen `<alert_action>`)

  Definierar de åtgärder som kan vidtas som svar på en maximal avisering.

`<alert_action`> har egenskaper:

* **Typen** (typen sträng)

  Typ av avisering åtgärd. Följande är kända:

  * **AcknowledgeAlert**: status för aviseringen bör ändras till godkänt.
  * **CloseAlert**: alla äldre aviseringar av samma typ bör inte längre visas i instrumentpanelen.
  * **CallOpcMethod**: en OPC UA-metoden ska anropas.
  * **OpenWebPage**: ett fönster i webbläsaren ska öppnas med ytterligare kontextuella information.

* **Beskrivning** (typen sträng)

  Beskrivning av åtgärden visas i instrumentpanelen.

* **Parametern** (typen sträng)

  Parametrar som krävs för att utföra åtgärden. Värdet beror på typen av.

  * **AcknowledgeAlert**: ingen parameter som krävs.
  * **CloseAlert**: ingen parameter som krävs.
  * **CallOpcMethod**: nod information och parametrarna för metoden OPC UA att anropa i formatet ”NodeId av överordnad nod NodeId för metoden att anropa URI OPC UA Server”.
  * **OpenWebPage**: URL: en ska visas i webbläsarfönstret.

`<opc_node_description>` innehåller information om OPC UA noder i en station (OPC UA server). Noder som motsvarar ingen befintlig OPC UA noder, men används som lagring i logiken för beräkning av anslutna fabriken kan användas. Det har följande egenskaper:

* **NodeId** (typen sträng)

  Adressen till OPC UA nod på stationen (OPC UA serverns)-adressutrymme. Måste vara syntax som anges i OPC UA-specifikationen för en NodeId.

* **SymbolicName** (typen sträng)

  Namnet som visas i instrumentpanelen när värdet för den här noden OPC UA visas.

* **Relevanta** (matris av typen string)

  Anger beräkningen av OEE eller KPI OPC UA nodvärde är relevanta. Varje matriselement i kan vara något av följande värden:

  * **OeeAvailability_Running**: värdet är relevant för beräkning av OEE tillgänglighet.
  * **OeeAvailability_Fault**: värdet är relevant för beräkning av OEE tillgänglighet.
  * **OeePerformance_Ideal**: värdet är relevant för beräkning av OEE prestanda och är vanligtvis ett konstant värde.
  * **OeePerformance_Actual**: värdet är relevant för beräkning av OEE prestanda.
  * **OeeQuality_Good**: värdet är relevant för beräkning av OEE kvalitet.
  * **OeeQuality_Bad**: värdet är relevant för beräkning av OEE kvalitet.
  * **Kpi1**: värdet är relevant för beräkning av KPI1.
  * **Kpi2**: värdet är relevant för beräkning av KPI2.

* **OpCode** (typen sträng)

  Anger hur värdet för noden OPC UA hanteras i tid serien Insight frågor och OEE/KPI-beräkningar. Varje tid serien Insight fråga mål timespan specifika, vilket är en parameter för frågan och levererar en resultatuppsättning. OpCode styr hur resultatet är beräknad och kan vara något av följande värden:

  * **Diff**: skillnaden mellan sist och det första värdet i timespan.
  * **Genomsnittlig**: medelvärdet av alla värden i timespan.
  * **Summa**: summan av alla värden i timespan.
  * **Senaste**: används för närvarande inte.
  * **Antal**: antalet värden i timespan.
  * **Max**: värdet för maximal timespan.
  * **Min**: det minsta värdet i timespan.
  * **Konstanten**: resultatet är värdet som anges av egenskapen ConstValue.
  * **SubMaxMin**: skillnaden mellan maximalt och det minimala värdet.
  * **TimeSpan**: timespan.

* **Enheter** (typen sträng)

  Definierar en värdet för att visas i instrumentpanelen.

* **Synliga** (typen boolean)

  Styr om värdet ska visas i instrumentpanelen.

* **ConstValue** (typen double)

  Om den **OpCode** är **konstanten**, och sedan på den här egenskapen är värdet för noden.

* **Minsta** (typen double)

  Om det aktuella värdet hamnar under detta värde, skapas en avisering om minsta.

* **Maximal** (typen double)

  Om det aktuella värdet genererar ovanför det här värdet, skapas en avisering om maximala.

* **MinimumAlertActions** (typen `<alert_action>`)

  Definierar de åtgärder som kan vidtas som svar på en avisering om minsta.

* **MaximumAlertActions** (typen `<alert_action>`)

  Definierar de åtgärder som kan vidtas som svar på en maximal avisering.

På nivån station du också se **simuleringen** objekt. Dessa objekt bara används för att konfigurera anslutna Factory simuleringen och ska inte användas för att konfigurera en verklig topologi.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Hur konfigurationsdata används vid körning

Alla egenskaper som används i konfigurationsfilen kan grupperas i olika kategorier beroende på hur de används. Dessa typer är:

### <a name="visual-appearance"></a>Utseende

I den här kategorin definiera utseendet på instrumentpanelen anslutna fabriken. Exempel:

* Namn
* Beskrivning
* Bild
* Plats
* Enheter
* Synlig

### <a name="internal-topology-tree-addressing"></a>Internt topologi trädet adressering

WebApp upprätthåller en intern Dataordlista som innehåller information för alla noder i topologin. Egenskaperna **Guid** och **OpcUri** används som nycklar för åtkomst till den här ordlistan och måste vara unika.

### <a name="oeekpi-computation"></a>OEE/KPI beräkning

OEE/KPI siffrorna för anslutna Factory simuleringen parametriserade av:

* Noden OPC UA värden ska tas med i beräkningen.
* Hur beräknas bilden från telemetri värdena.

Anslutna Factory använder OEE formler som publicerats av den http://oeeindustrystandard.oeefoundation.org.

OPC UA nod objekt i stationer aktivera märkning för användning i OEE/KPI-beräkning. Den **relevans** egenskapen anger för vilken OEE/KPI-bild OPC UA nodvärde ska användas. Den **OpCode** egenskap anger hur värdet med i beräkningen.

### <a name="alert-handling"></a>Hantering av varning

Anslutna fabriken stöder en mekanism för enkel minsta/högsta tröskelvärdesbaserad varningsgenereringen. Det finns ett antal fördefinierade åtgärder som du kan konfigurera som svar på dessa aviseringar. Följande egenskaper styr den här mekanismen:

* Maximal
* Minimum
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Korrelerar till telemetridata

För vissa åtgärder, till exempel visualisera det sista värdet eller skapa tid serien Insight frågor i WebApp behöver en adresseringsschema för infogade telemetridata. Telemetri som skickas till anslutna fabriken måste också lagras i interna datastrukturer. Två egenskaper att aktivera dessa åtgärder är på stationen (OPC UA server) och OPC UA nodnivå:

* **OpcUri**

  Identifierar (globalt unik) OPC UA server telemetrin kommer från. I de infogade meddelandena skickas den här egenskapen som **ApplicationUri**.

* **nodeId**

  Identifierar nod-värde i OPC UA-server. Formatet på egenskapen måste vara som anges i OPC UA-specifikationen. I de infogade meddelandena skickas den här egenskapen som **NodeId**.

Kontrollera [detta](https://github.com/Azure/iot-edge-opc-publisher) GitHub-sidan för mer information om hur telemetridata inhämtas till anslutna Factory med OPC-utgivare.

## <a name="example-how-kpi1-is-calculated"></a>Exempel: Hur KPI1 beräknas

Konfigurationen i den `ContosoTopologyDescription.json` filen styr hur OEE/KPI figurerna beräknas. I följande exempel visas hur egenskaper i den här filen styra beräkning av KPI1.

I ansluten Factory KPI1 används för att mäta antalet produkter som har tillverkats under den senaste timmen. Varje station (OPC UA server) i simuleringen anslutna fabriken innehåller en OPC UA-nod (`NodeId: "ns=2;i=385"`), som tillhandahåller telemetri att beräkna KPI.

Konfigurationen för den här noden OPC UA ser ut som följande utdrag:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Den här konfigurationen kan frågor till telemetri värdena för den här noden med tiden serien insikter. Tid serien insikter frågan hämtar:

* Antal värden.
* Det minimala värdet.
* Det maximala värdet.
* Medelvärdet av alla värden.
* Summan av alla värden för alla unika **OpcUri** (**ApplicationUri**), **NodeId** par i ett givet timespan.

En egenskap för den **NumberOfManufactureredProducts** nodvärde är att den endast ökar. Om du vill beräkna antalet produkter som tillverkas på timespan anslutna fabriken använder den **OpCode** **SubMaxMin**. Beräkningen hämtar det minsta värdet i början av timespan och det högsta värdet i slutet av timespan.

Den **OpCode** konfigurerar beräkning logik för att beräkna ett resultat av skillnaden mellan de högsta och lägsta värde i konfigurationen. Dessa resultat kan sedan ackumulerad längst ned till rotnivån (global) och visas i instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

Ett förslag nästa steg är att lära dig hur du [distribuera en gateway på Windows- eller Linux för anslutna Factory solution accelerator](iot-accelerators-connected-factory-gateway-deployment.md).