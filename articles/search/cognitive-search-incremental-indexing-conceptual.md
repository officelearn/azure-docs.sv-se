---
title: Stegvis indexering (för hands version)
titleSuffix: Azure Cognitive Search
description: Konfigurera din AI-pipeline för att driva dina data till en eventuell konsekvens för att hantera eventuella uppdateringar av färdigheter, färdighetsuppsättningar, indexerare eller data källor. Den här funktionen är för närvarande en offentlig för hands version
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 76ab8784f04f3c67e4ea8062505931783048dea1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113598"
---
# <a name="what-is-incremental-indexing-in-azure-cognitive-search"></a>Vad är stegvis indexering i Azure Kognitiv sökning?

> [!IMPORTANT] 
> Stegvis indexering är för närvarande en offentlig för hands version. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Den [REST API version 2019-05-06 – för hands version](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande ingen portal eller .NET SDK-support.

Stegvis indexering är en ny funktion i Azure Kognitiv sökning som lägger till cachelagring och tillstånd för berikat innehåll i en kognitiv färdigheter, vilket ger dig kontroll över bearbetning och ombearbetning av enskilda steg i en anriknings pipeline. Detta bevarar inte bara din monetära investering i bearbetningen, men det gör också ett mer effektivt system. När strukturer och innehåll cachelagras kan en indexerare avgöra vilka kunskaper som har ändrats och bara köra de som har ändrats, samt eventuella underordnade beroende kunskaper. 

Med stegvis indexering gör den aktuella versionen av pipelinen för anrikning den minsta mängden arbete för att garantera konsekvens för alla dokument i ditt index. För scenarier där du vill ha fullständig kontroll kan du använda detaljerade kontroller för att åsidosätta de förväntade beteendena. Mer information om konfiguration finns i [Konfigurera stegvis indexering](search-howto-incremental-index.md).

## <a name="indexer-cache"></a>Indexerare-cache

Stegvis indexering lägger till en indexerare-cache i pipelinen. Indexeraren cachelagrar resultaten från dokument sprickor och utdata för varje dokument. När en färdigheter uppdateras, körs bara de ändrade eller underordnade färdigheterna igen. De uppdaterade resultaten skrivs till cachen och dokumentet uppdateras i indexet och kunskaps lagret.

Fysiskt är cacheminnet ett lagrings konto. Alla index i en Sök tjänst kan dela samma lagrings konto för indexeraren cache. Varje indexerare tilldelas en unik och oföränderlig cache-identifierare.

### <a name="cache-configuration"></a>Cache-konfiguration

Du måste ange egenskapen `cache` på indexeraren för att starta får från stegvis indexering. I följande exempel illustreras en indexerare med cachelagring aktiverat. Vissa delar av den här konfigurationen beskrivs i följande avsnitt.

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true,
        "id" : "Auto generated Id you do not need to set"
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters":{}
}
```

Om du ställer in den här egenskapen för första gången på en befintlig indexerare måste du också återställa den, vilket leder till att alla dokument i data källan bearbetas igen. Målet med stegvis indexering är att göra dokumenten i indexet konsekvent med din data källa och den aktuella versionen av din färdigheter. Att återställa indexet är det första steget mot denna konsekvens eftersom det eliminerar dokument som har berikats av tidigare versioner av färdigheter. Indexeraren måste återställas för att börja med en konsekvent bas linje.

### <a name="cache-lifecycle"></a>Cache-livscykel

Livs längden för cachen hanteras av indexeraren. Om egenskapen `cache` på indexeraren är inställd på null eller om anslutnings strängen ändrades, tas den befintliga cachen bort. Cachens livs cykel är också kopplad till indexerare livs cykel. Om en indexerare tas bort, tas även den tillhör ande cachen bort.

### <a name="indexer-cache-mode"></a>Cache-läge för indexerare

Indexeraren cache kan köras i lägen där data bara skrivs till cacheminnet eller data skrivs till cachen och används för att utöka dokument igen.  Du kan tillfälligt inaktivera stegvis berikning genom att ange `enableReprocessing` egenskap i cachen som `false`, och senare återuppta stegvis anrikning och köra eventuell konsekvens genom att ställa in den på `true`. Den här kontrollen är särskilt användbar när du vill prioritera att indexera nya dokument för att säkerställa konsekvens i sökkorpus av dokument.

## <a name="change-detection-override"></a>Åsidosättning av ändrings identifiering

Stegvis indexering ger detaljerad kontroll över alla aspekter av pipelinen. Med den här kontrollen kan du hantera situationer där en ändring kan ha oönskade konsekvenser. Om du till exempel redigerar en färdigheter och uppdaterar URL: en för en anpassad färdighet kommer indexeraren att indexera de cachelagrade resultaten för den kunskapen. Om du bara flyttar slut punkten till en annan virtuell dator eller omdistribuerar din kunskap med en ny åtkomst nyckel vill du verkligen inte att befintliga dokument ombearbetas.

För att säkerställa att indexeraren bara är beroende av det som du uttryckligen behöver, kan uppdateringar av färdigheter också ange parametern `disableCacheReprocessingChangeDetection` QueryString för att `true`. När den här inställningen är aktive rad ser den här parametern till att endast uppdateringar av färdigheter är bekräftade och att ändringen inte utvärderas för effekter på den befintliga sökkorpus.

I följande exempel visas QueryString-användning. Den ingår i begäran, med &-avgränsade nyckel värde par. 

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

## <a name="cache-invalidation"></a>Ogiltig cachelagring

Det här scenariot är ett där du kan distribuera en ny version av en anpassad färdighet, inget i pipeline-ändringar, men du behöver en speciell färdighet ogiltig och alla berörda dokument har ombearbetats för att avspegla fördelarna med en uppdaterad modell. I sådana fall kan du anropa funktionen för att förklara kunskaper i färdigheter. API: et för återställning av kunskaper accepterar en POST-begäran med en lista över kunskaps utmatningar i cacheminnet som ska ogiltig förklaras. Mer information om API för återställning av kunskaper finns i [Reset Indexer (sök REST API)](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="bi-directional-change-detection"></a>Dubbelriktad ändrings identifiering

Indexerare flyttar inte bara framåt och bearbetar nya dokument, men kan nu flytta baklänges och bearbeta tidigare bearbetade dokument till konsekvens. Med den här nya funktionen är det viktigt att förstå hur ändringar i dina komponenter i pipeline-komponenter resulterar i att indexeraren fungerar. Indexeraren kommer att köa arbetet att göras när den identifierar en ändring som antingen är inkonsekvent eller inkonsekvent i förhållande till det cachelagrade innehållet.

### <a name="invalidating-changes"></a>Ogiltig ändring

Invalideringen av ändringar är sällsynt men har en betydande inverkan på statusen för din pipeline. En ogiltig ändring är en där hela cachen inte längre är giltig. Ett exempel på en ogiltig ändring är en plats där data källan uppdateras. För scenarier när du vet att ändringen inte ska göra en ogiltig verifiering av cachen, t. ex. genom att rotera nyckeln på lagrings kontot, ska parametern `ignoreResetRequirement` QueryString anges till `true` på uppdaterings åtgärden för den aktuella resursen för att säkerställa att åtgärden är ej avvisad.

Här är en fullständig lista över ändringar som skulle göra din cache ogiltig:

* Ändra till typ av data Källa
* Ändra till data källans behållare
* Autentiseringsuppgifter för data Källa
* Princip för ändrings identifiering av data Källa
* Ta bort identifierings princip för data källor
* Fält mappningar för indexerare
* Indexerings parametrar
    * Tolknings läge
    * Uteslutna fil namns tillägg
    * Indexerade fil namns tillägg
    * Indexera lagrings metadata endast för dokument med storleks storlek
    * Avgränsade text rubriker
    * Avgränsad text avgränsare
    * Dokument rot
    * Avbildnings åtgärd (ändringar i hur bilder extraheras)

### <a name="inconsistent-changes"></a>Inkonsekventa ändringar

Ett exempel på en inkonsekvent ändring är en uppdatering av din färdigheter som ändrar en färdighet. Ändringen kan göra att en del av cachen är inkonsekvent. Indexeraren identifierar arbetet för att göra saker konsekventa igen.  

Den fullständiga listan över ändringar som resulterar i inkonsekvens i cache:

* Kompetensen i färdigheter har en annan typ. OData-typen för kunskapen uppdateras
* Kunskapsbaserade parametrar har uppdaterats, till exempel URL, standardinställningar eller andra parametrar
* Ändringar i kunskaps utmatningar, kunskapen returnerar ytterligare eller andra utdata
* Kunskaps uppdateringar som uppstår är olika föregångare, kunskaps kedjan har ändrats, dvs. Kompetens inmatningar
* Eventuella indata från en överordnad kompetens, om en färdighet som tillhandahåller indata till den här kompetensen uppdateras
* Uppdateringar till platsen för kunskaps lager projektion, resultat i omprojektering av dokument
* Ändringar i kunskaps lagrets projektioner, resultat i omprojektering av dokument
* Mappningar av utdatakolumner som har ändrats på en indexerare resulterar i omprojektering av dokument till indexet

## <a name="rest-api-reference-for-incremental-indexing"></a>REST API referens för stegvis indexering

REST `api-version=2019-05-06-Preview` innehåller API: er för stegvis indexering, med tillägg till indexerare, färdighetsuppsättningar och data källor. Referens dokumentationen innehåller för närvarande inte dessa tillägg. I följande avsnitt beskrivs API-ändringar.

### <a name="indexers"></a>Indexerare

[Skapa indexerare](https://docs.microsoft.com/rest/api/searchservice/create-indexer) och [Uppdatera indexeraren](https://docs.microsoft.com/rest/api/searchservice/update-indexer) kommer nu att visa nya egenskaper för cachen:

* `StorageAccountConnectionString`: anslutnings strängen till det lagrings konto som ska användas för att cachelagra mellanliggande resultat.

* `CacheId`: `cacheId` är identifieraren för behållaren i det `annotationCache` lagrings konto som ska användas som cache för den här indexeraren. Den här cachen är unik för den här indexeraren och om indexeraren tas bort och återskapas med samma namn kommer `cacheId` att återskapas. `cacheId` kan inte anges, den genereras alltid av tjänsten.

* `EnableReprocessing`: Ställ in på `true` som standard när det är inställt på `false`kommer dokumenten att fortsätta att skrivas till cacheminnet, men inga befintliga dokument ombearbetas baserat på cache-data.

Vissa indexerare (via [data källor](https://docs.microsoft.com/rest/api/searchservice/create-data-source)) hämtar data via frågor. För frågor som hämtar data kommer indexerare också att ha stöd för en ny frågesträngparametern: `ignoreResetRequirement` ska anges till `true` när din uppdaterings åtgärd inte ska göra en ogiltig verifiering av cachen.

### <a name="skillsets"></a>Kompetensuppsättningar

Färdighetsuppsättningar har inte stöd för nya åtgärder, men har stöd för en ny QueryString-parameter: `disableCacheReprocessingChangeDetection` ska anges till `true` när du inte vill att befintliga dokument ska uppdateras baserat på den aktuella åtgärden.

### <a name="datasources"></a>Data källor

Data källor har inte stöd för några nya åtgärder, men har stöd för en ny QueryString-parameter: `ignoreResetRequirement` ska vara inställt på `true` när uppdaterings åtgärden inte ska ogiltig förklara cachen.

## <a name="best-practices"></a>Bästa praxis

Den rekommenderade metoden för att använda stegvis indexering är att konfigurera stegvis indexering genom att ange egenskapen cache i en ny indexerare eller återställa en befintlig indexerare och ange egenskapen cache.

Använd `ignoreResetRequirement` sparsamt eftersom det kan leda till oavsiktlig inkonsekvens i dina data som inte kommer att identifieras enkelt.

## <a name="takeaways"></a>Lärdomar

Stegvis indexering är en kraftfull funktion som utökar ändrings spårningen från data källan till alla aspekter av pipelinen för anrikning, inklusive data källan, den aktuella versionen av din färdigheter och indexeraren. När dina kunskaper, färdighetsuppsättningar eller anrikninger utvecklas ser den här pipelinen till att minst möjligt arbete utförs samtidigt som dokumenten blir mer konsekvent.

## <a name="next-steps"></a>Nästa steg

Kom igång med stegvis indexering genom att lägga till ett cacheminne till en befintlig indexerare eller Lägg till cachen när du definierar en ny indexerare.

> [!div class="nextstepaction"]
> [Konfigurera stegvis indexering](search-howto-incremental-index.md)
