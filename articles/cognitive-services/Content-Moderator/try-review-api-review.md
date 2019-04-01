---
title: Skapa moderering granskningar med REST API-konsolen – Content Moderator
titlesuffix: Azure Cognitive Services
description: 'Använda Azure Content Moderator granska API: erna för att skapa bild eller text granskningar för mänskliga moderering.'
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: beadbfc09526f738ba90252787b5b0910a2f7163
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755371"
---
# <a name="create-human-reviews-rest"></a>Skapa mänsklig granskning (REST)

[Granskningar](./review-api.md#reviews) lagra och visa innehållet för mänskliga moderatorer att utvärdera. När en användare har slutfört en granskning, skickas resultaten till en slutpunkt för angivna återanrop. I den här guiden får du lära dig hur du ställer in granskningar som använder granska REST API: er via API-konsolen. När du förstår hur API: er kan portera du enkelt dessa anrop till valfri REST-kompatibel plattform.

## <a name="prerequisites"></a>Förutsättningar

- Logga in eller skapa ett konto på Content Moderator [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/) plats.

## <a name="create-a-review"></a>Skapa en granskning

Om du vill skapa en granskning, går du till den **[granska – skapa](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API refererar till sidan och klicka på knappen för din nyckel region (du hittar den i slutpunkts-URL på den **autentiseringsuppgifter** sidan av den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/)). Detta startar API-konsol, där du kan enkelt skapa och köra REST API-anrop.

![Granska - Get valet](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Ange parametrar för REST-anrop

Ange värden för **teamName**, och **Ocp-Apim-Subscription-Key**:

- **teamName**: Lag-ID som du skapade när du ställer in din [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/) konto (finns i den **Id** på din granskningsverktyget autentiseringsuppgifter skärmen).
- **Ocp-Apim-Subscription-Key**: Din nyckel för Content Moderator. Du hittar den på den **inställningar** fliken den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Ange en definition för granskning

Redigera den **Begärandetext** om du vill ange JSON-begäran med följande fält:

- **Metadata**: Anpassad nyckel / värde-par som ska returneras till slutpunkten återanrop. Om nyckeln är en kort kod som har definierats i den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com), visas den som en tagg.
- **Innehåll**: När det gäller bild och videoinnehåll innehåll är det här en URL-sträng som pekar på innehållet. Det här är den faktiska textsträngen för textinnehåll.
- **ContentId**: En anpassad ID-sträng. Den här strängen skickas till API: et och returneras via motringningen. Det är användbart för att associera interna identifierare eller metadata med resultatet från ett jobb för moderering.
- **CallbackEndpoint**: (Valfritt) URL till får återanrop information när granskningen har slutförts.

Standard-begärandetexten visar exempel på de olika typerna av granskningar som du kan skapa:

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

### <a name="submit-your-request"></a>Skicka din begäran
  
Välj **Skicka**. Om åtgärden lyckas den **svarsstatus** är `200 OK`, och **svarsinnehåll** visar ett ID för granskningen. Kopiera detta ID ska användas i följande steg.

![Granska – skapa konsolen svar innehåll visar åtkomstgransknings-ID](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Undersök ny granskning

I den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com)väljer **granska** > **bild**/**Text** / **Video** (beroende på vilket innehåll du används). Det innehåll som du överförde bör visas och redo för mänsklig granskning.

![Kontrollera att verktyget avbildning av en fotboll](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Hämta information om granskning

Om du vill hämta information om en befintlig granskning, går du till den [granska – hämta](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) API refererar till sidan och klicka på knappen för din region (den region där din nyckel administreras).

![Arbetsflöde – Get valet](images/test-drive-region.png)

Ange parametrar för REST-anrop som i avsnittet ovan. Det här steget **reviewId** är en unik ID-sträng som du fick när du skapade granskningen.

![Granska – skapa konsolen Get-resultat](images/test-drive-review-3.PNG)
  
Välj **Skicka**. Om åtgärden lyckas den **svarsstatus** är `200 OK`, och **svarsinnehåll** rutan visar den granska informationen i JSON-format, som följande:

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

Anteckna följande fält i svaret:

- **status**
- **reviewerResultTags**: Det här visas om några taggar manuellt har lagts till av mänsklig granskning-teamet (visas den **createdBy** fältet).
- **metadata**: Detta visar de taggar som ursprungligen har lagts till i granskningen innan mänsklig granskning team har gjort ändringar.

## <a name="next-steps"></a>Nästa steg

I den här handboken beskrivs hur du skapar innehållsmoderering granskningar med hjälp av REST-API. Sedan integrera granskningar i ett scenario för slutpunkt till slutpunkt moderering som den [E-handel moderering](./ecommerce-retail-catalog-moderation.md) självstudien.