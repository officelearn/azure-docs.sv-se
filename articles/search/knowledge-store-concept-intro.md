---
title: Introduktion till kunskaps lager (för hands version)
titleSuffix: Azure Cognitive Search
description: Skicka berikade dokument till Azure Storage där du kan visa, ändra form på och använda berikade dokument i Azure Kognitiv sökning och i andra program. Den här funktionen finns i offentlig för hands version.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: aa32f671756b8ba7f17c25592b6a15b66de42b2c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790020"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Introduktion till kunskaps lager i Azure Kognitiv sökning

> [!IMPORTANT] 
> Kunskaps lagret är för närvarande en offentlig för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API version 2019-05-06-Preview](search-api-preview.md) innehåller för hands versions funktioner. Det finns för närvarande begränsad Portal support och inget stöd för .NET SDK.

Kunskaps lager är en funktion i Azure Kognitiv sökning som sparar utdata från en [AI-pipeline](cognitive-search-concept-intro.md) för senare analys eller annan efterföljande bearbetning. Ett *berikat dokument* är en pipeline-utdata som skapas från innehåll som har extraherats, strukturer ATS och analyser ATS med hjälp av AI-processer. I en standard-AI-pipeline är berikade dokument övergående, som endast används vid indexering och sedan ignoreras. Med kunskaps lager bevaras de dokument som är omfattande. 

Om du har använt kognitiva färdigheter med Azure Kognitiv sökning tidigare vet du redan att *färdighetsuppsättningar* flyttar ett dokument genom en sekvens av anrikninger. Resultatet kan vara ett sökindex eller (nytt i den här förhands granskningen) projektioner i ett kunskaps lager. De två utmatningarna, Sök indexet och kunskaps lagret delar samma innehåll, men lagras och används på olika sätt.

Fysiskt är ett kunskaps lager [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview), antingen Azure Table Storage, Azure Blob Storage eller båda. Alla verktyg eller processer som kan ansluta till Azure Storage kan använda innehållet i ett kunskaps lager.

![Kunskaps lager i Pipeline-diagram](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Kunskaps lager i Pipeline-diagram")

Om du vill använda kunskaps lager lägger du till ett `knowledgeStore`-element i en färdigheter som definierar steg-för-steg-åtgärder i en indexerings pipeline. Under körningen skapar Azure Kognitiv sökning ett utrymme i ditt Azure Storage-konto och skapar ett berikat dokument som blobbar eller tabeller, beroende på din konfiguration.

## <a name="benefits-of-knowledge-store"></a>Fördelar med kunskaps lager

Ett kunskaps lager ger dig struktur, kontext och faktiskt innehåll – uppnår från ostrukturerade och delvis strukturerade datafiler som blobbar, bildfiler som har genomgått analyser eller till och med strukturerade data som har ändrats i nya formulär. I en [steg-för-steg-genom gång](knowledge-store-howto.md)kan du se hur ett kompakt JSON-dokument är partitionerat i under strukturer, rekonstituerat i nya strukturer och på annat sätt görs tillgängligt för efterföljande processer som Machine Learning och data science-arbetsbelastningar.

Även om det är användbart att se vad en AI-pipeline kan skapa, är det möjligt att ändra form på data. Du kan börja med en grundläggande färdigheter och sedan iterera över den för att lägga till ökande struktur nivåer, som du sedan kan kombinera i nya strukturer, i andra appar förutom Azure Kognitiv sökning.

Räknat, fördelarna med kunskaps lager inkluderar följande:

+ Använd omfattande dokument i andra [analys-och rapporterings verktyg](#tools-and-apps) än Sök. Power BI med Power Query är ett övertygande val, men alla verktyg och appar som kan ansluta till Azure Storage kan hämta från ett kunskaps lager som du skapar.

+ Förfina en AI-indexerings pipeline vid fel sökning av steg och färdigheter definitioner. I ett kunskaps lager visas produkten av en färdigheter-definition i en AI-indexerad pipeline. Du kan använda dessa resultat för att utforma en bättre färdigheter, eftersom du kan se exakt vad som kan se ut. Du kan använda [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) i Azure Storage för att visa innehållet i ett kunskaps lager.

+ Forma data till nya formulär. Omformningen är att kodifiera i färdighetsuppsättningar, men punkten är att en färdigheter nu kan tillhandahålla den här funktionen. [Formaren-kunskapen](cognitive-search-skill-shaper.md) i Azure kognitiv sökning har utökats för att kunna hantera den här uppgiften. Med hjälp av omformningen kan du definiera en projektion som anpassas efter den avsedda användningen av data medan du bevarar relationer.

> [!Note]
> Är du nybörjare på AI-anrikning och kognitiva färdigheter? Azure Kognitiv sökning integreras med Cognitive Services vision-och språk funktioner för att extrahera och utöka källdata med OCR (optisk tecken läsning) över bildfiler, enhets igenkänning och nyckel fras extrahering från textfiler med mera. Mer information finns i [AI-anrikning i Azure kognitiv sökning](cognitive-search-concept-intro.md).

## <a name="creating-a-knowledge-store"></a>Skapa ett kunskaps lager

Ett kunskaps lager är en del av en [färdigheter](cognitive-search-working-with-skillsets.md), som i sin tur är en del av en [indexerare](search-indexer-overview.md). 

I den här för hands versionen kan du skapa ett kunskaps lager med hjälp av REST API och `api-version=2019-05-06-Preview`, eller via guiden **Importera data** i portalen.

### <a name="json-representation-of-a-knowledge-store"></a>JSON-representation av ett kunskaps lager

Följande JSON anger en `knowledgeStore`, som är en del av en färdigheter, som anropas av en indexerare (visas inte). Om du redan är bekant med AI-anrikning bestämmer en färdigheter skapande, organisation och ämne för varje berikat dokument. En färdigheter måste innehålla minst en kunskap, troligen en formaren-skicklighet om du är modulating data strukturer.

En `knowledgeStore` består av en anslutning och projektioner. 

+ Anslutning är till ett lagrings konto i samma region som Azure Kognitiv sökning. 

+ Projektioner kan vara tabell-, JSON-objekt eller-filer. `Tables` definiera det fysiska uttrycket för berikade dokument i Azure Table Storage. `Objects` definiera fysiska JSON-objekt i Azure Blob Storage. `Files` är binärfiler som bilder som har extraherats från dokumentet som ska sparas.

+ Projektioner är en samling projekt objekt, varje projektions objekt kan innehålla `tables``objects` och `files`. Fördelade projekt i en enstaka projektion är relaterade till varandra även när de projiceras mellan typer (tabeller, objekt eller filer). Projektioner i projektions objekt är inte relaterade och är oberoende av varandra. Samma form kan projiceras Aross flera projekt objekt.

```json
{
  "name": "my-new-skillset",
  "description": "Example showing knowledgeStore placement in a skillset.",
  "skills":
  [
    {
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document/content/phrases/*",
    "inputs": [
        {
        "name": "text",
        "source": "/document/content/phrases/*"
        },
        {
        "name": "sentiment",
        "source": "/document/content/phrases/*/sentiment"
        }
    ],
    "outputs": [
        {
        "name": "output",
        "targetName": "analyzedText"
        }
    ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<your key goes here>"
    },
  "knowledgeStore": { 
    "storageConnectionString": "<your connection string goes here>", 
    "projections": [ 
        { 
            "tables": [  
            { "tableName": "Reviews", "generatedKeyName": "ReviewId", "source": "/document/Review" , "sourceContext": null, "inputs": []}, 
            { "tableName": "Sentences", "generatedKeyName": "SentenceId", "source": "/document/Review/Sentences/*", "sourceContext": null, "inputs": []}, 
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/Review/Sentences/*/KeyPhrases", "sourceContext": null, "inputs": []}, 
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/Review/Sentences/*/Entities/*" ,"sourceContext": null, "inputs": []} 

            ], 
            "objects": [ 
               
            ], 
            "files": [

            ]  
        },
        { 
            "tables": [ 
            ], 
            "objects": [ 
                { 
                "storageContainer": "Reviews", 
                "format": "json", 
                "source": "/document/Review", 
                "key": "/document/Review/Id" 
                } 
            ],
            "files": [
                
            ]  
        }        
    ]     
    } 
}
```

Det här exemplet innehåller inte några bilder, ett exempel på hur du använder filprojektioner finns i [arbeta med projektioner](knowledge-store-projection-overview.md).
### <a name="sources-of-data-for-a-knowledge-store"></a>Data källor för ett kunskaps lager

Om ett kunskaps lager ska matas ut från en AI-pipeline, vilka är indata? De ursprungliga data som du vill extrahera, utöka och slutligen spara till ett kunskaps lager kan komma från alla Azure-Data källor som stöds av Sök indexerarna: 

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)

* [Azure Table Storage](search-howto-indexing-azure-tables.md)

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Indexerarna och färdighetsuppsättningar du skapar extrahera och utöka eller transformera det här innehållet som en del av en indexerings arbets belastning och sparar sedan resultatet i ett kunskaps lager.

### <a name="rest-apis-used-in-creation-of-a-knowledge-store"></a>REST-API: er som används för att skapa ett kunskaps lager

Endast två API: er har de tillägg som krävs för att skapa ett kunskaps lager (skapa färdigheter och skapa indexerare). Andra API: er används i befintligt skick.

| Objekt | REST-API | Beskrivning |
|--------|----------|-------------|
| Data Källa | [Skapa datakälla](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | En resurs som identifierar en extern Azure-datakälla som tillhandahåller källdata som används för att skapa dokument med omfattande data.  |
| färdigheter | [Skapa färdigheter (API-version = 2019-05 -06 – för hands version)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | En resurs som koordinerar användningen av [inbyggda kunskaper](cognitive-search-predefined-skills.md) och [anpassade kognitiva kunskaper](cognitive-search-custom-skill-interface.md) som används i en anriknings pipeline vid indexering. En färdigheter har en `knowledgeStore`-definition som ett underordnat element. |
| Tabbindex | [Skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Ett schema som uttrycker ett sökindex. Fält i index kartan till fält i källdata eller till fält som tillverkas under anriknings fasen (till exempel ett fält för organisations namn som skapats av enhets igenkänning). |
| Indexer | [Skapa indexerare (API-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | En resurs som definierar komponenter som används vid indexering: inklusive en data källa, en färdigheter, fält associationer från käll-och mellanliggande data strukturer till mål index och själva indexet. Att köra indexeraren är utlösaren för data inmatning och berikning. Utdata är ett sökindex baserat på index schemat, ifyllt med källdata, berikade med färdighetsuppsättningar.  |

### <a name="physical-composition-of-a-knowledge-store"></a>Fysisk sammansättning för ett kunskaps lager

 En *projektion*, som är ett element i en `knowledgeStore`-definition, leda till schemat och strukturen på utdata så att den matchar den avsedda användningen. Du kan definiera flera projektioner om du har program som använder data i olika format och former. 

Projektioner kan ledas som objekt eller tabeller:

+ Som ett objekt mappar projektionen till Blob Storage, där projektionen sparas i en behållare, inom vilka är objekt eller hierarkiska representationer i JSON för scenarier som en data vetenskaps pipeline.

+ Som en tabell mappas projektionen till Table Storage. En tabell representation bevarar relationer för scenarier som data analys eller exportera som data ramar för maskin inlärning. De berikade projektionerna kan sedan enkelt importeras till andra data lager. 

Du kan skapa flera projektioner i ett kunskaps lager för att hantera olika constituencies i din organisation. En utvecklare kan behöva åtkomst till den fullständiga JSON-representationen av ett berikat dokument, medan data forskare eller analytiker kan ha detaljerade eller modulära data strukturer som är formade för din färdigheter.

Om ett av målen för anriknings processen exempelvis är att skapa en data uppsättning som används för att träna en modell, är det ett sätt att projicera data i objekt arkivet på ett sätt att använda data i dina pipelines för data vetenskap. Alternativt, om du vill skapa en snabb Power BI instrument panel baserat på de omfattande dokumenten, skulle tabell projektionen fungera bra.

<a name="tools-and-apps"></a>

## <a name="connecting-with-tools-and-apps"></a>Ansluta med verktyg och appar

När det finns lagrings utrymme kan alla verktyg och tekniker som ansluter till Azure Blob eller Table Storage användas för att utforska, analysera eller använda innehållet. Följande lista är en start:

+ [Storage Explorer](knowledge-store-view-storage-explorer.md) för att visa en omfattande dokument struktur och innehåll. Överväg detta som ditt bas linje verktyg för att Visa kunskaps lager innehåll.

+ [Power BI](knowledge-store-connect-power-bi.md) för rapporterings-och analys verktygen om du har numeriska data.

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) för ytterligare modifiering.

## <a name="next-steps"></a>Nästa steg

Kunskaps lager erbjuder beständiga dokument, är användbara när du utformar en färdigheter, eller när du skapar nya strukturer och innehåll för användning av klient program som kan komma åt ett Azure Storage-konto.

Den enklaste metoden för att skapa omfattande dokument är via guiden **Importera data** , men du kan också använda Postman och REST API, vilket är mer användbart om du vill veta mer om hur objekt skapas och refereras till.

> [!div class="nextstepaction"]
> [Skapa ett kunskaps lager med hjälp av portalen](knowledge-store-create-portal.md)
> [skapa ett kunskaps lager med POSTman och REST-API: et](knowledge-store-create-rest.md)
