---
title: Skapa modereringsgranskningar med REST API-konsol - Innehållsmoderator
titleSuffix: Azure Cognitive Services
description: Använd AZURE Content Moderator Review API:er för att skapa avbildnings- eller textrecensioner för mänsklig måtta.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: a9726e41a84926d00d48b51e31f534a3d8c2fe0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757147"
---
# <a name="create-human-reviews-rest"></a>Skapa mänskliga recensioner (REST)

[Recensioner](./review-api.md#reviews) lagra och visa innehåll för mänskliga moderatorer att bedöma. När en användare slutför en granskning skickas resultaten till en angiven slutpunkt för motringning. I den här guiden får du lära dig hur du konfigurerar recensioner med hjälp av granska REST API:er via API-konsolen. När du har förstått API:ernas struktur kan du enkelt portera dessa anrop till valfri REST-kompatibel plattform.

## <a name="prerequisites"></a>Krav

- Logga in eller skapa ett konto på webbplatsen för [granskning](https://contentmoderator.cognitive.microsoft.com/) av innehållsmoderator.

## <a name="create-a-review"></a>Skapa en recension

Om du vill skapa en recension går du till referenssidan **[granska - skapa](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API och väljer knappen för nyckelområdet (du hittar den i slutpunkts-URL:en på sidan **Autentiseringsuppgifter** i [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/)). Detta startar API-konsolen, där du enkelt kan konstruera och köra REST API-anrop.

![Recension - Hämta regionval](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Ange parametrar för REST-anrop

Ange värden för **teamName**och **Ocp-Apim-Subscription-Key:**

- **teamName**: Det grupp-ID som du skapade när du konfigurerade [granskningsverktygskontot](https://contentmoderator.cognitive.microsoft.com/) (finns i **fältet ID** på skärmen Autentiseringsuppgifter för granskningsverktyget).
- **Ocp-Apim-Prenumeration-Key:** Din Innehåll Moderator nyckel. Du hittar detta på fliken **Inställningar** i [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Ange en granskningsdefinition

Redigera **brödtextrutan Begäran om** du vill ange JSON-begäran med följande fält:

- **Metadata**: Anpassade nyckelvärdespar som ska returneras till slutpunkten för motringning. Om nyckeln är en kort kod som definieras i [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com)visas den som en tagg.
- **Innehåll**: När det gäller bild- och videoinnehåll är detta en URL-sträng som pekar på innehållet. För textinnehåll är detta den faktiska textsträngen.
- **ContentId**: En anpassad identifierare sträng. Den här strängen skickas till API:et och returneras via motringningen. Det är användbart för att associera interna identifierare eller metadata med resultatet av ett modereringsjobb.
- **CallbackEndpoint**: (Valfritt) URL:en för att ta emot motringningsinformation när granskningen är klar.

Standardförfrågningstexten visar exempel på de olika typer av granskningar som du kan skapa:

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>Skicka din förfrågan
  
Välj **Skicka**. Om åtgärden lyckas är `200 OK` **svarsstatusen** och rutan **Svarsinnehåll** visar ett ID för granskningen. Kopiera det här ID:et som du kan använda i följande steg.

![Recension - Innehållsrutan Skapa konsolsvar visar gransknings-ID:t](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Granska den nya översynen

I [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com)väljer du **Granska** > **bildtextvideo**/**Text**/**Video** (beroende på vilket innehåll du använde). Innehållet som du har laddat upp ska visas, redo för mänsklig granskning.

![Granska verktygsbild av en fotboll](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Få information om recension

Om du vill hämta information om en befintlig granskning går du till referenssidan [granska - få](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) API och väljer knappen för din region (den region där nyckeln administreras).

![Arbetsflöde - Hämta regionval](images/test-drive-region.png)

Ange REST-samtalsparametrarna som i avsnittet ovan. I det här steget är **reviewId** den unika ID-sträng som du fick när du skapade granskningen.

![Recension - Skapa konsol Få resultat](images/test-drive-review-3.PNG)
  
Välj **Skicka**. Om åtgärden lyckas är `200 OK` **svarsstatusen** och i rutan **Svarsinnehåll** visas granskningsinformationen i JSON-format, till exempel följande:

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

Ta del av följande fält i svaret:

- **status**
- **granskarresultTags**: Detta visas om några taggar har lagts till manuellt av det mänskliga granskningsteamet (visas **fältet createdBy).**
- **metadata**: Detta visar de taggar som ursprungligen lades till i granskningen, innan den mänskliga granskningsgruppen gjorde ändringar.

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du skapar recensioner för innehållsmoderering med REST API.In this guide, you learned how to create content moderation reviews using the REST API. Därefter integrera recensioner i ett end-to-end moderering scenario, till exempel [e-handel moderering](./ecommerce-retail-catalog-moderation.md) handledning.