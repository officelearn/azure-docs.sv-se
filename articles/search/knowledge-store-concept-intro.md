---
title: Introduktion till kunskapsbutik (förhandsgranskning)
titleSuffix: Azure Cognitive Search
description: Skicka utökade dokument till Azure Storage där du kan visa, omforma och använda utökade dokument i Azure Cognitive Search och i andra program. Den här funktionen är en allmänt tillgänglig förhandsversion.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0ad780c04954c09ddfd432b3c7de3dc65f0841bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942984"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Introduktion till kunskapsbutiker i Azure Cognitive Search

> [!IMPORTANT] 
> Knowledge Store är för närvarande i offentlig förhandsversion. Förhandsversionsfunktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API-versionen 2019-05-06-Preview](search-api-preview.md) innehåller förhandsgranskningsfunktioner. Det finns för närvarande begränsat portalstöd och inget .NET SDK-stöd.

Knowledge Store är en funktion i Azure Cognitive Search som bevarar utdata från en [AI-anrikningspipeline](cognitive-search-concept-intro.md) för oberoende analys eller nedströmsbearbetning. Ett *berikat dokument* är en pipelines utdata, skapad från innehåll som har extraherats, strukturerats och analyserats med hjälp av AI-processer. I en standard-AI-pipeline är berikade dokument övergående, används endast under indexeringen och sedan kasseras. Med kunskapsbutik bevaras berikade dokument. 

Om du har använt kognitiva färdigheter i det förflutna, vet du redan att *skillsets* flytta ett dokument genom en sekvens av berikningar. Resultatet kan vara ett sökindex eller (nytt i den här förhandsversionen) prognoser i en kunskapsbutik. De två utgångarna, sökindex och kunskapslager, är produkter av samma pipeline; härleds från samma ingångar, men resulterar i utdata som är strukturerade, lagrade och används på mycket olika sätt.

Fysiskt är ett kunskapslager [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview), antingen Azure Table storage, Azure Blob storage eller båda. Alla verktyg eller processer som kan ansluta till Azure Storage kan använda innehållet i ett kunskapslager.

![Kunskapslager i pipelinediagram](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Kunskapslager i pipelinediagram")

## <a name="benefits-of-knowledge-store"></a>Fördelar med kunskapsbutik

Ett kunskapslager ger dig struktur, sammanhang och faktiskt innehåll - som hämtas från ostrukturerade och halvstrukturerade datafiler som blobbar, bildfiler som har analyserats, eller till och med strukturerat data, omformas till nya formulär. I en [steg-för-steg-genomgång](knowledge-store-create-rest.md)kan du se första hand hur ett tätt JSON-dokument delas ut i understrukturer, rekonstitueras till nya strukturer och på annat sätt görs tillgängligt för nedströmsprocesser som maskininlärning och datavetenskapsarbetsbelastningar.

Även om det är användbart att se vad en AI-anrikningspipeline kan producera, är den verkliga potentialen i ett kunskapslager möjligheten att omforma data. Du kan börja med en grundläggande kompetens och sedan iterera över den för att lägga till ökande strukturnivåer, som du sedan kan kombinera till nya strukturer, förbrukningsbara i andra appar förutom Azure Cognitive Search.

Uppräknas, fördelarna med kunskapsbutik inkluderar följande:

+ Använd utökade dokument i [andra analys- och rapporteringsverktyg](#tools-and-apps) än sökning. Power BI med Power Query är ett övertygande val, men alla verktyg eller appar som kan ansluta till Azure Storage kan hämta från ett kunskapslager som du skapar.

+ Förfina en AI-indexeringspipeline medan du felsöker steg och skillset-definitioner. En kunskapsbutik visar produkten av en kompetensdefinition i en AI-indexeringspipeline. Du kan använda dessa resultat för att utforma en bättre kompetens eftersom du kan se exakt hur berikande ser ut. Du kan använda [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) i Azure Storage för att visa innehållet i ett kunskapsarkiv.

+ Forma data till nya formulär. Omformningen kodifieras i skillsets, men poängen är att en kompetens kan nu ge denna förmåga. [Shaper-färdigheten](cognitive-search-skill-shaper.md) i Azure Cognitive Search har utökats för att hantera den här uppgiften. Genom att omforma kan du definiera en projektion som stämmer överens med din avsedda användning av data samtidigt som relationer bevaras.

> [!Note]
> Ny till AI-anrikning och kognitiva färdigheter? Azure Cognitive Search integreras med Cognitive Services Vision och Language funktioner för att extrahera och berika källdata med optisk teckenigenkänning (OCR) över bildfiler, entitetsigenkänning och nyckelfrasextrahering från textfiler och mycket mer. Mer information finns [i AI-anrikning i Azure Cognitive Search](cognitive-search-concept-intro.md).

## <a name="physical-storage"></a>Fysisk förvaring

Det fysiska uttrycket för en kunskapsbutik artikuleras genom elementet `projections` i en `knowledgeStore` definition i en Skillset. Projektionen definierar en struktur för utdata så att den matchar din avsedda användning.

Projektioner kan artikuleras som tabeller, objekt eller filer.

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

Vilken typ av projektion du anger i den här strukturen avgör vilken typ av lagring som används av Knowledge Store.

+ Tabelllagring används när `tables`du definierar . Definiera en tabellprojektion när du behöver tabellrapporteringsstrukturer för indata till analysverktyg eller exportera som dataramar till andra datalager. Du kan `tables` ange flera för att hämta en delmängd eller ett tvärsnitt av utökade dokument. Inom samma projektionsgrupp bevaras tabellrelationer så att du kan arbeta med dem alla.

+ Blob-lagring används när `objects` `files`du definierar eller . Den fysiska representationen av en `object` är en hierarkisk JSON-struktur som representerar ett berikat dokument. A `file` är en bild som extraherats från ett dokument och överförs intakt till Blob-lagring.

Ett enda projektionsobjekt innehåller `tables`en `objects` `files`uppsättning , , och för många scenarier kan det räcka med att skapa en projektion. 

Det är dock möjligt att `table` - `object` - `file` skapa flera uppsättningar projektioner, och det kan du göra om du vill ha olika datarelationer. I en uppsättning är data relaterade, förutsatt att dessa relationer finns och kan identifieras. Om du skapar ytterligare uppsättningar är dokumenten i varje grupp aldrig relaterade. Ett exempel på att använda flera projektionsgrupper kan vara om du vill att samma data ska projiceras för användning med ditt onlinesystem och det måste representeras på ett visst sätt, du vill också att samma data ska projiceras för användning i en data science pipeline som representeras Olikt.

## <a name="requirements"></a>Krav 

[Azure Storage](https://docs.microsoft.com/azure/storage/) krävs. Det ger fysisk förvaring. Du kan använda Blob storage, Table storage eller båda. Blob-lagring används för intakta berikade dokument, vanligtvis när utdata går till nedströmsprocesser. Tabelllagring är för segment av berikade dokument, som vanligen används för analys och rapportering.

[Skillset](cognitive-search-working-with-skillsets.md) krävs. Den innehåller `knowledgeStore` definitionen, och den bestämmer strukturen och sammansättningen av ett berikat dokument. Du kan inte skapa ett kunskapsarkiv med hjälp av en tom kompetens. Du måste ha minst en färdighet i en kompetens.

[Indexeraren](search-indexer-overview.md) krävs. En kompetens anropas av en indexerare som driver körningen. Indexerare levereras med sin egen uppsättning krav och attribut. Flera av dessa attribut har en direkt inverkan på ett kunskapslager:

+ Indexerare kräver en [Azure-datakälla som stöds](search-indexer-overview.md#supported-data-sources) (pipelinen som slutligen skapar kunskapsarkivet börjar med att hämta data från en källa som stöds på Azure). 

+ Indexerare kräver ett sökindex. En indexerare kräver att du anger ett indexschema, även om du aldrig planerar att använda det. Ett minimalt index har ett strängfält som anges som nyckel.

+ Indexerare tillhandahåller valfria fältmappningar som används för att aliasa ett källfält till ett målfält. Om en standardfältmappning behöver ändras (om du vill använda ett annat namn eller en annan typ) kan du skapa en [fältmappning](search-indexer-field-mappings.md) i en indexerare. För kunskapslagerutdata kan målet vara ett fält i ett blob-objekt eller en tabell.

+ Indexatorer har scheman och andra egenskaper, till exempel ändringsidentifieringsmekanismer som tillhandahålls av olika datakällor, kan också tillämpas på ett kunskapslager. Du kan till exempel [schemalägga](search-howto-schedule-indexers.md) berikning med jämna mellanrum för att uppdatera innehållet. 

## <a name="how-to-create-a-knowledge-store"></a>Hur man skapar en kunskapsbutik

Om du vill skapa kunskapsarkivet använder`api-version=2019-05-06-Preview`du portalen eller förhandsversionen AV REST API ( ).

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

Guiden **Importera data** innehåller alternativ för att skapa ett kunskapsarkiv. För inledande utforskning, [skapa din första kunskapsbutik i fyra steg.](knowledge-store-connect-power-bi.md)

1. Välj en datakälla som stöds.

1. Ange berikning: koppla en resurs, välj kunskaper och ange ett kunskapslager. 

1. Skapa ett indexschema. Guiden kräver det och kan härleda en åt dig.

1. Kör guiden. Extraktion, anrikning och lagring sker i det här sista steget.

### <a name="use-create-skillset-and-the-preview-rest-api"></a>Använd Skapa kunskaper och rest-API:et för förhandsversionen

A `knowledgeStore` definieras i en [kompetens](cognitive-search-working-with-skillsets.md), som i sin tur anropas av en [indexerare](search-indexer-overview.md). Under anrikningen skapar Azure Cognitive Search ett utrymme i ditt Azure Storage-konto och projicerar de berikade dokumenten som blobbar eller i tabeller, beroende på din konfiguration.

För närvarande är förhandsversionen REST API den enda mekanismen genom vilken du kan skapa ett kunskapslager programmässigt. Ett enkelt sätt att utforska är att [skapa din första kunskapsbutik med Postman och REST API.](knowledge-store-create-rest.md)

Referensinnehåll för den här förhandsgranskningsfunktionen finns i [API-referensavsnittet](#kstore-rest-api) i den här artikeln. 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Så här ansluter du till verktyg och appar

När anrikningarna finns i lagring kan alla verktyg eller teknik som ansluter till Azure Blob eller Table Storage användas för att utforska, analysera eller använda innehållet. Följande lista är en början:

+ [Lagringsutforskaren](knowledge-store-view-storage-explorer.md) om du vill visa utökad dokumentstruktur och innehåll. Betrakta detta som baslinjeverktyg för visning av innehåll i kunskapsarkivet.

+ [Power BI](knowledge-store-connect-power-bi.md) för rapportering och analys. 

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) för ytterligare manipulering.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>API-referens

REST API-versionen `2019-05-06-Preview` tillhandahåller kunskapsarkivet genom ytterligare definitioner av skillsets. Förutom referensen finns i [Skapa ett kunskapsarkiv med Postman](knowledge-store-create-rest.md) för mer information om hur du anropar API:erna.

+ [Skapa skillset (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-skillset) 
+ [Uppdatera Skillset (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) 


## <a name="next-steps"></a>Nästa steg

Kunskapsarkivet erbjuder envishet i utökade dokument, användbart när du utformar en kompetens eller skapar nya strukturer och innehåll för förbrukning av alla klientprogram som kan komma åt ett Azure Storage-konto.

Den enklaste metoden för att skapa berikade dokument är [via portalen](knowledge-store-create-portal.md), men du kan också använda Postman och REST API, vilket är mer användbart om du vill ha insikt i hur objekt skapas och refereras.

> [!div class="nextstepaction"]
> [Skapa ett kunskapslager med Postman och REST](knowledge-store-create-rest.md)

Om du vill veta mer om projektioner, funktioner och hur du [definierar dem i en kompetens](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Projektioner i en kunskapsbutik](knowledge-store-projection-overview.md)

För en handledning som täcker avancerade projektioner begrepp som skivning, inline forma och relationer, börja med [att definiera prognoser i en kunskapsbutik](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definiera projektioner i en kunskapsbutik](knowledge-store-projections-examples.md)