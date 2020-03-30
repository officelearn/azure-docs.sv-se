---
title: Kompatibilitetsnivåer för Azure Stream Analytics
description: Lär dig hur du anger en kompatibilitetsnivå för ett Azure Stream Analytics-jobb och större ändringar på den senaste kompatibilitetsnivån
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 8f22b1ff97826dc318794aca58973b1276e74209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087863"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Kompatibilitetsnivå för Azure Stream Analytics-jobb

I den här artikeln beskrivs alternativet kompatibilitetsnivå i Azure Stream Analytics. Stream Analytics är en hanterad tjänst med regelbundna funktionsuppdateringar och prestandaförbättringar. De flesta av tjänstens runtimes-uppdateringar görs automatiskt tillgängliga för slutanvändare. 

Vissa nya funktioner i tjänsten kan dock innebära en större förändring, till exempel en ändring i beteendet för ett befintligt jobb eller en ändring av hur data förbrukas i jobb som körs. Du kan hålla dina befintliga Stream Analytics-jobb igång utan större ändringar genom att låta inställningen för kompatibilitetsnivå sänkas. När du är redo för de senaste körningsbeteendena kan du anmäla dig genom att höja kompatibilitetsnivån. 

## <a name="choose-a-compatibility-level"></a>Välj en kompatibilitetsnivå

Kompatibilitetsnivå styr körningsbeteendet för ett dataflödesanalysjobb. 

Azure Stream Analytics stöder för närvarande tre kompatibilitetsnivåer:

* 1.0 - Original kompatibilitetsnivå, som introducerades under allmän tillgänglighet för Azure Stream Analytics för flera år sedan.
* 1.1 - Föregående uppförande
* 1.2 - Nyaste beteende med de senaste förbättringarna

När du skapar ett nytt Stream Analytics-jobb är det bäst att skapa det med hjälp av den senaste kompatibilitetsnivån. Starta jobbdesignen för att förlita dig på de senaste beteendena, för att undvika ytterligare förändringar och komplexitet senare.

## <a name="set-the-compatibility-level"></a>Ange kompatibilitetsnivå

Du kan ange kompatibilitetsnivån för ett Stream Analytics-jobb i Azure-portalen eller genom att använda [problemets REST API-anrop](/rest/api/streamanalytics/stream-analytics-job).

Så här uppdaterar du kompatibilitetsnivån för jobbet i Azure-portalen:

1. Använd [Azure-portalen](https://portal.azure.com) för att hitta till ditt Stream Analytics-jobb.
2. **Stoppa** jobbet innan du uppdaterar kompatibilitetsnivån. Du kan inte uppdatera kompatibilitetsnivån om jobbet körs.
3. Välj **Kompatibilitetsnivå under**rubriken **Konfigurera** .
4. Välj önskat kompatibilitetsnivåvärde.
5. Välj **Spara** längst ned på sidan.

![Stream Analytics-kompatibilitetsnivå i Azure-portalen](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

När du uppdaterar kompatibilitetsnivån validerar T-kompilatorn jobbet med den syntax som motsvarar den valda kompatibilitetsnivån.

## <a name="compatibility-level-12"></a>Kompatibilitetsnivå 1.2

Följande större ändringar införs på kompatibilitetsnivå 1.2:

###  <a name="amqp-messaging-protocol"></a>AMQP-meddelandeprotokoll

**1.2-nivå:** Azure Stream Analytics använder [AMQP-meddelandeprotokoll (Advanced Message Queueing Protocol)](../service-bus-messaging/service-bus-amqp-overview.md) för att skriva till servicebussköer och informationsavsnitt. MED AMQP kan du skapa hybridprogram som inte är plattformarna och hybridprogram med ett öppet standardprotokoll.

### <a name="geospatial-functions"></a>Geospatiala funktioner

**Tidigare nivåer:** Azure Stream Analytics använde geografiberäkningar.

**1.2 nivå:** Med Azure Stream Analytics kan du beräkna geometriska projicerade geokoordinater. Det finns ingen förändring i signaturen för geospatiala funktioner. Men deras semantik är något annorlunda, vilket möjliggör mer exakt beräkning än tidigare.

Azure Stream Analytics stöder geospatial referensdataindexering. Referensdata som innehåller geospatiala element kan indexeras för en snabbare kopplingsberäkning.

De uppdaterade geospatiala funktionerna ger full uttrycksfullhet av välkänt textformat (WKT). Du kan ange andra geospatiala komponenter som inte tidigare stöds med GeoJson.

Mer information finns [i Uppdateringar av geospatiala funktioner i Azure Stream Analytics – Cloud och IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Parallell frågekörning för indatakällor med flera partitioner

**Tidigare nivåer:** Azure Stream Analytics-frågor krävde användning av PARTITION BY-satsen för att parallellisera frågebearbetning över indatakällpartitioner.

**1.2 nivå:** Om frågelogiken kan parallelliseras mellan indatakällpartitioner skapar Azure Stream Analytics separata frågeinstanser och kör beräkningar parallellt.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Inbyggd Mass-API-integrering med CosmosDB-utdata

**Tidigare nivåer:** Det senaste beteendet var *infoga eller slå samman*.

**1.2 nivå:** Native Bulk API-integrering med CosmosDB-utdata maximerar dataflödet och hanterar effektivt begränsningsbegäranden. Mer information finns [på sidan Azure Stream Analytics-utdata till Azure Cosmos DB](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12).

Det senaste beteendet *infogas eller ersätts*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset när du skriver till SQL-utdata

**Tidigare nivåer:** [DateTimeOffset-typerna](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) justerades till UTC.

**1.2 nivå:** DateTimeOffset justeras inte längre.

### <a name="long-when-writing-to-sql-output"></a>Lång när du skriver till SQL-utdata

**Tidigare nivåer:** Värdena trunkerades baserat på måltypen.

**1.2 nivå:** Värden som inte passar in i måltypen hanteras enligt principen för utdatafel.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>Post- och matrisseriering när du skriver till SQL-utdata

**Tidigare nivåer:** Poster skrevs som "Post" och matriser skrevs som "Array".

**1.2 nivå:** Poster och matriser serialiseras i JSON-format.

### <a name="strict-validation-of-prefix-of-functions"></a>Strikt validering av prefix av funktioner

**Tidigare nivåer:** Det fanns ingen strikt validering av funktionsprefix.

**1.2 nivå:** Azure Stream Analytics har en strikt validering av funktionsprefix. Om du lägger till ett prefix i en inbyggd funktion uppstår ett fel. Stöds till`myprefix.ABS(…)` exempel inte.

Om du lägger till ett prefix i inbyggda aggregat uppstår också ett fel. Stöds till `myprefix.SUM(…)` exempel inte.

Om du använder prefixet "system" för användardefinierade funktioner uppstår fel.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Tillåt inte matris och objekt som nyckelegenskaper i Cosmos DB-utdatakort

**Tidigare nivåer:** Matris- och objekttyper stöddes som nyckelegenskap.

**1.2 nivå:** Matris- och objekttyper stöds inte längre som nyckelegenskap.

## <a name="compatibility-level-11"></a>Kompatibilitetsnivå 1.1

Följande större ändringar införs på kompatibilitetsnivå 1.1:

### <a name="service-bus-xml-format"></a>XML-format för servicebuss

**1,0 nivå:** Azure Stream Analytics använde DataContractSerializer, så meddelandeinnehållet inkluderade XML-taggar. Ett exempel:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 nivå:** Meddelandeinnehållet innehåller strömmen direkt utan ytterligare taggar. Exempel: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Beständig fallkänslighet för fältnamn

**1,0 nivå:** Fältnamn ändrades till gemener när de bearbetades av Azure Stream Analytics-motorn.

**1.1-nivå:** skiftlägeskänslighet sparas för fältnamn när de bearbetas av Azure Stream Analytics-motorn.

> [!NOTE]
> Beständig fallkänslighet är ännu inte tillgänglig för Stream Analytic-jobb som finns med hjälp av Edge-miljön. Därför konverteras alla fältnamn till gemener om jobbet finns på Edge.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1,0 nivå:** KOMMANDOT CREATE TABLE filtrerade inte händelser med NaN (Not-a-Number. Oändlighet, -Infinity) i kolumntypen FLOAT eftersom de inte har det dokumenterade intervallet för dessa nummer.

**1.1 nivå:** SKAPA TABELL kan du ange ett starkt schema. Stream Analytics-motorn verifierar att data överensstämmer med det här schemat. Med den här modellen kan kommandot filtrera händelser med NaN-värden.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Inaktivera automatisk uppdatering för datetime-strängar i JSON

**1,0 nivå:** JSON-tolken skulle automatiskt uppdat strängvärden med datum-/tid/zoninformation till DateTime-typ och konvertera den sedan till UTC. Detta resulterade i att tidszonsinformationen förlorades.

**1.1 nivå:** Strängvärdena uppdateras inte mer automatiskt med datum-/tids-/zoninformation till DateTime-typen. Som ett resultat sparas tidszonsinformation.

## <a name="next-steps"></a>Nästa steg

* [Felsöka Azure Stream Analytics-indata](stream-analytics-troubleshoot-input.md)
* [Hälsa för Resurs för Stream Analytics](stream-analytics-resource-health.md)
