---
title: Associera en Cognitive Services-resurs med en kompetens i Azure Search
description: Instruktioner för att bifoga en allt-i-ett Cognitive Services-prenumeration till en kognitiva kunskaper i Azure Search
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 52efa685bba330879365f56e547881d62a52a185
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000163"
---
# <a name="associate-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Associera en Cognitive Services-resurs med en kompetens i Azure Search 

Kognitiv sökning extraherar och förbättra data så att den blir sökbar i Azure Search. Vi kallar extrahering och berikande *kognitiva kunskaper*. Uppsättning kompetenser anropas under indexering av innehåll som har definierats i en *kompetens*. En kompetens kan använda [fördefinierade kunskaper](cognitive-search-predefined-skills.md) eller anpassade funktioner. Mer information finns i [exempel: skapa en anpassad färdighet](cognitive-search-create-custom-skill-example.md).

I den här artikeln får du lära dig hur du associerar en [Azure Cognitive Services ](https://azure.microsoft.com/services/cognitive-services/) resurs med din cognitive kompetens.

Cognitive Services-resurs som du väljer kommer power inbyggda kognitiva funktioner. Den här resursen används också för fakturering. Alla enrichments som du utför med hjälp av inbyggda kognitiva funktioner kommer att debiteras mot Cognitive Services-resurs som du väljer. De kommer att faktureras enligt samma taxa som om du har utfört samma uppgift med hjälp av en resurs för Cognitive Services. Se [priser för Cognitive Service](https://azure.microsoft.com/pricing/details/cognitive-services/).

> [!NOTE]
> Från och med den 21 December 2018 kan du associera en resurs för Cognitive Services med ett Azure Search-kompetens. På så sätt kan vi ta betalt för körning av kompetens. På det här datumet måste också började vi debitera för extrahering av avbildningen som en del av dokumentknäckning fasen. Textextrahering från dokument fortsätter att erbjudas utan extra kostnad.
>
> Körningen av inbyggda färdigheter som ingår debiteras enligt de [Cognitive Services betala-som-du gå pris](https://azure.microsoft.com/pricing/details/cognitive-services/). Bild extrahering priser som ingår debiteras enligt priserna för förhandsversionen och beskrivs i den [Azure Search sidan med priser](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="limits-when-no-cognitive-services-resource-is-selected"></a>Begränsningar när ingen resurs för Cognitive Services är vald
Med början den 1 februari 2019 om du inte associerar en Cognitive Services-prenumeration med din kompetens kan kommer du att kunna utöka endast ett litet antal dokument för kostnadsfri (20 dokument per dag). 

## <a name="associate-a-cognitive-services-resource-with-a-new-skillset"></a>Koppla en resurs för Cognitive Services till en ny kompetens

1. Som en del av den [dataimport](search-import-data-portal.md) guiden när du ansluter till din datakälla, gå till den **Lägg till kognitiv sökning** valfritt steg. Det här är det andra steget i guiden.

1. Expandera den **bifoga Cognitive Services** avsnittet. Det här steget visar alla Cognitive Services-resurser som du har i samma regioner som Azure Search-tjänsten.

   ![Expanderad bifoga Cognitive Services avsnittet](./media/cognitive-search-attach-cognitive-services/attach1.png "expanderas bifoga Cognitive Services")

1. Välj en befintlig resurs för Cognitive Services, eller välj **Skapa ny resurs för Cognitive Services**. Om du väljer **kostnadsfri (begränsad Enrichments)**, du kommer att kunna utöka endast ett litet antal dokument för kostnadsfri (20 dokument per dag). Om du väljer **Skapa ny resurs för Cognitive Services**, en ny flik öppnas där du kan skapa resursen. 

1. Om du har skapat en ny resurs, Välj **uppdatera** att uppdatera listan över resurser för Cognitive Services och välj sedan resursen. 

   ![Valt resurs för Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach2.png "valt Cognitive Service-resurs")

1. Expandera den **lägga till Enrichments** om du vill välja de specifika kognitiva kunskaper som du vill köra över dina data och fortsätta med resten av flödet.

## <a name="associate-a-cognitive-services-resource-with-an-existing-skillset"></a>Associera en Cognitive Services-resurs med en kunskaper

1. På den **tjänstöversikt** väljer den **kompetens** fliken.

   ![Kompetens fliken](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "kompetens fliken")

1. Välj de kunskaper som du vill ändra. Det här steget öppnas ett blad där du kan redigera en kompetens.

1. Välj en befintlig resurs för Cognitive Services, eller välj **Skapa ny resurs för Cognitive Services**. Om du väljer **kostnadsfri (begränsad Enrichments)**, du kommer att kunna utöka endast ett litet antal dokument för kostnadsfri (20 dokument per dag). Om du väljer **Skapa ny resurs för Cognitive Services**, en ny flik öppnas där du kan skapa resursen.

   <n/> 
   <img src="./media/cognitive-search-attach-cognitive-services/attach-existing2.png" width="350">

1. Om du har skapat en ny resurs, Välj **uppdatera** att uppdatera listan över resurser för Cognitive Services och välj sedan resursen.

1. Välj **OK** att bekräfta ändringarna.

## <a name="associate-a-cognitive-services-resource-programmatically"></a>Associera en resurs för Cognitive Services via programmering

När du definierar gruppens kunskaper avgör programmässigt, lägga till en `cognitiveServices` avsnittet. I avsnittet med nyckeln för Cognitive Services-resursen som du vill associera med kompetens. Även innehålla `@odata.type`, och ge den värdet `#Microsoft.Azure.Search.CognitiveServicesByKey`. I följande exempel visas det här mönstret.

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
        "key": "your key goes here"
    }
}
```
## <a name="example-estimate-the-cost-of-document-cracking-and-enrichment"></a>Exempel: Beräkna kostnaden för dokumentknäckning och berikande
Du kanske vill beräkna hur mycket det kostar för att utöka en typ av dokument. Följande övning är bara ett exempel, men det kan vara till hjälp för dig.

Anta att du har 1 000 PDF-filer. Du uppskattar att i genomsnitt varje dokument har 6 sidor. Varje sida har 1 bild. Det finns i genomsnitt cirka 3 000 tecken per sida. 

Anta att du vill utföra följande steg som en del av din pipeline för berikande:
1. Som en del av dokumentknäckning, extrahera innehåll och bilder från dokumentet.
1. Som en del av berikande, Använd optisk teckenläsning (OCR) för var och en av de extrahera sidorna, kombinera texten för alla sidor och extrahera sedan var och en av organisationer i den kombinerade texten i alla avbildningar.

Nu ska vi beräkna hur mycket det skulle kosta för att mata in de 1 000 dokument, steg för steg:

1. För dokumentknäckning, skulle du extrahera ett kombinerat antal 6 000 bilder. Om vi antar att $1 för varje 1 000 bilder som extraherats som skulle kostar $6.00.

2. Du kan extrahera text från var och en av dessa 6 000 bilder. I engelska använder OCR kognitiva kunskaper den bästa algoritmen (DescribeText). Om vi antar att en kostnad av $2.50 per 1 000 bilder analyseras, skulle du betalar $15,00 för det här steget.

3. För entitetextrahering har du totalt 3 text poster per sida. (Varje post är 1 000 tecken.) Tre textposter per sida * 6 000 sidor = 18 000 textposter. Om vi antar att $2.00 per 1 000 textposter 36,00 kosta i det här steget.

Sätter samman allt betala du $57.00 att mata in 1 000 PDF-dokument av den här typen med beskrivs kompetens. I den här övningen antas vi dyraste priset per transaktion. Det kan ha varit lägre på grund av de blivit priser. Se [priser för Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).



## <a name="next-steps"></a>Nästa steg
+ [Azure Search sidan med priser](https://azure.microsoft.com/pricing/details/search/)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Skapa kompetens (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Avancerad och mappning](cognitive-search-output-field-mapping.md)
