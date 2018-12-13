---
title: Lägg till kognitiva tjänster till din kompetens – Azure Search
description: Instruktioner för att bifoga en allt-i-ett Cognitive Services-prenumeration till en kognitiva kunskaper
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7f604d1b94e51db11e6d6992b7f070d64ae869dd
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317244"
---
# <a name="associate-cognitive-services-resource-with-a-skillset"></a>Koppla resurs för Cognitive Services till en kompetens 

> [!NOTE]
> Från och med den 21 December 2018 kan du associera en resurs för Cognitive Services med ett Azure Search-kompetens. Detta gör att vi börjar debitera för körning av kompetens. På det här datumet måste också börjar vi debitera för extrahering av avbildningen som en del av dokumentknäckning fasen. Textextrahering från dokument fortsätter att erbjudas utan extra kostnad.
>
> Körningen av inbyggda kunskaper debiteras med den befintliga [Cognitive Services betala-som-du gå pris](https://azure.microsoft.com/pricing/details/cognitive-services/) . Bild extrahering priser kommer att debiteras enligt priserna för förhandsversionen och beskrivs i den [Azure Search sidan med priser](https://go.microsoft.com/fwlink/?linkid=2042400).


Kognitiv sökning extraherar och förbättra data så att den blir sökbar i Azure Search. Vi kallar extrahering och berikande *kognitiva kunskaper*. Uppsättning kompetenser anropas under indexering av innehåll som har definierats i en *kompetens*. En kompetens kan använda [fördefinierade kunskaper](cognitive-search-predefined-skills.md) eller anpassade funktioner (se [exempel: skapa en anpassad färdighet](cognitive-search-create-custom-skill-example.md) för mer information).

I den här artikeln får du lära dig hur du associerar den [Cognitive Services ](https://azure.microsoft.com/services/cognitive-services/) resurs till din cognitive kompetens.

Resurs för Cognitive Services som du väljer kommer power inbyggda kognitiva funktioner. Den här resursen används också för fakturering. Alla enrichments som du utför med hjälp av inbyggda kognitiva funktioner kommer att debiteras mot Cognitive Services-resurs som du väljer. De kommer att faktureras enligt samma taxa som om du har utfört samma uppgift med hjälp av en resurs för Cognitive Services. Se [priser för Cognitive Service](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="limits-when-no-cognitive-services-resource-is-selected"></a>Begränsningar när ingen resurs för Cognitive Services är vald
Med början den 1 februari 2019 om du inte associerar en Cognitive Services-prenumeration med din kompetens kan kommer du endast att kunna utöka ett litet antal dokument för kostnadsfria (20 dokument per dag). 

## <a name="associating-a-cognitive-services-resource-with-a-new-skillset"></a>Associera en resurs för Cognitive Services med en ny kompetens

1. Som en del av den *dataimport* uppstår när du har anslutit till din datakälla du navigerar till den *Lägg till kognitiv sökning* valfritt steg. 

1. Expandera den *bifoga Cognitive Services* avsnittet. Då visas alla Cognitive Service-resurser som du har i samma regioner som din söktjänst. 
![Expanderad bifoga Cognitive Service](./media/cognitive-search-attach-cognitive-services/attach1.png "expanderas Lägg till kognitiva tjänster")

1. Välj en befintlig resurs för Cognitive Services, eller *skapa en ny resurs för Cognitive Services*. Om du väljer den *kostnadsfri (begränsad Enrichments) resource*, kommer du bara kunna utöka ett litet antal dokument för kostnadsfria (20 dokument per dag). Om du klickade på *skapa en ny resurs för Cognitive Services*, en ny flik öppnas som gör att du kan skapa resurs för Cognitive Services. 

1. Om du har skapat en ny resurs, klickar du på *uppdatera* att uppdatera listan över resurser för Cognitive Services och välj resursen. 
![Valt Cognitive Service resurs](./media/cognitive-search-attach-cognitive-services/attach2.png "valt Cognitive Service-resurs")

1. När du har gjort det, du kan expandera den *lägga till Enrichments* avsnitt för att välja specifika kognitiva kunskaper som du vill köra över dina data och fortsätta med resten av flödet.

## <a name="associating-a-cognitive-services-resource-with-an-existing-skillset"></a>Associera en resurs för Cognitive Services med ett kunskaper

1. På sidan Översikt över tjänsten väljer den *kompetens* fliken. ![Kompetens fliken](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "kompetens fliken")

1. *Klicka på* på gruppens kunskaper avgör du vill ändra. Då öppnas ett blad som gör att du kan redigera en kompetens.

1. Välj en befintlig resurs för Cognitive Services eller *skapa en ny resurs för Cognitive Services*. Om du väljer den *kostnadsfri (begränsad Enrichments) resource*, kommer du bara kunna utöka ett litet antal dokument för kostnadsfria (20 dokument per dag). Om du klickade på *skapa en ny resurs för Cognitive Services*, en ny flik öppnas som gör att du kan skapa resurs för Cognitive Services. <n/> 
<img src="./media/cognitive-search-attach-cognitive-services/attach-existing2.png" width="350">

1. Om du har skapat en ny resurs, klickar du på *uppdatera* att uppdatera listan över resurser för Cognitive Services och välj resursen.
1. Klicka på *OK* att bekräfta ändringarna

## <a name="associating-a-cognitive-services-resource-programmatically"></a>Associera en resurs för Cognitive Services via programmering

När du definierar gruppens kunskaper avgör programmässigt, lägga till en `cognitiveServices` avsnittet. Avsnittet bör innehålla nyckeln för Cognitive Services-resursen som du vill associera med kompetens, samt de @odata.type, vilket ska vara inställd på ”#Microsoft.Azure.Search.CognitiveServicesByKey”. Det här mönstret visas i exemplet nedan.

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
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey"
        "description": "mycogsvcs",
        "key": "your key goes here"
    }
}
```
## <a name="example-estimating-the-cost-of-document-cracking-and-enrichment"></a>Exempel: Uppskatta kostnaden för dokumentknäckning och berikande
Du kanske vill beräkna hur mycket det kostar för att utöka en viss typ av dokument. Den här övningen nedan är bara ett exempel, men det kan vara till hjälp för dig.

Anta att vi har 1000 PDF-filer och vi beräknar att i genomsnitt varje dokument har 6-sidor varje. Anta att var och en av dem har en avbildning per sida för den här övningen. Låt oss också anta som i genomsnitt finns det cirka 3 000 tecken per sida. 

Nu kan vi anta att vi vill utföra följande steg som en del av din pipeline för berikande:
1. Som en del av dokumentknäckning, extrahera innehåll och bilder från dokumentet.
1. Som en del av berikande, OCR av sidorna extraheras, kombinera texten för alla sidor och extrahera sedan var och en av organisationer i den kombinerade texten i alla avbildningar.

Nu ska vi beräkna hur mycket det skulle kosta för att mata in dessa dokument, steg för steg.

För 1000-dokument:

1. Dokumentera knäcka, skulle vi Extrahera ett kombinerat antal 6 000 bilder. Om vi antar att $1 för varje 1000 avbildningar som extraherats som skulle kosta $6.00 till oss.

2. Vi skulle extrahera text från var och en av dessa 6 000 bilder. I engelska använder OCR kognitiva kunskaper den bästa algoritmen (DescribeText). Om vi antar att en kostnad av $2.50 per 1 000 bilder analyseras, skulle vi betalar $15,00 för det här steget.

3. För entitetextrahering har vi totalt 3 textposter per sida (varje post är 1 000 tecken). 3 text poster/sidan * 6 000 sidor = 18 000 textposter. Om vi antar att $2.00 / 1000 textposter det här steget skulle kosta oss 36,00.

Vi skulle betala $57.00 att mata in 1000 pdf-dokument av den här typen med kompetens beskrivs sätter samman allt.  I den här övningen antas vi dyraste priset per transaktion, det kan ha varit lägre på grund av att de blivit priser. Se [priser för Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).



## <a name="next-steps"></a>Nästa steg
+ [Prissättningssidan för Azure Search](https://azure.microsoft.com/pricing/details/search/)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Skapa kompetens (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Avancerad och mappning](cognitive-search-output-field-mapping.md)
