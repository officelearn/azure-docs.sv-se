---
title: Förstå kompatibilitetsnivån för Azure Stream Analytics-jobb
description: Lär dig hur du ställer in en kompatibilitetsnivå för Azure Stream Analytics-jobb och större ändringar i senaste kompatibilitetsnivå
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 6fb93152263d253de983b17d25f02f4c68a172fd
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361401"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Kompatibilitetsnivån för Azure Stream Analytics-jobb
 
Kompatibilitetsnivån refererar till release-specifika funktioner för ett Azure Stream Analytics-tjänsten. Azure Stream Analytics är en hanterad tjänst med regelbundna funktionsuppdateringar och prestanda. Vanligtvis blir uppdateringar automatiskt tillgängliga för slutanvändare. Vissa nya funktioner kan dock medföra större ändringar sådana som-ändring i beteendet för ett befintligt jobb, ändra i de processer som förbrukar data från dessa jobb osv. En kompatibilitetsnivå som används för att representera en större ändring som introducerades i Stream Analytics. Större ändringar introduceras alltid med en ny kompatibilitetsnivå. 

Kompatibilitetsnivån ser till att befintliga jobb köras utan några fel. När du skapar ett nytt Stream Analytics-jobb är en bra idé att skapa den med hjälp av senaste kompatibilitetsnivå. 
 
## <a name="set-a-compatibility-level"></a>Ange en kompatibilitetsnivå 

Kompatibilitetsnivån styr beteende under körning av ett stream analytics-jobb. Du kan ange kompatibilitetsnivån för ett Stream Analytics-jobb med hjälp av portalen eller med hjälp av den [skapa jobbet REST API-anrop](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Azure Stream Analytics stöder för närvarande två kompatibilitet nivåer – ”1.0” och ”1.1”. Kompatibilitetsnivå är som standard ”1.0”, som introducerades vid den allmänna tillgängligheten av Azure Stream Analytics. Om du vill uppdatera standardvärdet, navigera till ditt befintliga Stream Analytics-jobb > Välj den **kompatibilitetsnivå** alternativet i **konfigurera** och ändra värdet. 

Se till att du stoppa jobbet innan du uppdaterar kompatibilitetsnivå. Du kan inte uppdatera kompatibilitetsnivå om jobbet är i körningstillstånd. 

![Stream Analytics kompatibilitetsnivå i Azure-portalen](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

 
När du uppdaterar kompatibilitetsnivå, verifierar T-SQL-kompilatorn jobbet med den syntax som motsvarar den valda kompatibilitetsnivån. 

## <a name="major-changes-in-the-latest-compatibility-level-12"></a>Större ändringar i den senaste kompatibilitetsnivån (1.2)

Följande viktiga ändringar har introducerats i kompatibilitetsnivå 1.2:

### <a name="geospatial-functions"></a>Geospatiala funktioner 

**Tidigare versioner:** Azure Stream Analytics används geografi beräkningar.

**aktuell version:** Azure Stream Analytics kan du beräkna geometriska planerade geo-koordinater. Det finns ingen ändring i signaturen för geospatiala funktioner. Deras semantik är dock något annorlunda, vilket gör att mer exakt beräkning än innan.

Azure Stream Analytics har stöd för indexering av geospatiala referens för data. Referensdata som innehåller geospatiala element kan indexeras för en snabbare join-beräkning.

Uppdaterade geospatiala funktioner ger dig fullständig behovet av välkända Text (WELL-KNOWN) geospatiala format. Du kan ange andra geospatiala-komponenter som tidigare inte stöds med GeoJson.

Mer information finns i [uppdaterar till geospatiala funktioner i Azure Stream Analytics – moln- och IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Parallell frågekörning för indatakällor med flera partitioner 

**Tidigare versioner:** Azure Stream Analytics-frågor krävs användning av PARTITION BY-satsen att parallellisera frågebearbetning över Indatakällan partitioner.

**aktuell version:** Om frågans logik kan parallelliseras över Indatakällan partitioner, Azure Stream Analytics skapas separat fråga instanser och beräkningar körs parallellt.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Inbyggd grupp API-integration med CosmosDB-utdata

**Tidigare versioner:** Beteendet upsert har *insert- eller merge-*.

**aktuell version:** Inbyggd grupp API-integration med CosmosDB-utdata maximerar dataflödet och hanterar effektivt begränsningsbegäranden.

Beteendet upsert är *infoga eller ersätta*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset när du skriver SQL-utdata

**Tidigare versioner:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) typer justerades till UTC.

**aktuell version:** DateTimeOffset justeras inte längre.

### <a name="strict-validation-of-prefix-of-functions"></a>Strikt verifiering av prefixet för funktioner

**Tidigare versioner:** Det fanns inga strikt verifiering av funktionen prefix.

**aktuell version:** Azure Stream Analytics har en strikt verifiering av funktionen prefix. Att lägga till ett prefix till en inbyggd funktion orsakar ett fel. Till exempel`myprefix.ABS(…)` stöds inte.

Att lägga till ett prefix till inbyggda aggregeringar resulterar också i fel. Till exempel `myprefix.SUM(…)` stöds inte.

Med prefixet ”system” för alla användardefinierade funktioner resulterar i fel.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Tillåt inte matris och objekt som nyckelegenskaper i Cosmos DB-utdataadapter

**Tidigare versioner:** Matris och objekt typer stöddes som en nyckelegenskap.

**aktuell version:** Matris och objekt typer stöds inte längre som en nyckelegenskap.


## <a name="next-steps"></a>Nästa steg
* [Felsöka Azure Stream Analytics-indata](stream-analytics-troubleshoot-input.md)
* [Stream Analytics Resource health](stream-analytics-resource-health.md)
