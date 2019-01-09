---
title: Associera en Cognitive Services-resurs med en kompetens – Azure Search
description: Anvisningar för att bifoga en allt-i-ett Cognitive Services-prenumeration för en pipeline med kognitiva funktioner i Azure Search.
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 01/07/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 509125e7c93f34b9ce28c58cb1ec96db1074d995
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119653"
---
# <a name="associate-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Associera en Cognitive Services-resurs med en kompetens i Azure Search 

AI-algoritmer som driver [kognitiv sökning](cognitive-search-concept-intro.md) pipelines för bearbetning av Ostrukturerade data som är baserade på [ **Cognitive Services-resurser**](https://azure.microsoft.com/services/cognitive-services/). Resurser som [ **visuellt** ](https://azure.microsoft.com/services/cognitive-services/computer-vision/) ger bildanalys och optisk teckenläsning (OCR) extraherar text och struktur utanför bildfiler, medan [ **Text Analytics** ](https://azure.microsoft.com/services/cognitive-services/text-analytics/) ger naturlig språkbearbetning, till exempel entitet erkännande och nyckeln diskussionsämne, att nämna några.

Du kan utöka ett begränsat antal dokument kostnadsfritt eller du kan koppla en fakturerbar Cognitive Services-resurs för större och mer frekventa arbetsbelastningar. I den här artikeln får du lära dig hur du associerar en resurs för Cognitive Services med dina cognitive färdigheter och utöka data under indexering.

Om din pipeline består uteslutande av [anpassade funktioner](cognitive-search-create-custom-skill-example.md), du behöver inte att koppla en resurs för Cognitive Services.

> [!NOTE]
> Från och med den 21 December 2018 kan du associera en resurs för Cognitive Services med ett Azure Search-kompetens. På så sätt kan vi ta betalt för körning av kompetens. På det här datumet måste också började vi debitera för extrahering av avbildningen som en del av dokumentknäckning fasen. Textextrahering från dokument fortsätter att erbjudas utan extra kostnad.
>
> Körningen av [inbyggda kognitiva kunskaper](cognitive-search-predefined-skills.md) som ingår debiteras enligt de [Cognitive Services betala-som-du gå pris](https://azure.microsoft.com/pricing/details/cognitive-services/), på samma pris som om du har utfört uppgiften oberoende av Azure Search. Bild extrahering priser som ingår debiteras enligt priserna för förhandsversionen och beskrivs i den [Azure Search sidan med priser](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="use-free-resources"></a>Använd kostnadsfria resurser

Du kan använda en begränsad, kostnadsfri Bearbetningsalternativ som ger dig den 20 dokumentet enrichments per dag, räcker för att slutföra självstudien och quickstart övningarna för kognitiv sökning. 

> [!Important]
> Startar den 1 februari 2019 den **kostnadsfri (begränsad Enrichments)** är begränsad till 20 dokument per dag. 

1. Öppna den **dataimport** guiden.

   ![Kommandot Importera data](media/search-get-started-portal/import-data-cmd2.png "kommandot Importera data")

1. Välj en datakälla och fortsätta att **Lägg till kognitiv sökning (valfritt)**. En stegvis genomgång av den här guiden finns [Import, index och fråga med hjälp av portal-verktyg](search-get-started-portal.md).

1. Expandera **bifoga Cognitive Services** och välj **kostnadsfri (begränsad Enrichments)**.

   ![Expanderad bifoga Cognitive Services avsnittet](./media/cognitive-search-attach-cognitive-services/attach1.png "expanderas bifoga Cognitive Services")

Fortsätt till nästa steg **lägga till enrichments**. En beskrivning av färdigheter som är tillgänglig i portalen finns i [”steg 2: Lägg till kognitiva kunskaper ”](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline) i snabbstarten kognitiv sökning.

## <a name="use-billable-resources"></a>Använd fakturerbara resurser

För arbetsbelastningar numrering av fler än 20 dokument dagligen, behöver du en fakturerbar resurs för Cognitive Services.

1. I den **dataimport** guiden i **bifoga Cognitive Services**, Välj en befintlig resurs eller klicka på **Skapa ny resurs för Cognitive Services**.

1. För **Skapa ny resurs för Cognitive Services**, en ny flik öppnas så att du kan skapa resursen. Ge ett unikt namn för resursen.

1. Välj samma plats som Azure Search. För närvarande stöds kognitiva funktioner som indexering i dessa regioner:

  * Västra centrala USA
  * Södra centrala USA
  * Östra USA
  * USA, östra 2
  * Västra USA 2
  * Centrala Kanada
  * Västra Europa
  * Storbritannien, södra
  * Norra Europa
  * Södra Brasilien
  * Sydostasien
  * Indien, centrala
  * Östra Australien

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
