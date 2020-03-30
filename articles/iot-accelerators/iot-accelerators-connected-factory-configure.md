---
title: Konfigurera topologin Ansluten fabrik – Azure | Microsoft-dokument
description: I den här artikeln beskrivs hur du konfigurerar lösningsacceleratorn för Ansluten fabrik, inklusive dess topologi.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 5fa3d4d4fdfa0dd81cd8ab8772ffb3903dda289f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820117"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Konfigurera lösningsacceleratorn för Ansluten fabrik

Lösningsacceleratorn connected factory visar en simulerad instrumentpanel för ett fiktivt företag Contoso. Detta företag har fabriker på många globala platser globalt.

I den här artikeln används Contoso som ett exempel för att beskriva hur du konfigurerar topologin för en ansluten fabrikslösning.

## <a name="simulated-factories-configuration"></a>Konfiguration av simulerade fabriker

Varje Contoso-fabrik har produktionslinjer som består av tre stationer vardera. Varje station är en riktig OPC UA-server med en specifik roll:

* Monteringsstation
* Teststation
* Förpackningsstation

Dessa OPC UA-servrar har OPC UA-noder och [OPC Publisher](overview-opc-publisher.md) skickar värdena för dessa noder till Connected Factory. Det här omfattar:

* Aktuell driftstatus, till exempel aktuell strömförbrukning.
* Produktionsinformation såsom antalet producerade produkter.

Du kan använda instrumentpanelen för att öka kontosfabrikens topologi från en global vy ner till en stationsnivåvy. Instrumentpanelen Ansluten fabrik gör det möjligt:

* Visualisering av OEE- och KPI-figurer för varje lager i topologin.
* Visualisering av aktuella värden för OPC UA-noder på stationerna.
* Aggregeringen av OEE- och KPI-siffrorna från stationsnivå till global nivå.
* Visualisering av aviseringar och åtgärder som ska utföras om värden når specifika tröskelvärden.

## <a name="connected-factory-topology"></a>Ansluten fabrikstopologi

Topologin för fabriker, produktionslinjer och stationer är hierarkisk:

* Den globala nivån har fabriksnoder som barn.
* Fabrikerna har produktionslinjenoder som barn.
* Produktionslinjerna har stationsnoder som underordnade.
* Stationerna (OPC UA-servrar) har OPC UA-noder som underordnade.

Varje nod i topologin har en gemensam uppsättning egenskaper som definierar:

* En unik identifierare för topologinoden.
* Ett namn.
* En beskrivning.
* En bild.
* Barnen till topologinoden.
* Lägsta värden, mål- och maximivärden för OEE- och KPI-siffror och varningsåtgärder som ska utföras.

## <a name="topology-configuration-file"></a>Konfigurationsfil för topologi

Om du vill konfigurera egenskaperna i föregående avsnitt använder lösningen Ansluten fabrik en konfigurationsfil som heter [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Du hittar den här filen i `WebApp/Contoso/Topology` lösningens källkod i mappen.

Följande kodavsnitt visar en disposition `ContosoTopologyDescription.json` av konfigurationsfilen:

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

De vanligaste `<global_configuration>`egenskaperna `<factory_configuration>` `<production_line_configuration>`för `<station_configuration>` , , och är:

* **Namn** (typsträng)

  Definierar ett beskrivande namn, som bara ska vara ett ord för topologinoden att visa på instrumentpanelen.

* **Beskrivning** (typsträng)

  Beskriver topologinoden mer i detalj.

* **Bild** (typsträng)

  Sökvägen till en bild i WebApp-lösningen som ska visas när information om topologinoden visas på instrumentpanelen.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, `<performance_definition>` **Kpi1**, **KPI2** (typ)

  Dessa egenskaper definierar minimala värden, målvärden och högsta värden för den operativa figur som används för att generera aviseringar. Dessa egenskaper definierar också de åtgärder som ska köras om en avisering identifieras.

Föremålen `<production_line_configuration>` och objekten `<factory_configuration>` har en egenskap:

* **Guid** (typsträng)

  Identifierar noden topologi unikt.

`<factory_configuration>`har en fastighet:

* **Plats** (typ) `<location_definition>`

  Anger var fabriken finns.

`<station_configuration>`har egenskaper:

* **OpcUri** (typsträng)

  Den här egenskapen måste anges till OPC UA Application URI på OPC UA-servern.
  Eftersom den måste vara globalt unik enligt OPC UA-specifikationen används den här egenskapen för att identifiera stationstopologinoden.

* **OpcNodes**, som är en matris med OPC UA-noder (typ) `<opc_node_description>`

`<location_definition>`har egenskaper:

* **Ort** (typsträng)

  Namn på ort närmast platsen

* **Land** (typsträng)

  Land på platsen

* **Latitud** (skriv dubbel)

  Platsens latitud

* **Longitud** (typ dubbel)

  Longitud av läget

`<performance_definition>`har egenskaper:

* **Minimum** (typ dubbel)

  Lägre tröskelvärde som värdet kan nå. Om det aktuella värdet ligger under det här tröskelvärdet genereras en avisering.

* **Mål** (typ dubbel)

  Perfekt målvärde.

* **Maximum** (typ dubbel)

  Övre tröskelvärdet som värdet kan nå. Om det aktuella värdet ligger över det här tröskelvärdet genereras en avisering.

* **MinimumAlertActions** (typ) `<alert_action>`

  Definierar uppsättningen åtgärder som kan vidtas som svar på en minsta avisering.

* **MaximumAlertActions** (typ) `<alert_action>`

  Definierar uppsättningen åtgärder som kan vidtas som svar på en maximal avisering.

`<alert_action`> har egenskaper:

* **Typ** (typsträng)

  Typ av varningsåtgärd. Följande typer är kända:

  * **AcknowledgeAlert**: status för aviseringen bör ändras till bekräftad.
  * **CloseAlert**: alla äldre aviseringar av samma typ ska inte längre visas på instrumentpanelen.
  * **CallOpcMethod**: en OPC UA-metod bör anropas.
  * **OpenWebPage**: ett webbläsarfönster bör öppnas med ytterligare kontextuell information.

* **Beskrivning** (typsträng)

  Beskrivning av åtgärden som visas på instrumentpanelen.

* **Parameter** (typsträng)

  Parametrar som krävs för att utföra åtgärden. Värdet beror på åtgärdstypen.

  * **AcknowledgeAlert**: ingen parameter krävs.
  * **CloseAlert**: ingen parameter krävs.
  * **CallOpcMethod**: nodinformation och parametrar för OPC UA-metoden för att anropa i formatet "NodeId of parent node, NodeId of method to call, URI of the OPC UA server".
  * **OpenWebPage**: URL:en som ska visas i webbläsarfönstret.

`<opc_node_description>`innehåller information om OPC UA-noder på en station (OPC UA-server). Noder som inte representerar några befintliga OPC UA-noder, men som används som lagring i beräkningslogiken i Connected Factory är också giltiga. Den har följande egenskaper:

* **NodeId** (typsträng)

  Adressen till OPC UA-noden i stationens (OPC UA-serverns) adressutrymme. Syntax måste anges som anges i OPC UA-specifikationen för en NodeId.

* **SymbolicName** (typsträng)

  Namn som ska visas på instrumentpanelen när värdet för den här OPC UA-noden visas.

* **Relevans** (matris av typsträng)

  Anger för vilken beräkning av OEE eller KPI OPC UA-nodvärdet är relevant. Varje matriselement kan vara ett av följande värden:

  * **OeeAvailability_Running**: värdet är relevant för beräkning av OEE-tillgänglighet.
  * **OeeAvailability_Fault**: värdet är relevant för beräkning av OEE-tillgänglighet.
  * **OeePerformance_Ideal:** Värdet är relevant för beräkning av OEE Performance och är vanligtvis ett konstant värde.
  * **OeePerformance_Actual**: värdet är relevant för beräkning av OEE Performance.
  * **OeeQuality_Good:** Värdet är relevant för beräkning av OEE-kvalitet.
  * **OeeQuality_Bad:** Värdet är relevant för beräkning av OEE-kvalitet.
  * **KPI1**: Värdet är relevant för beräkning av KPI1.
  * **KPI2**: värdet är relevant för beräkning av KPI2.

* **OpCode** (typsträng)

  Anger hur värdet för OPC UA-noden hanteras i Time Series Insight-frågor och OEE/KPI-beräkningar. Varje Time Series Insight-fråga är inriktad på en viss tidsrymd, som är en parameter för frågan och ger ett resultat. OpCode styr hur resultatet beräknas och kan vara ett av följande värden:

  * **Diff**: skillnaden mellan det sista och det första värdet i tidsspannet.
  * **Medel :** medelvärdet av alla värden i tidsspannet.
  * **Summa**: summan av alla värden i tidsspannet.
  * **Sista**: används för närvarande inte.
  * **Antal**: antalet värden i tidsspannet.
  * **Max**: det maximala värdet i tidsspannet.
  * **Min**: det minimala värdet i tidsspannet.
  * **Const**: resultatet är det värde som anges av egenskapen ConstValue.
  * **SubMaxMin**: skillnaden mellan det maximala och det minimala värdet.
  * **Tidsspann:** tidsspannet.

* **Enheter** (typsträng)

  Definierar en enhet med värdet för visning i instrumentpanelen.

* **Synlig** (skriv boolesk)

  Styr om värdet ska visas på instrumentpanelen.

* **ConstValue** (typ dubbel)

  Om **OpCode** är **Const**är den här egenskapen nodens värde.

* **Minimum** (typ dubbel)

  Om det aktuella värdet sjunker under det här värdet genereras en minsta avisering.

* **Maximum** (typ dubbel)

  Om det aktuella värdet höjer över det här värdet genereras en maximal avisering.

* **MinimumAlertActions** (typ) `<alert_action>`

  Definierar uppsättningen åtgärder som kan vidtas som svar på en minsta avisering.

* **MaximumAlertActions** (typ) `<alert_action>`

  Definierar uppsättningen åtgärder som kan vidtas som svar på en maximal avisering.

På stationsnivå visas även **Simuleringsobjekt.** Dessa objekt används endast för att konfigurera simuleringen ansluten fabrik och bör inte användas för att konfigurera en riktig topologi.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Så här används konfigurationsdata vid körning

Alla egenskaper som används i konfigurationsfilen kan grupperas i olika kategorier beroende på hur de används. Dessa kategorier är:

### <a name="visual-appearance"></a>Utseende

Egenskaper i den här kategorin definierar det visuella utseendet på instrumentpanelen ansluten fabrik. Exempel på rekommendationer:

* Namn
* Beskrivning
* Bild
* Location
* Enheter
* Synliga

### <a name="internal-topology-tree-addressing"></a>Adressering av intern topologi träd

WebApp underhåller en intern dataordlista som innehåller information om alla topologinoder. Egenskaperna **Guid** och **OpcUri** används som nycklar för att komma åt den här ordlistan och måste vara unika.

### <a name="oeekpi-computation"></a>OEE/KPI-beräkning

OEE/KPI-siffrorna för simuleringen ansluten fabrik paras in av:

* De OPC UA-nodvärden som ska inkluderas i beräkningen.
* Hur figuren beräknas från telemetrivärdena.

Connected Factory använder OEE-formlerna [http://www.oeefoundation.org](http://www.oeefoundation.org)som publicerats av .

OPC UA-nodobjekt i stationer aktiverar taggning för användning i OEE/KPI-beräkning. Egenskapen **Relevans** anger för vilken OEE/KPI-siffra OPC UA-nodvärdet ska användas. Egenskapen **OpCode** definierar hur värdet ingår i beräkningen.

### <a name="alert-handling"></a>Varningshantering

Connected Factory stöder en enkel minsta/högsta tröskel-baserad varningsgenereringsmekanism. Det finns ett antal fördefinierade åtgärder som du kan konfigurera som svar på dessa aviseringar. Följande egenskaper styr den här mekanismen:

* Maximal
* Minimum
* Maximalaåtgärder
* Minsta åtgärder

## <a name="correlating-to-telemetry-data"></a>Korrelera till telemetridata

För vissa åtgärder, till exempel visualisera det senaste värdet eller skapa Time Series Insight-frågor, behöver WebApp ett adresseringsschema för de intas telemetridata. Telemetrin som skickas till Connected Factory måste också lagras i interna datastrukturer. De två egenskaper som möjliggör dessa åtgärder finns på station (OPC UA-server) och OPC UA-nodnivå:

* **OpcUri (opcuri)**

  Identifierar (globalt unik) den OPC UA-server som telemetrin kommer ifrån. I de intämmade meddelandena skickas den här egenskapen som **ApplicationUri**.

* **NodeId**

  Identifierar nodvärdet på OPC UA-servern. Egenskapens format måste vara det som anges i OPC UA-specifikationen. I de intjänade meddelandena skickas den här egenskapen som **NodeId**.

Mer information om hur telemetridata används till Connected Factory finns i [Vad är OPC Publisher.](overview-opc-publisher.md)

## <a name="example-how-kpi1-is-calculated"></a>Exempel: Hur KPI1 beräknas

Konfigurationen `ContosoTopologyDescription.json` i filen styr hur OEE/KPI-siffror beräknas. Följande exempel visar hur egenskaper i den här filen styr beräkningen av KPI1.

I Connected Factory KPI1 används för att mäta antalet framgångsrikt tillverkade produkter under den senaste timmen. Varje station (OPC UA-server) i simuleringen Ansluten`NodeId: "ns=2;i=385"`fabrik tillhandahåller en OPC UA-nod ( ), som tillhandahåller telemetrin för att beräkna den här KPI:n.

Konfigurationen för den här OPC UA-noden ser ut som följande utdrag:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Den här konfigurationen möjliggör frågor om telemetrivärdena för den här noden med hjälp av Time Series Insights. Frågan Time Series Insights hämtar:

* Antalet värden.
* Det minimala värdet.
* Det maximala värdet.
* Medelvärdet av alla värden.
* Summan av alla värden för alla unika **OpcUri** (**ApplicationUri**), **NodeId** par i en viss tidsperiod.

En egenskap hos **nodvärdet NumberOfManufactureredProducts** är att det bara ökar. För att beräkna antalet produkter som tillverkas i tidsspannet använder Connected Factory **OpCode** **SubMaxMin**. Beräkningen hämtar minimivärdet i början av tidsspannet och det maximala värdet i slutet av tidsintervallet.

**OpCode** i konfigurationen konfigurerar beräkningslogiken för att beräkna resultatet av skillnaden mellan högsta och lägsta värde. Dessa resultat ackumuleras sedan nedifrån och upp till rotnivån (global) och visas i instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

Ett förslag på nästa steg är att lära sig hur du [anpassar lösningen ansluten fabrik](iot-accelerators-connected-factory-customize.md).
