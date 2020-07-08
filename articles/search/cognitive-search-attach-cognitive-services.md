---
title: Bifoga Cognitive Services till en färdigheter
titleSuffix: Azure Cognitive Search
description: Instruktioner för att bifoga en Cognitive Services allt-i-ett-prenumeration till en AI-pipeline i Azure Kognitiv sökning.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: ffb52cd34d56c227fc689ea70146e99983e17c0d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564443"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Bifoga en Cognitive Services resurs till en färdigheter i Azure Kognitiv sökning 

När du konfigurerar en pipeline för anrikning i Azure Kognitiv sökning kan du utöka ett begränsat antal dokument kostnads fritt. För större och mer frekventa arbets belastningar bör du koppla en fakturerbar Cognitive Services-resurs.

I den här artikeln får du lära dig hur du kopplar en resurs genom att tilldela en nyckel till en färdigheter som definierar en pipeline.

## <a name="resources-used-during-enrichment"></a>Resurser som används under anrikning

Azure Kognitiv sökning har ett beroende på Cognitive Services, inklusive [visuellt innehåll](https://azure.microsoft.com/services/cognitive-services/computer-vision/) för bild analys och optisk tecken IGENKÄNNING (OCR), [textanalys](https://azure.microsoft.com/services/cognitive-services/text-analytics/) för bearbetning av naturligt språk och andra berikare som [text översättning](https://azure.microsoft.com/services/cognitive-services/translator-text-api/). I samband med berikning i Azure Kognitiv sökning omsluts dessa AI-algoritmer inuti en *färdighet*, placeras i en *färdigheter*och refereras till av en *indexerare* under indexeringen.

## <a name="how-billing-works"></a>Så fungerar debiteringen

+ Azure Kognitiv sökning använder den Cognitive Services resurs nyckel som du anger på en färdigheter för att fakturera för avbildning och text-anrikning. Att köra fakturerbara färdigheter är på [Cognitive Services betala per användning-pris](https://azure.microsoft.com/pricing/details/cognitive-services/).

+ Avbildnings extrahering är en åtgärd i Azure Kognitiv sökning som inträffar när dokumenten har knäckts innan de berikas. Avbildnings extrahering är fakturerbart. För priser för avbildnings extrahering, se [prissättnings sidan för Azure kognitiv sökning](https://azure.microsoft.com/pricing/details/search/).

+ Text extrahering sker även under dokumentets cracking-fras. Det är inte fakturerbart.

+ Färdigheter som inte anropar Cognitive Services, inklusive villkor för villkorlig, formaren, text koppling och text delning, är inte fakturerbara.

## <a name="same-region-requirement"></a>Krav för samma region

Vi kräver att Azure Kognitiv sökning och Azure Cognitive Services finns inom samma region. Annars får du det här meddelandet vid körning:`"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

Det finns inget sätt att flytta en tjänst mellan regioner. Om du får det här felet bör du skapa en ny Cognitive Services-resurs i samma region som Azure Kognitiv sökning.

> [!NOTE]
> Vissa inbyggda kunskaper baseras på icke-regionala Cognitive Services (till exempel [kunskap om text översättning](cognitive-search-skill-text-translation.md)). Om du använder en icke-regional färdighet innebär det att din begäran kan servas i en annan region än Azure Kognitiv sökning region. Mer information icke-regionala tjänster finns på sidan [Cognitive Services produkt per region](https://aka.ms/allinoneregioninfo) .

## <a name="use-free-resources"></a>Använd kostnads fria resurser

Du kan använda ett begränsat alternativ för fri bearbetning för att slutföra självstudierna om AI-anrikning och snabb starts övningar.

Kostnads fria (begränsade berikade) resurser är begränsade till 20 dokument per dag, per indexerare. Du kan ta bort och återskapa indexeraren för att återställa räknaren.

1. Öppna guiden Importera data:

   ![Öppna guiden Importera data](media/search-get-started-portal/import-data-cmd.png "Öppna guiden Importera data")

1. Välj en data källa och fortsätt att **lägga till AI-anrikning (valfritt)**. En stegvis genom gång av den här guiden finns i [skapa ett index i Azure Portal](search-get-started-portal.md).

1. Expandera **bifoga Cognitive Services** och välj sedan **ledigt (begränsade anrikninger)**:

   ![Utöka Cognitive Services avsnittet](./media/cognitive-search-attach-cognitive-services/attach1.png "Utöka Cognitive Services avsnittet")

1. Nu kan du fortsätta till nästa steg, inklusive **lägga till kognitiva kunskaper**.

## <a name="use-billable-resources"></a>Använd fakturerbara resurser

För arbets belastningar som skapar fler än 20 berikningar per dag, se till att koppla en fakturerbar Cognitive Services-resurs. Vi rekommenderar att du alltid bifogar en fakturerbar Cognitive Services resurs, även om du aldrig avser att anropa API:er för Cognitive Services. Om du lägger till en resurs åsidosätts den dagliga gränsen.

Du debiteras bara för färdigheter som anropar API:er för Cognitive Services. Du debiteras inte för [anpassade kunskaper](cognitive-search-create-custom-skill-example.md)eller kunskaper som [text sammanslagning](cognitive-search-skill-textmerger.md), [text delning](cognitive-search-skill-textsplit.md)och [formaren](cognitive-search-skill-shaper.md), som inte är API-baserade.

1. Öppna guiden Importera data, Välj en data källa och fortsätt att **lägga till AI-anrikning (valfritt)**.

1. Expandera **bifoga Cognitive Services** och välj sedan **Skapa ny Cognitive Services resurs**. En ny flik öppnas så att du kan skapa resursen:

   ![Skapa en -resurs för Cognitive Services](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Skapa en -resurs för Cognitive Services")

1. I listan **plats** väljer du den region där din Azure kognitiv sökning-tjänst finns. Se till att använda den här regionen av prestanda skäl. Om du använder den här regionen annulleras även utgående bandbredds avgifter mellan regioner.

1. I listan **pris nivå** väljer du **S0** för att hämta en allt-i-ett-samling med Cognitive Services funktioner, inklusive funktioner för vision och språk som backar upp de inbyggda kunskaper som tillhandahålls av Azure kognitiv sökning.

   För S0-nivån kan du hitta priser för vissa arbets belastningar på [sidan Cognitive Services prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/).
  
   + I listan **Välj erbjudande** kontrollerar du att **Cognitive Services** har marker ATS.
   + Under **språk** funktioner gäller priserna för **TEXTANALYS standard** för AI-indexering.
   + Under **vision** -funktioner gäller priserna för **visuellt innehåll S1** .

1. Välj **skapa** för att etablera den nya Cognitive Services resursen.

1. Gå tillbaka till föregående flik, som innehåller guiden Importera data. Välj **Uppdatera** för att Visa Cognitive Services resursen och välj sedan resursen:

   ![Välj den Cognitive Services resursen](./media/cognitive-search-attach-cognitive-services/attach2.png "Välj den Cognitive Services resursen")

1. Expandera avsnittet **Lägg till kognitiva kunskaper** och välj de speciella kognitiva färdigheter som du vill köra på dina data. Slutför resten av guiden.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Koppla en befintlig färdigheter till en Cognitive Services-resurs

Om du har en befintlig färdigheter kan du koppla den till en ny eller annan Cognitive Services resurs.

1. På sidan **tjänst översikt** väljer du **färdighetsuppsättningar**:

   ![Fliken färdighetsuppsättningar](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Fliken färdighetsuppsättningar")

1. Välj namnet på färdigheter och välj sedan en befintlig resurs eller skapa en ny. Klicka på **OK** för att bekräfta ändringarna.

   ![Resurs lista för färdigheter](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "Resurs lista för färdigheter")

   Kom ihåg att alternativet **kostnads fri (begränsad berikande)** begränsar dig till 20 dokument varje dag och att du kan använda **Skapa ny Cognitive Services resurs** för att etablera en ny fakturerbar resurs. Om du skapar en ny resurs väljer du **Uppdatera** för att uppdatera listan över Cognitive Services resurser och väljer sedan resursen.

## <a name="attach-cognitive-services-programmatically"></a>Bifoga Cognitive Services program mässigt

När du definierar färdigheter program mässigt lägger du till ett `cognitiveServices` avsnitt i färdigheter. I avsnittet inkluderar du nyckeln för den Cognitive Services resurs som du vill associera med färdigheter. Kom ihåg att resursen måste finnas i samma region som din Azure Kognitiv sökning-resurs. Ta även med `@odata.type` och ange det som `#Microsoft.Azure.Search.CognitiveServicesByKey` .

I följande exempel visas det här mönstret. Lägg märke till `cognitiveServices` avsnittet i slutet av definitionen.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
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

## <a name="example-estimate-costs"></a>Exempel: uppskatta kostnader

Om du vill beräkna de kostnader som är kopplade till kognitiv söknings indexering börjar du med en uppfattning om hur mycket ett genomsnittligt dokument ser ut så att du kan köra vissa tal. Du kan till exempel approximera:

+ 1 000 PDF-filer.
+ Sex sidor var.
+ En bild per sida (6 000 bilder).
+ 3 000 tecken per sida.

Anta en pipeline som består av dokument sprickor för varje PDF, bild-och text extrahering, OCR (optisk tecken läsning) och enhets igenkänning av organisationer.

Priserna som visas i den här artikeln är hypotetiska. De används för att illustrera uppskattnings processen. Dina kostnader kan vara lägre. De faktiska priserna för transaktioner finns i se [Cognitive Services priser](https://azure.microsoft.com/pricing/details/cognitive-services).

1. För att dokument knäckas med text-och bild innehåll är text extraheringen för närvarande kostnads fri. För 6 000-avbildningar antar du att $1 för varje 1 000-avbildning har extraherats. Detta är en kostnad på $6,00 för det här steget.

2. För OCR av 6 000-bilder på engelska använder funktionen för att använda den bästa algoritmen (DescribeText). Om du antar att en kostnad på $2,50 per 1 000-avbildning ska analyseras betalar du $15,00 för det här steget.

3. För enhets extrahering har du totalt tre text poster per sida. Varje post är 1 000 tecken. Tre text poster per sida multiplicerat med 6 000 sidor är lika med 18 000 text poster. Om du antar $2,00 per 1 000 text poster kostar det här steget $36,00.

Genom att sätta ihop allt, betalar du om $57,00 för att mata in 1 000 PDF-dokument av den här typen med den beskrivna färdigheter.

## <a name="next-steps"></a>Nästa steg
+ [Sidan prissättning för Azure Kognitiv sökning](https://azure.microsoft.com/pricing/details/search/)
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Skapa färdigheter (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Så här mappar du omfattande fält](cognitive-search-output-field-mapping.md)
