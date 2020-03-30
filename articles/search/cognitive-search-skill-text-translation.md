---
title: Kognitiv skicklighet för textöversättning
titleSuffix: Azure Cognitive Search
description: Utvärderar text och returnerar text som översätts till det angivna målspråket i en AI-anrikningspipeline i Azure Cognitive Search för varje post.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5089174fcfd5a97128c1f789b818243243a5282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460766"
---
#   <a name="text-translation-cognitive-skill"></a>Kognitiv skicklighet för textöversättning

**Textöversättningsfärdigheten** utvärderar text och returnerar för varje post den översatta texten till det angivna målspråket. Den här färdigheten använder [Translator Text API v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) som är tillgängligt i Cognitive Services.

Den här funktionen är användbar om du förväntar dig att dina dokument kanske inte är på ett språk, i vilket fall du kan normalisera texten till ett enda språk innan du indexerar för sökning genom att översätta den.  Det är också användbart för lokalisering användningsfall, där du kanske vill ha kopior av samma text tillgänglig på flera språk.

[Translator Text API v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) är en icke-regional cognitive service, vilket innebär att dina data inte garanteras att stanna i samma region som din Azure Cognitive Search eller ansluten Cognitive Services-resurs.

> [!NOTE]
> När du utökar omfattningen genom att öka bearbetningsfrekvensen, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [bifoga en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifter ackumuleras när du anropar API:er i Cognitive Services och för bildutvinning som en del av dokumentsprickningsfasen i Azure Cognitive Search. Det finns inga avgifter för textextrahering från dokument.
>
> Utförande av inbyggda färdigheter debiteras på den befintliga [Cognitive Services pay-as-you go pris](https://azure.microsoft.com/pricing/details/cognitive-services/). Prissättning för avbildningsextrahering beskrivs på [prissidan](https://go.microsoft.com/fwlink/?linkid=2042400)för Azure Cognitive Search .

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.text.translationskill

## <a name="data-limits"></a>Databegränsningar
Den maximala storleken på en post bör vara 50 [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)000 tecken mätt med . Om du behöver dela upp dina data innan du skickar dem till textöversättningsfärdigheten kan du använda [textdelningsfärdigheten](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Färdighetsparametrar

Parametrar är skiftlägeskänsliga.

| Indata                | Beskrivning |
|---------------------|-------------|
| standardToLanguageCode | (Obligatoriskt) Språkkoden som dokument ska översättas till för dokument som inte uttryckligen anger språket. <br/> Se [Fullständig lista över språk som stöds](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| standardFrånSpråkkod | (Valfritt) Språkkoden som ska översättas från för dokument som inte uttryckligen anger från-språket.  Om standardFrånLanguageCode inte anges används den automatiska språkidentifiering som tillhandahålls av translatortext-API:et för att bestämma från språket. <br/> Se [Fullständig lista över språk som stöds](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| suggestedFrom | (Valfritt) Språkkoden för att översätta dokument från när varken indata från SpråkuageCode eller parametern DefaultFromLanguageCode tillhandahålls och den automatiska språkidentifieringen misslyckas.  Om det föreslagna Från-språket inte anges används engelska (en) som det förslag påFrom-språket. <br/> Se [Fullständig lista över språk som stöds](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |

## <a name="skill-inputs"></a>Indata för färdighet

| Indatanamn     | Beskrivning |
|--------------------|-------------|
| text | Texten som ska översättas.|
| toLanguageCode    | En sträng som anger vilket språk texten ska översättas till. Om den här indata inte anges används standardtospråkuagekoden för att översätta texten. <br/>Se [Fullständig lista över språk som stöds](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|
| frånSpråkKod  | En sträng som anger textens aktuella språk. Om den här parametern inte anges används standardFrånLanguageCode (eller automatisk språkidentifiering om standardFrånLanguageCode inte anges) för att översätta texten. <br/>Se [Fullständig lista över språk som stöds](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|

## <a name="skill-outputs"></a>Utdata för färdighet

| Utdatanamn    | Beskrivning |
|--------------------|-------------|
| översatttext | Strängen resultatet av textöversättningen från translatedFromLanguageCode till translatedToLanguageCode.|
| översattToLanguageCode  | En sträng som anger den språkkod som texten översattes till. Användbart om du översätter till flera språk och vill kunna hålla reda på vilken text som är vilket språk.|
| översattFrånSpråkkod    | En sträng som anger den språkkod som texten översattes från. Användbart om du har valt alternativet automatisk språkidentifiering eftersom den här utdata ger dig resultatet av den identifieringen.|

##  <a name="sample-definition"></a>Exempeldefinition

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
      }
    ]
  }
```

##  <a name="sample-input"></a>Exempelinmatning

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "text": "We hold these truths to be self-evident, that all men are created equal."
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "text": "Estamos muy felices de estar con ustedes."
        }
    }
  ]
}
```


##  <a name="sample-output"></a>Exempel på utdata

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>Fel och varningar
Om du anger en språkkod som inte stöds för antingen från- eller till-språket genereras ett fel och texten översätts inte.
Om texten är tom visas en varning.
Om texten är större än 50 000 tecken översätts bara de första 50 000 tecknen och en varning utfärdas.

## <a name="see-also"></a>Se även

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
