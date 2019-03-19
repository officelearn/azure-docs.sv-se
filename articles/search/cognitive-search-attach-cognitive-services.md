---
title: Bifoga en Cognitive Services-resurs med en kompetens – Azure Search
description: Anvisningar för att bifoga en allt-i-ett Cognitive Services-prenumeration för en pipeline med kognitiva funktioner i Azure Search.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: d5fdae09055f922fe9783f6eb074457af12c60df
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880423"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Bifoga en Cognitive Services-resurs med en kompetens i Azure Search 

AI-algoritmer enheten den [kognitiv sökning pipelines](cognitive-search-concept-intro.md) används för att behandla Ostrukturerade data i ett Azure Search indexering igen. Dessa algoritmer är baserade på [Cognitive Services-resurser](https://azure.microsoft.com/services/cognitive-services/), inklusive [visuellt](https://azure.microsoft.com/services/cognitive-services/computer-vision/) för bildanalys och optisk teckenläsning (OCR) och [textanalys](https://azure.microsoft.com/services/cognitive-services/text-analytics/)för igenkänning av entiteter, extrahering av diskussionsämne och andra enrichments.

Du kan utöka ett begränsat antal dokument kostnadsfritt eller bifoga en fakturerbar Cognitive Services-resurs för större och mer frekventa arbetsbelastningar. I den här artikeln lär du dig hur du associerar en resurs för Cognitive Services med dina cognitive färdigheter och utöka data under [Azure sökindexering](search-what-is-an-index.md).

Om din pipeline består av kunskaper som inte är relaterade till Cognitive Services API: er, bör du fortfarande koppla en resurs för Cognitive Services. Gör så åsidosättningar i **kostnadsfri** resurs som begränsar du en liten mängd enrichments per dag. Det finns ingen kostnad för färdigheter som inte är bundna till Cognitive Services API: er. Inkludera dessa kunskaper: [anpassade funktioner](cognitive-search-create-custom-skill-example.md), [text fusion](cognitive-search-skill-textmerger.md), [text delare](cognitive-search-skill-textsplit.md), och [formaren](cognitive-search-skill-shaper.md).

> [!NOTE]
> Från och med den 21 December 2018 kan du associera en resurs för Cognitive Services med ett Azure Search-kompetens. På så sätt kan vi ta betalt för körning av kompetens. På det här datumet måste också började vi debitera för extrahering av avbildningen som en del av dokumentknäckning fasen. Textextrahering från dokument fortsätter att erbjudas utan extra kostnad.
>
> [Inbyggda kognitiva kunskaper](cognitive-search-predefined-skills.md) körning som ingår debiteras enligt de [Cognitive Services betala-som-du gå pris](https://azure.microsoft.com/pricing/details/cognitive-services), på samma pris som om du har utfört uppgiften direkt. Extrahering av avbildningen är en Azure Search-avgift kan för närvarande på priset för förhandsversionen. Mer information finns i [Azure Search prissättningssidan](https://go.microsoft.com/fwlink/?linkid=2042400) eller [hur debiteringen fungerar](search-sku-tier.md#how-billing-works).


## <a name="use-free-resources"></a>Använd kostnadsfria resurser

Du kan använda en begränsad, kostnadsfri bearbetningsalternativ för att slutföra självstudien och quickstart övningarna kognitiv sökning. 

> [!Important]
> Från och med 1 februari 2019 den **kostnadsfri (begränsad Enrichments)** begränsas till 20 dokument per dag. 

1. Öppna den **dataimport** guiden.

   ![Kommandot Importera data](media/search-get-started-portal/import-data-cmd2.png "kommandot Importera data")

1. Välj en datakälla och fortsätta att **Lägg till kognitiv sökning (valfritt)**. En stegvis genomgång av den här guiden finns [Import, index och fråga med hjälp av portal-verktyg](search-get-started-portal.md).

1. Expandera **bifoga Cognitive Services** och välj **kostnadsfri (begränsad Enrichments)**.

   ![Expanderad bifoga Cognitive Services avsnittet](./media/cognitive-search-attach-cognitive-services/attach1.png "expanderas bifoga Cognitive Services")

Fortsätt till nästa steg **lägga till enrichments**. En beskrivning av färdigheter som är tillgänglig i portalen finns i [”steg 2: Lägg till kognitiva kunskaper ”](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) i snabbstarten kognitiv sökning.

## <a name="use-billable-resources"></a>Använd fakturerbara resurser

För arbetsbelastningar numrering fler än 20 enrichments varje dag, måste du bifoga en fakturerbar resurs för Cognitive Services. 

Du debiteras endast för färdigheter som anropar API: er för Cognitive Services. Icke-API-baserade färdigheter som [anpassade funktioner](cognitive-search-create-custom-skill-example.md), [text fusion](cognitive-search-skill-textmerger.md), [text delare](cognitive-search-skill-textsplit.md), och [formaren](cognitive-search-skill-shaper.md) kunskaper faktureras inte.

1. I den **dataimport** guiden i **bifoga Cognitive Services**, Välj en befintlig resurs eller klicka på **Skapa ny resurs för Cognitive Services**.

1. För **Skapa ny resurs för Cognitive Services**, en ny flik öppnas så att du kan skapa resursen. Ge ett unikt namn för resursen.

1. Om du skapar en ny resurs för Cognitive Services, **välja samma region** som din Azure Search-resurs.

1. Välj prisnivå allt-i-ett **S0**. Den här nivån tillhandahåller funktioner för visuellt innehåll och språk som stöder de fördefinierade kunskaper i kognitiv sökning.

    ![Skapa en ny resurs för Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "skapa en ny resurs för Cognitive Services")

1. Klicka på **skapa** att etablera den nya Cognitive Services-resursen. 

1. Gå tillbaka till den föregående fliken som innehåller den **dataimport** guiden. Klicka på **uppdatera** att visa resurs för Cognitive Services och välj sedan resursen.

   ![Valt resurs för Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "valt Cognitive Service-resurs")

1. Expandera den **lägga till Enrichments** om du vill välja de specifika kognitiva kunskaper som du vill köra över dina data och fortsätta med resten av flödet. En beskrivning av färdigheter som är tillgänglig i portalen finns i [”steg 2: Lägg till kognitiva kunskaper ”](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) i snabbstarten kognitiv sökning.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Koppla en kunskaper till en resurs för Cognitive Services

Om du har en kunskaper kan koppla du den till en ny eller annan Cognitive Services-resurs.

1. På den **tjänstöversikt** klickar du på **kompetens**.

   ![Kompetens fliken](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "kompetens fliken")

1. Klicka på namnet på kompetens, och välj en befintlig resurs eller skapa en ny. Klicka på **OK** att bekräfta ändringarna. 

   ![Kompetens resurslistan](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "kompetens resurslistan")

Kom ihåg att **kostnadsfri (begränsad Enrichments)** är begränsat till 20 dokument varje dag, och att **Skapa ny resurs för Cognitive Services** används för att etablera en ny fakturerbar resurs. Om du skapar en ny resurs, Välj **uppdatera** att uppdatera listan över resurser för Cognitive Services och välj sedan resursen.

## <a name="attach-cognitive-services-programmatically"></a>Lägg till kognitiva tjänster programmässigt

När du definierar gruppens kunskaper avgör programmässigt, lägga till en `cognitiveServices` att gruppens kunskaper avgör. I avsnittet med nyckeln för Cognitive Services-resursen som du vill associera med kompetens. Kom ihåg att resursen måste vara i samma region som Azure Search. Även innehålla `@odata.type`, och ge den värdet `#Microsoft.Azure.Search.CognitiveServicesByKey`. 

I följande exempel visas det här mönstret. Lägg märke till cognitiveServices-avsnitt i slutet av definitionen

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
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

Du kan beräkna kostnaderna för kognitiv sökning, indexering, börjar du med en uppfattning om hur en genomsnittlig dokumentet ser ut så att du kan köra några siffror. Till exempel för kostnadsuppskattning, du kan göra en uppskattning av:

+ 1000 PDF-filer
+ Sex sidor
+ En avbildning per sida (6000 bilder)
+ 3000 tecken per sida

Anta att en pipeline som består av dokumentet cracking av varje PDF med bild- och extrahering, optisk teckenläsning (OCR) av bilder, och som har namnet entitetsidentifiering av organisationer. 

I den här övningen använder vi dyraste priset per transaktion. Faktiska kostnader kan vara lägre på grund av graderad priser. Se [priser för Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).

1. För document cracking-med text- och innehåll, är textextrahering för närvarande kostnadsfritt. 6000 avbildningar, förutsätter i $1 för varje 1 000 bilder extraherats kostar $6.00 för det här steget.

2. För OCR av 6 000 bilder på engelska använder OCR kognitiva kunskaper den bästa algoritmen (DescribeText). Om vi antar att en kostnad av $2.50 per 1 000 bilder analyseras, skulle du betalar $15,00 för det här steget.

3. För entitetextrahering har du totalt 3 text poster per sida. Varje post är 1 000 tecken. Tre textposter per sida * 6 000 sidor = 18 000 textposter. Om vi antar att $2.00 per 1 000 textposter 36,00 kosta i det här steget.

Sätter samman allt, skulle du betalar om $57.00 att mata in 1 000 PDF-dokument av den här typen med beskrivs kompetens. 

## <a name="next-steps"></a>Nästa steg
+ [Azure Search sidan med priser](https://azure.microsoft.com/pricing/details/search/)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Skapa kompetens (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Avancerad och mappning](cognitive-search-output-field-mapping.md)
