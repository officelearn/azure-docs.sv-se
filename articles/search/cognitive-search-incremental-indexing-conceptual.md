---
title: Inkrementell anrikning (förhandsgranskning)
titleSuffix: Azure Cognitive Search
description: Cachelagring av mellanliggande innehåll och inkrementella ändringar från AI-anrikningspipeline i Azure Storage för att bevara investeringar i befintliga bearbetade dokument. Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 09003c26ead9108d07ae339fcf64235c246474a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024151"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Introduktion till inkrementell anrikning och cachelagring i Azure Cognitive Search

> [!IMPORTANT] 
> Inkrementell berikning är för närvarande i offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API-versionen 2019-05-06-Preview](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för portalen eller .NET SDK.

Inkrementell berikning lägger caching och statefulness till en anrikning pipeline, bevara din investering i befintlig produktion, samtidigt som du ändrar endast de dokument som påverkas av särskilda ändringar. Detta bevarar inte bara din monetära investering i bearbetning (i synnerhet OCR och bildbehandling) men det gör också för ett effektivare system. När strukturer och innehåll cachelagras kan en indexerare bestämma vilka kunskaper som har ändrats och bara köra de som har ändrats, samt eventuella underordnade kunskaper i nedströms. 

## <a name="indexer-cache"></a>Indexer cache

Inkrementell anrikning lägger till en cache i anrikningspipelinen. Indexeraren cachelagrar resultaten från dokumentsprickning plus utdata för varje färdighet för varje dokument. När en kompetens uppdateras körs endast de ändrade, eller nedströms, färdigheterna. De uppdaterade resultaten skrivs till cacheminnet och dokumentet uppdateras i sökindexet eller kunskapsarkivet.

Fysiskt lagras cachen i en blob-behållare i ditt Azure Storage-konto. Cachen använder också tabelllagring för en intern post med bearbetningsuppdateringar. Alla index i en söktjänst kan dela samma lagringskonto för indexerarcachen. Varje indexerare tilldelas en unik och oföränderlig cacheidentifierare till behållaren som den använder.

## <a name="cache-configuration"></a>Konfiguration av cache

Du måste ange egenskapen `cache` på indexeraren för att börja dra nytta av inkrementell berikning. Följande exempel illustrerar en indexerare med cachelagring aktiverad. Specifika delar av den här konfigurationen beskrivs i följande avsnitt. Mer information finns i [Ställ inkrementell berikning](search-howto-incremental-index.md).

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

Om du anger den här egenskapen på en befintlig indexerare måste du återställa och köra indexeraren igen, vilket resulterar i att alla dokument i datakällan bearbetas igen. Det här steget är nödvändigt för att eliminera alla dokument som berikats av tidigare versioner av kompetensen. 

## <a name="cache-management"></a>Hantering av cache

Cacheminnets livscykel hanteras av indexeraren. Om `cache` egenskapen på indexeraren är inställd på null eller om anslutningssträngen ändras tas den befintliga cachen bort på nästa indexeringskörning. Cachelivscykeln är också knuten till indexerarens livscykel. Om en indexerare tas bort tas även den associerade cachen bort.

Även om inkrementell anrikning är utformad för att identifiera och svara på ändringar utan att någon åtgärd från din sida, finns det parametrar som du kan använda för att åsidosätta standardbeteenden:

+ Prioritera nya dokument
+ Kringgå kompetenskontroller
+ Kringgå datakällkontroller
+ Tvinga kompetens utvärdering

### <a name="prioritize-new-documents"></a>Prioritera nya dokument

Ange `enableReprocessing` egenskapen för att styra bearbetningen över inkommande dokument som redan finns representerad i cacheminnet. När `true` (standard) bearbetas dokument som redan finns i cacheminnet om när du kör indexeraren igen, förutsatt att din färdighetsuppdatering påverkar det dokumentet. 

När `false`bearbetas inte befintliga dokument om, vilket effektivt prioriterar nytt, inkommande innehåll framför befintligt innehåll. Du bör `enableReprocessing` bara `false` ställa in på tillfällig basis. För att säkerställa konsekvens i `enableReprocessing` hela `true` corpus, bör vara för det mesta, se till att alla dokument, både nya och befintliga, är giltiga enligt den nuvarande skillset definition.

### <a name="bypass-skillset-evaluation"></a>Bypass kompetens utvärdering

Ändra en kompetens och upparbetning av denna skillset går vanligtvis hand i hand. Vissa ändringar i en kompetens bör dock inte leda till upparbetning (till exempel distribuera en anpassad färdighet till en ny plats eller med en ny åtkomstnyckel). Troligtvis är dessa perifera modifieringar som inte har någon verklig inverkan på innehållet i kompetensen själv. 

Om du vet att en förändring av kompetensen verkligen är ytlig, `disableCacheReprocessingChangeDetection` bör `true`du åsidosätta kompetensutvärdering genom att ställa in parametern till:

1. Anropa uppdateringskompetens och ändra kompetensdefinitionen.
1. Lägg till `disableCacheReprocessingChangeDetection=true` parametern på begäran.
1. Skicka ändringen.

Om du ställer in den här parametern säkerställs att endast uppdateringar av kompetensdefinitionen har bekräftats och ändringen utvärderas inte för effekter på den befintliga corpus.

I följande exempel visas en begäran om uppdateringskompetensuppsättning med parametern:

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Kringgå valideringskontroller av datakälla

De flesta ändringar i en datakälldefinition ogiltigförklarar cachen. Men för scenarier där du vet att en ändring inte bör ogiltigförklara cachen - till exempel ändra`ignoreResetRequirement` en anslutningssträng eller rotera nyckeln på lagringskontot - lade parametern på datakälluppdateringen. Om du `true` ställer in den här parametern så att committ kan gå igenom, utan att utlösa ett återställningsvillkor som skulle resultera i att alla objekt återskapas och fylls i från grunden.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Tvinga kompetens utvärdering

Syftet med cacheminnet är att undvika onödig bearbetning, men anta att du gör en ändring av en färdighet som indexeraren inte identifierar (till exempel ändra något i extern kod, till exempel en anpassad färdighet).

I det här fallet kan du använda [återställningskunskaperna](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) för att tvinga fram upparbetning av en viss färdighet, inklusive alla nedströmsfärdigheter som har ett beroende av den färdighetens utdata. Det här API:et accepterar en POST-begäran med en lista över kunskaper som ska ogiltigförklaras och markeras för upparbetning. Kör indexeraren för att anropa pipelinen efter Återställ kunskaper.

## <a name="change-detection"></a>Ändra identifiering

När du har aktiverat en cache utvärderar indexeraren ändringar i pipeline-kompositionen för att avgöra vilket innehåll som kan återanvändas och vilka som behöver bearbetas. Det här avsnittet räknar upp ändringar som gör cachen direkt ogiltig, följt av ändringar som utlöser inkrementell bearbetning. 

### <a name="changes-that-invalidate-the-cache"></a>Ändringar som gör cachen ogiltig

En ogiltig ändring är en ändring där hela cacheminnet inte längre är giltigt. Ett exempel på en ogiltig ändring är en ändring där datakällan uppdateras. Här är den fullständiga listan över ändringar som skulle ogiltigförklara din cache:

* Ändra till datakälltypen
* Ändra till behållare för datakälla
* Autentiseringsuppgifter för datakälla
* Identifieringsprincip för ändring av datakälla
* Identifieringsprincip för datakällor tar bort
* Indexerarfältsmappningar
* Indexerare parametrar
    * Tolkningsläge
    * Undantagna filnamnstillägg
    * Indexerade filnamnstillägg
    * Indexera lagringsmetadata endast för överdimensionerade dokument
    * Avgränsade textrubriker
    * Avgränsad text avgränsare
    * Dokumentrot
    * Bildåtgärd (Ändringar i hur bilder extraheras)

### <a name="changes-that-trigger-incremental-processing"></a>Ändringar som utlöser inkrementell bearbetning

Inkrementell bearbetning utvärderar din kompetensdefinition och avgör vilka kunskaper som ska köras igen, selektivt uppdaterar de berörda delarna av dokumentträdet. Här är den fullständiga listan över ändringar som resulterar i inkrementell berikning:

* Färdighet i kompetensen har olika typ. Färdighetens odatatyp uppdateras
* Färdigspecifika parametrar uppdaterade, till exempel url, standardvärden eller andra parametrar
* Färdighetsutdata ändras, färdigheten returnerar ytterligare eller olika utdata
* Färdighetsuppdateringar som resulterar är olika anor, färdighetskedja har förändrats, dvs färdigheter
* Alla ogiltighetser för uppströms färdighet, om en färdighet som ger en indata till den här färdigheten uppdateras
* Uppdateringar av projektionsplatsen för kunskapslager, resulterar i att omprojektera dokument
* Ändringar i kunskapslagerprognoserna, resulterar i att omprojektera dokument
* Utdatafältmappningar som ändrats för en indexerare resulterar i att omprojektera dokument till indexet

## <a name="api-reference"></a>API-referens

REST API-versionen `2019-05-06-Preview` ger inkrementell anrikning genom ytterligare egenskaper på indexerare, skillsets och datakällor. Förutom referensdokumentationen finns i [Konfigurera cachelagring för inkrementell anrikning](search-howto-incremental-index.md) för mer information om hur du anropar API:erna.

+ [Skapa indexerare (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 

+ [Uppdatera indexeraren (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) 

+ [Uppdatera Skillset (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) (ny URI-parameter på begäran)

+ [Återställ kompetens (api-version=2019-05-06-förhandsversion)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)

+ Databasindexerare (Azure SQL, Cosmos DB). Vissa indexerare hämtar data via frågor. För frågor som hämtar data stöder [Uppdateringsdatakälla](https://docs.microsoft.com/rest/api/searchservice/update-data-source) en ny parameter på en begäran **ignoreResetRequirement**, som ska ställas in på `true` när uppdateringsåtgärden inte ska ogiltigförklara cachen. 

  Använd **ignoreResetRequirement** sparsamt eftersom det kan leda till oavsiktlig inkonsekvens i dina data som inte kommer att upptäckas lätt.

## <a name="next-steps"></a>Nästa steg

Inkrementell anrikning är en kraftfull funktion som utökar förändringsspårning till skillsets och AI-anrikning. AIncremental enrichment möjliggör återanvändning av befintligt bearbetat innehåll när du itererar över skillset-design.

Som ett nästa steg aktiverar du cachelagring på en befintlig indexerare eller lägger till en cache när du definierar en ny indexerare.

> [!div class="nextstepaction"]
> [Konfigurera cachelagring för inkrementell anrikning](search-howto-incremental-index.md)
