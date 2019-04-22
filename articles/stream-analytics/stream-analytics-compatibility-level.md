---
title: Förstå kompatibilitetsnivån för Azure Stream Analytics-jobb
description: Lär dig hur du ställer in en kompatibilitetsnivå för Azure Stream Analytics-jobb och större ändringar i senaste kompatibilitetsnivå
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: b5c833798f8533e7c6fbe3595a726ac6ce56e2d2
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682822"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Kompatibilitetsnivån för Azure Stream Analytics-jobb

Den här artikeln beskriver kompatibilitetsalternativet nivå i Azure Stream Analytics. Stream Analytics är en hanterad tjänst med regelbundna funktionsuppdateringar och prestanda. De flesta av tjänstuppdateringar körningar blir automatiskt tillgängliga för slutanvändare. 

Men vissa nya funktioner i tjänsten kan införa en större ändring, t.ex en ändring i beteendet för ett befintligt jobb eller en ändring i hur data används i jobb som körs. Du kan behålla din befintliga Stream Analytics-jobb som körs utan större ändringar genom att lämna inställningen sänkt kompatibilitetsnivå. När du är redo för de senaste körningsbeteenden, du kan anmäla sig genom att höja kompatibilitetsnivå. 

## <a name="choose-a-compatibility-level"></a>Välj en kompatibilitetsnivå

Kompatibilitetsnivån styr beteende under körning av ett stream analytics-jobb. 

Azure Stream Analytics stöder för närvarande tre kompatibilitetsnivåer:

* 1.0 - Standardnivå
* 1.1 - den aktuella versionen beteende
* 1.2 (förhandsversion) – senaste beteende med de senaste förbättringarna av utvärdering

Ursprungliga 1.0 kompatibilitetsnivå introducerades vid den allmänna tillgängligheten av Azure Stream Analytics flera år sedan.

När du skapar ett nytt Stream Analytics-jobb är en bra idé att skapa den med hjälp av senaste kompatibilitetsnivå. Starta utformningen jobbet att förlita sig på de senaste beteenden att undvika extra ändrings- och komplexiteten vid ett senare tillfälle.

## <a name="set-the-compatibility-level"></a>Ange kompatibilitetsnivån

Du kan ange kompatibilitetsnivån för ett Stream Analytics-jobb i Azure portal eller med hjälp av den [skapa jobbet REST API-anrop](/rest/api/streamanalytics/stream-analytics-job).

Så här uppdaterar kompatibilitetsnivån för jobbet i Azure portal:

1. Använd den [Azure-portalen](https://portal.azure.com) att hitta till ditt Stream Analytics-jobb.
2. **Stoppa** jobbet innan du uppdaterar kompatibilitetsnivå. Du kan inte uppdatera kompatibilitetsnivå om jobbet är i körningstillstånd.
3. Under den **konfigurera** väljer **kompatibilitetsnivå**.
4. Välj kompatibilitet på värdet som du vill.
5. Välj **spara** längst ned på sidan.

![Stream Analytics kompatibilitetsnivå i Azure-portalen](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

När du uppdaterar kompatibilitetsnivå, verifierar T-SQL-kompilatorn jobbet med den syntax som motsvarar den valda kompatibilitetsnivån.

## <a name="compatibility-level-12"></a>Kompatibilitetsnivå 1.2

Följande viktiga ändringar har introducerats i kompatibilitetsnivå 1.2:

### <a name="geospatial-functions"></a>Geospatiala funktioner

**Föregående nivåer:** Azure Stream Analytics används geografi beräkningar.

**1.2 nivå:** Azure Stream Analytics kan du beräkna geometriska planerade geo-koordinater. Det finns ingen ändring i signaturen för geospatiala funktioner. Deras semantik är dock något annorlunda, vilket gör att mer exakt beräkning än innan.

Azure Stream Analytics har stöd för indexering av geospatiala referens för data. Referensdata som innehåller geospatiala element kan indexeras för en snabbare join-beräkning.

Uppdaterade geospatiala funktioner ger dig fullständig behovet av välkända Text (WELL-KNOWN) geospatiala format. Du kan ange andra geospatiala-komponenter som tidigare inte stöds med GeoJson.

Mer information finns i [uppdaterar till geospatiala funktioner i Azure Stream Analytics – moln- och IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Parallell frågekörning för indatakällor med flera partitioner

**Föregående nivåer:** Azure Stream Analytics-frågor krävs användning av PARTITION BY-satsen att parallellisera frågebearbetning över Indatakällan partitioner.

**1.2 nivå:** Om frågans logik kan parallelliseras över Indatakällan partitioner, Azure Stream Analytics skapas separat fråga instanser och beräkningar körs parallellt.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Inbyggd grupp API-integration med CosmosDB-utdata

**Föregående nivåer:** Beteendet upsert har *insert- eller merge-*.

**1.2 nivå:** Inbyggd grupp API-integration med CosmosDB-utdata maximerar dataflödet och hanterar effektivt begränsningsbegäranden.

Beteendet upsert är *infoga eller ersätta*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset när du skriver SQL-utdata

**Föregående nivåer:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) typer justerades till UTC.

**1.2 nivå:** DateTimeOffset justeras inte längre.

### <a name="strict-validation-of-prefix-of-functions"></a>Strikt verifiering av prefixet för funktioner

**Föregående nivåer:** Det fanns inga strikt verifiering av funktionen prefix.

**1.2 nivå:** Azure Stream Analytics har en strikt verifiering av funktionen prefix. Att lägga till ett prefix till en inbyggd funktion orsakar ett fel. Till exempel`myprefix.ABS(…)` stöds inte.

Att lägga till ett prefix till inbyggda aggregeringar resulterar också i fel. Till exempel `myprefix.SUM(…)` stöds inte.

Med prefixet ”system” för alla användardefinierade funktioner resulterar i fel.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Tillåt inte matris och objekt som nyckelegenskaper i Cosmos DB-utdataadapter

**Föregående nivåer:** Matris och objekt typer stöddes som en nyckelegenskap.

**1.2 nivå:** Matris och objekt typer stöds inte längre som en nyckelegenskap.

## <a name="compatibility-level-11"></a>Kompatibilitetsnivå 1.1

Följande viktiga ändringar har introducerats i kompatibilitetsnivå 1.1:

### <a name="service-bus-xml-format"></a>Service Bus XML-format

**1.0 nivå:** Azure Stream Analytics används DataContractSerializer, så att innehållet i meddelandet ingår XML-taggar. Exempel:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 nivå:** Meddelandeinnehåll innehåller stream direkt med ingen ytterligare taggar. Exempel: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Bevara skiftlägeskänslighet för fältnamn

**1.0 nivå:** Fältnamn ändrades till gemener när bearbetas av Azure Stream Analytics-motorn.

**1.1 nivå:** skiftlägeskänslighet bevaras för fältnamn när de bearbetas av Azure Stream Analytics-motorn.

> [!NOTE]
> Bevara skiftlägeskänslighet är ännu inte tillgängligt för Stream Analytics-jobb med hjälp av Edge-miljö. Därför kan konverteras alla fältnamn till gemener om ditt jobb finns i Microsoft Edge.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1.0 nivå:** CREATE TABLE-kommando inte att filtrera händelser med NaN (inte ett tal. Till exempel oändligt, -Infinity) i en kolumn för FLYTTAL anger eftersom de inte uppfyller det dokumenterade intervallet för dessa siffror.

**1.1 nivå:** Skapa tabell kan du ange ett starkt schema. Stream Analytics-motorn validerar att informationen som överensstämmer med det här schemat. Med den här modellen kan kommandot Filtrera händelser med NaN-värden.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Inaktivera automatisk upcast för datetime-strängar i JSON

**1.0 nivå:** JSON-parsern skulle automatiskt ”uppåt” strängvärden med datum / / tidszonsinformation för DateTime-typ och sedan konvertera den till UTC. Detta resulterade i att förlora Tidszonsinformationen.

**1.1 nivå:** Det finns inga fler automatiskt ”uppåt” av strängvärden med datum / / tidszonsinformation för DateTime-typen. Därför kan sparas informationen om tidszonen.

## <a name="next-steps"></a>Nästa steg

* [Felsöka Azure Stream Analytics-indata](stream-analytics-troubleshoot-input.md)
* [Stream Analytics Resource health](stream-analytics-resource-health.md)
