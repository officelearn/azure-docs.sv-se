---
title: Bifoga kognitiva tjänster till en kompetens
titleSuffix: Azure Cognitive Search
description: Instruktioner för att koppla en Cognitive Services allt-i-ett-prenumeration till en AI-anrikningspipeline i Azure Cognitive Search.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 254c912114e3f1c7a495f389bc6a6416cbde7e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472459"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Koppla en Cognitive Services-resurs till en kompetens i Azure Cognitive Search 

När du konfigurerar en anrikningspipeline i Azure Cognitive Search kan du berika ett begränsat antal dokument utan kostnad. För större och mer frekventa arbetsbelastningar bör du bifoga en fakturerbar Cognitive Services-resurs.

I den här artikeln får du lära dig hur du kopplar en resurs genom att tilldela en nyckel till en kompetens som definierar en anrikningspipeline.

## <a name="resources-used-during-enrichment"></a>Resurser som används under anrikning

Azure Cognitive Search har ett beroende av Cognitive Services, inklusive [datorseende](https://azure.microsoft.com/services/cognitive-services/computer-vision/) för bildanalys och optisk teckenigenkänning (OCR), [Textanalys](https://azure.microsoft.com/services/cognitive-services/text-analytics/) för bearbetning av naturligt språk och andra berikanden som [textöversättning](https://azure.microsoft.com/services/cognitive-services/translator-text-api/). I samband med anrikning i Azure Cognitive Search är dessa AI-algoritmer insvepta i en *färdighet*, placeras i en *kompetens*och refereras av en *indexerare* under indexeringen.

## <a name="how-billing-works"></a>Så fungerar debiteringen

+ Azure Cognitive Search använder den cognitive services-resursnyckel som du tillhandahåller på en kompetens för att fakturera för bild- och textberikande. Utförande av fakturerbara färdigheter är på [Cognitive Services pay-as-you go pris](https://azure.microsoft.com/pricing/details/cognitive-services/).

+ Avbildningsextrahering är en Azure Cognitive Search-åtgärd som inträffar när dokument är spruckna före anrikning. Bildutdragning är fakturerbar. Pris för bildextrahering finns på [prissidan](https://go.microsoft.com/fwlink/?linkid=2042400)för Azure Cognitive Search .

+ Textextrahering inträffar också under dokumentsprickningsfrasen. Det är inte fakturerbart.

+ Kunskaper som inte anropar Cognitive Services, inklusive kunskaper om villkorlig, shaper, textsammanfogning och textdelning, kan inte faktureras.

## <a name="same-region-requirement"></a>Krav i samma region

Vi kräver att Azure Cognitive Search och Azure Cognitive Services finns inom samma region. Annars får du det här meddelandet vid körning:`"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Det finns inget sätt att flytta en tjänst mellan regioner. Om du får det här felet bör du skapa en ny Cognitive Services-resurs i samma region som Azure Cognitive Search.

> [!NOTE]
> Vissa inbyggda färdigheter baseras på icke-regionala Cognitive Services (till exempel [Textöversättningsfärdigheten).](cognitive-search-skill-text-translation.md) Att använda en icke-regional färdighet innebär att din begäran kan servas i en annan region än Azure Cognitive Search-regionen. Mer information om icke-regionala tjänster finns på sidan [Cognitive Services-produkt efter region.](https://aka.ms/allinoneregioninfo)

## <a name="use-free-resources"></a>Använd lediga resurser

Du kan använda en begränsad, gratis bearbetning alternativ för att slutföra AI anrikning handledning och snabbstart övningar.

Gratis (begränsade anrikning) resurser är begränsade till 20 dokument per dag, per indexerare. Du kan ta bort och återskapa indexeraren för att återställa räknaren.

1. Öppna guiden Importera data:

   ![Öppna guiden Importera data](media/search-get-started-portal/import-data-cmd.png "Öppna guiden Importera data")

1. Välj en datakälla och fortsätt **att lägga till AI-anrikning (valfritt)**. En steg-för-steg-genomgång av den här guiden finns [i Skapa ett index i Azure-portalen](search-get-started-portal.md).

1. Expandera **Bifoga Cognitive Services** och välj sedan Gratis **(Begränsad anrikning):**

   ![Expanderad bifoga Cognitive Services avsnitt](./media/cognitive-search-attach-cognitive-services/attach1.png "Expanderad bifoga Cognitive Services avsnitt")

1. Du kan nu fortsätta till nästa steg, inklusive **Lägg till kognitiva färdigheter**.

## <a name="use-billable-resources"></a>Använda fakturerbara resurser

För arbetsbelastningar som skapar mer än 20 anrikningar per dag, se till att bifoga en fakturerbar Cognitive Services-resurs. Vi rekommenderar att du alltid bifogar en fakturerbar Cognitive Services-resurs, även om du aldrig tänker anropa Cognitive Services API:er. Om du ansluter en resurs åsidosätts den dagliga gränsen.

Du debiteras bara för färdigheter som anropar Api:erna för Cognitive Services. Du faktureras inte för [anpassade kunskaper](cognitive-search-create-custom-skill-example.md)eller färdigheter som [textsammanslagning,](cognitive-search-skill-textmerger.md) [textdelare](cognitive-search-skill-textsplit.md)och [shaper](cognitive-search-skill-shaper.md), som inte är API-baserade.

1. Öppna guiden Importera data, välj en datakälla och fortsätt **att lägga till AI-anrikning (valfritt)**.

1. Expandera **Bifoga Cognitive Services** och välj sedan Skapa ny Cognitive **Services-resurs**. En ny flik öppnas så att du kan skapa resursen:

   ![Skapa en -resurs för Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Skapa en -resurs för Cognitive Services")

1. Välj den region där din Azure Cognitive Search-tjänst finns i listan **Plats.** Se till att använda den här regionen av prestandaskäl. Om du använder den här regionen annulleras också utgående bandbreddsavgifter i olika regioner.

1. I listan **Prisnivå** väljer du **S0** för att hämta allt-i-ett-funktionerna med Cognitive Services-funktioner, inklusive funktionerna Vision och språk som stöder de inbyggda färdigheterna som tillhandahålls av Azure Cognitive Search.

   För S0-nivån kan du hitta priser för specifika arbetsbelastningar på [sidan Cognitive Services-priser](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + Kontrollera att **Cognitive Services** är markerat i listan **Välj erbjudande.**
   + Under **Språkfunktioner** gäller priserna för **Text Analytics Standard** för AI-indexering.
   + Under **Vision-funktioner** gäller priserna för **Computer Vision S1.**

1. Välj **Skapa** för att etablera den nya Cognitive Services-resursen.

1. Gå tillbaka till föregående flik, som innehåller guiden Importera data. Välj **Uppdatera** om du vill visa Cognitive Services-resursen och välj sedan resursen:

   ![Välj cognitive services-resursen](./media/cognitive-search-attach-cognitive-services/attach2.png "Välj cognitive services-resursen")

1. Expandera avsnittet **Lägg till kognitiva färdigheter** för att välja de specifika kognitiva färdigheter som du vill köra på dina data. Slutför resten av guiden.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Koppla en befintlig kompetens till en Cognitive Services-resurs

Om du har en befintlig kompetens kan du koppla den till en ny eller annan Cognitive Services-resurs.

1. På sidan **Serviceöversikt** väljer du **Kunskaper:**

   ![Fliken Skillsets](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Fliken Skillsets")

1. Välj namnet på kompetensen och välj sedan en befintlig resurs eller skapa en ny. Välj **OK** för att bekräfta ändringarna.

   ![Resurslista för kunskaper](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Resurslista för kunskaper")

   Kom ihåg att alternativet **Kostnadsfria (begränsade anrikningar)** begränsar dig till 20 dokument dagligen och att du kan använda **Skapa ny Cognitive Services-resurs** för att etablera en ny fakturerbar resurs. Om du skapar en ny resurs väljer du **Uppdatera** för att uppdatera listan över Cognitive Services-resurser och väljer sedan resursen.

## <a name="attach-cognitive-services-programmatically"></a>Bifoga Cognitive Services programmässigt

När du definierar kompetensen programmässigt lägger `cognitiveServices` du till ett avsnitt i kompetensen. I det avsnittet tar du med nyckeln till den Cognitive Services-resurs som du vill associera med kompetensen. Kom ihåg att resursen måste finnas i samma region som din Azure Cognitive Search-resurs. Inkludera `@odata.type`även och ställ `#Microsoft.Azure.Search.CognitiveServicesByKey`in den på .

Följande exempel visar det här mönstret. Lägg `cognitiveServices` märke till avsnittet i slutet av definitionen.

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

## <a name="example-estimate-costs"></a>Exempel: Uppskatta kostnader

Om du vill uppskatta kostnaderna för kognitiv sökning indexering, börja med en uppfattning om hur ett genomsnittligt dokument ser ut så att du kan köra några tal. Du kan till exempel approximera:

+ 1 000 PDF-filer.
+ Sex sidor vardera.
+ En bild per sida (6 000 bilder).
+ 3 000 tecken per sida.

Anta att en pipeline som består av dokumentsprickning av varje PDF-fil, bild- och textextrahering, optisk teckenigenkänning (OCR) av bilder och entitetsigenkänning av organisationer.

Priserna som visas i denna artikel är hypotetiska. De används för att illustrera uppskattningsprocessen. Dina kostnader kan bli lägre. De faktiska priserna för transaktioner finns i [Priser för Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).

1. För dokumentsprickor med text- och bildinnehåll är textextrahering för närvarande gratis. För 6.000 bilder, anta $ 1 för varje 1000 bilder extraheras. Det är en kostnad på $ 6,00 för detta steg.

2. För OCR på 6 000 bilder på engelska använder OCR:s kognitiva skicklighet den bästa algoritmen (DescribeText). Förutsatt att en kostnad på $ 2,50 per 1000 bilder som ska analyseras, skulle du betala $ 15,00 för detta steg.

3. För entitetsextrahering har du totalt tre textposter per sida. Varje post är 1 000 tecken. Tre textposter per sida multiplicerat med 6 000 sidor motsvarar 18 000 textposter. Förutsatt $ 2,00 per 1000 textposter, skulle detta steg kostar $ 36,00.

Sätta ihop allt, skulle du betala ca $ 57,00 för att inta 1.000 PDF-dokument av denna typ med den beskrivna skillset.

## <a name="next-steps"></a>Nästa steg
+ [Prissida för Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Skapa skillset (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Så här mappar du berikade fält](cognitive-search-output-field-mapping.md)
