---
title: Bifoga en Cognitive Services-resurs med en kompetens – Azure Search
description: Anvisningar för att bifoga en allt-i-ett Cognitive Services-prenumeration för en pipeline med kognitiva funktioner i Azure Search.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 44f16b3334b991e071fa85ca4cffbc0837f0a6ec
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244430"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Bifoga en Cognitive Services-resurs med en kompetens i Azure Search 

AI-algoritmer enheten den [cognitive indexering pipelines](cognitive-search-concept-intro.md) används för dokumentet funktioner i Azure Search. Dessa algoritmer är baserade på Azure Cognitive Services-resurser, inklusive [visuellt](https://azure.microsoft.com/services/cognitive-services/computer-vision/) för bildanalys och optisk teckenläsning (OCR) och [textanalys](https://azure.microsoft.com/services/cognitive-services/text-analytics/) för igenkänning av entiteter extrahering av diskussionsämne och andra enrichments. Som används av Azure Search för dokumentet berikande, algoritmerna är omslutna inuti en *färdighet*, placeras i en *kompetens*, och som refereras av en *indexeraren* under indexering.

Du kan utöka ett begränsat antal dokument kostnadsfritt. Alternativt kan du koppla en fakturerbar Cognitive Services-resurs till en *kompetens* för större och mer frekventa arbetsbelastningar. I den här artikeln lär du att koppla en fakturerbar Cognitive Services-resurs för att utöka dokument under Azure Search [indexering](search-what-is-an-index.md).

> [!NOTE]
> Faktureringsbara händelser innehåller anrop till API: er med Cognitive Services- och bildfiler extrahering som en del av dokumentknäckning fasen i Azure Search. Det är kostnadsfritt för textextrahering från dokument eller som inte anropar Cognitive Services.
>
> Körningen av fakturerbara kunskaper de [Cognitive Services betala-som-du gå pris](https://azure.microsoft.com/pricing/details/cognitive-services/). Bild extrahering priser finns i den [Azure Search sidan med priser](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="same-region-requirement"></a>Krav på samma region

Vi kräver att Azure Search och Azure Cognitive Services finns i samma region. I annat fall får du det här meddelandet vid körning: `"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Det går inte att flytta en tjänst i olika regioner. Om du får det här felet kan skapa du en ny resurs för Cognitive Services i samma region som Azure Search.

## <a name="use-free-resources"></a>Använd kostnadsfria resurser

Du kan använda en begränsad, kostnadsfri bearbetningsalternativ för att slutföra självstudien och quickstart övningarna kognitiv sökning.

Kostnadsfri (begränsad enrichments) resurser är begränsade till 20 dokument per dag, per prenumeration.

1. Öppna guiden Importera data:

   ![Öppna guiden Importera](media/search-get-started-portal/import-data-cmd2.png "öppna guiden Importera data")

1. Välj en datakälla och fortsätta att **Lägg till kognitiv sökning (valfritt)** . En stegvis genomgång av den här guiden finns [Import, index och fråga med hjälp av portal-verktyg](search-get-started-portal.md).

1. Expandera **bifoga Cognitive Services** och välj sedan **kostnadsfri (begränsad enrichments)** :

   ![Expanderad bifoga Cognitive Services avsnittet](./media/cognitive-search-attach-cognitive-services/attach1.png "expanderas bifoga Cognitive Services-avsnitt")

1. Fortsätt till nästa steg **lägga till Enrichments**. En beskrivning av färdigheter som är tillgänglig i portalen finns i [steg 2: Lägg till kognitiva kunskaper](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) i snabbstarten kognitiv sökning.

## <a name="use-billable-resources"></a>Använd fakturerbara resurser

Se till att koppla en fakturerbar Cognitive Services-resurs för arbetsbelastningar som skapar fler än 20 enrichments per dag. Vi rekommenderar att du alltid bifoga en fakturerbar Cognitive Services-resurs, även om du aldrig kommer att behöva anropa Cognitive Services API: er. Koppla en resurs åsidosätter den dagliga gränsen.

Du debiteras endast för färdigheter som anropar API: er för Cognitive Services. Du faktureras inte för [anpassade funktioner](cognitive-search-create-custom-skill-example.md), eller färdigheter som [text fusion](cognitive-search-skill-textmerger.md), [text delare](cognitive-search-skill-textsplit.md), och [formaren](cognitive-search-skill-shaper.md), som inte är API-baserad.

1. Öppna guiden Importera data, Välj en datakälla och fortsätta att **Lägg till kognitiv sökning (valfritt)** .

1. Expandera **bifoga Cognitive Services** och välj sedan **Skapa ny resurs för Cognitive Services**. En ny flik öppnas så att du kan skapa resursen:

   ![Skapa en resurs för Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "skapa en resurs för Cognitive Services")

1. I den **plats** väljer du den region där Azure Search-tjänsten finns. Se till att använda den här regionen av prestandaskäl. Med den här regionen också annullerar avgifter för utgående bandbredd i olika regioner.

1. I den **prisnivå** väljer **S0** att hämta allt-i-ett-samling för Cognitive Services-funktioner, inklusive funktioner för visuellt innehåll och språk som stöder de fördefinierade färdigheter som används av Azure Search.

   För S0-nivån kan du hitta priserna för specifika arbetsbelastningar på den [Cognitive Services sidan med priser](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + I den **Välj erbjuder** Kontrollera **Cognitive Services** har valts.
   + Under **språk** funktioner för **Text Analytics Standard** avser AI indexering.
   + Under **Vision** funktioner för **datorn Vision S1** gäller.

1. Välj **skapa** att etablera den nya Cognitive Services-resursen.

1. Gå tillbaka till den föregående fliken som innehåller guiden Importera data. Välj **uppdatera** att visa resurs för Cognitive Services och välj sedan resursen:

   ![Välj resurs för Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "Välj resurs för Cognitive Services")

1. Expandera den **lägga till Enrichments** avsnitt för att välja de specifika kognitiva kunskaper som du vill köra på dina data. Slutför resten av guiden. En beskrivning av färdigheter som är tillgänglig i portalen finns i [steg 2: Lägg till kognitiva kunskaper](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) i snabbstarten kognitiv sökning.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Koppla en kunskaper till en resurs för Cognitive Services

Om du har en kunskaper kan koppla du den till en ny eller annan Cognitive Services-resurs.

1. På den **tjänstöversikt** väljer **kompetens**:

   ![Kompetens fliken](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "kompetens fliken")

1. Välj namnet på kompetens och välj en befintlig resurs eller skapa en ny. Välj **OK** att bekräfta ändringarna.

   ![Kompetens resurslistan](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "kompetens resurslistan")

   Kom ihåg att den **kostnadsfri (begränsad enrichments)** alternativet begränsad till 20 dokument dagligen och som du kan använda **Skapa ny resurs för Cognitive Services** att etablera en ny fakturerbar resurs. Om du skapar en ny resurs, Välj **uppdatera** att uppdatera listan över resurser för Cognitive Services och välj sedan resursen.

## <a name="attach-cognitive-services-programmatically"></a>Lägg till kognitiva tjänster programmässigt

När du definierar gruppens kunskaper avgör programmässigt, lägga till en `cognitiveServices` att gruppens kunskaper avgör. I avsnittet, med nyckeln för Cognitive Services-resursen som du vill associera med kompetens. Kom ihåg att resursen måste vara i samma region som din Azure Search-resurs. Även innehålla `@odata.type`, och ge den värdet `#Microsoft.Azure.Search.CognitiveServicesByKey`.

I följande exempel visas det här mönstret. Observera den `cognitiveServices` i slutet av definitionen.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>Exempel: Beräkna kostnader

Du kan beräkna kostnaderna för kognitiv sökning, indexering, börjar du med en uppfattning om hur en genomsnittlig dokumentet ser ut så att du kan köra några siffror. Du kan till exempel ungefärlig:

+ 1 000 PDF-filer.
+ Sex sidor varje.
+ En bild per sida (6 000 bilder).
+ 3 000 tecken per sida.

Anta att en pipeline som består av dokumentknäckning av varje PDF, bild- och -utvinning, optisk teckenläsning (OCR) av avbildningar och entitetsidentifiering av organisationer.

Priserna som visas i den här artikeln är hypotetiska. De används för att illustrera uppskattningsberäkningen. Dina kostnader kan vara lägre. De faktiska priserna för transaktioner och finns [priser för Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).

1. För document cracking-med text- och innehåll, är textextrahering för närvarande kostnadsfritt. 6 000 bilder, förutsätter i $1 för varje 1 000 bilder som extraheras. Det är en kostnad av $6.00 för det här steget.

2. För OCR av 6 000 bilder på engelska använder OCR kognitiva kunskaper den bästa algoritmen (DescribeText). Om vi antar att en kostnad av $2.50 per 1 000 bilder analyseras, skulle du betalar $15,00 för det här steget.

3. För entitetextrahering har du totalt tre text poster per sida. Varje post är 1 000 tecken. Tre textposter per sida multipliceras med 6 000 sidor är lika med 18 000 textposter. Om vi antar att $2.00 per 1 000 textposter 36,00 kosta i det här steget.

Sätter samman allt, skulle du betalar om $57.00 att mata in 1 000 PDF-dokument för den här typen med beskrivs kompetens.

## <a name="next-steps"></a>Nästa steg
+ [Azure Search sidan med priser](https://azure.microsoft.com/pricing/details/search/)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Skapa kompetens (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Avancerad och mappning](cognitive-search-output-field-mapping.md)
