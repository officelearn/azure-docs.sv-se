---
title: Använda moderering jobb med REST API-konsolen – Content Moderator
titlesuffix: Azure Cognitive Services
description: Använd granska API-jobbet åtgärderna för att initiera jobb innehållsmoderering för slutpunkt till slutpunkt för bild eller text innehåll i Azure Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 7827cee2af2dfc0c1fddc407c1d146dc9a66c514
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756098"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Definiera och Använd moderering jobb (REST)

Ett jobb för moderering fungerar som en sorts Omslutning för funktionerna i innehållsmoderering, arbetsflöden och granskningar. Den här guiden visar hur du använder jobbet REST API: er för att initiera och kontrollera innehållsmoderering jobb. När du förstår hur API: er kan portera du enkelt dessa anrop till valfri REST-kompatibel plattform.

## <a name="prerequisites"></a>Förutsättningar

- Logga in eller skapa ett konto på Content Moderator [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/) plats.
- (Valfritt) [Definierar ett anpassat arbetsflöde](./Review-Tool-User-Guide/Workflows.md) ska användas med dina jobb; du kan också använda standardarbetsflödet.

## <a name="create-a-job"></a>Skapa ett jobb

För att skapa ett jobb för moderering, går du till den [jobb – skapa](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API refererar till sidan och klicka på knappen för din nyckel region (du hittar den i slutpunkts-URL på den **autentiseringsuppgifter** för den [granskning verktyget](https://contentmoderator.cognitive.microsoft.com/)). Detta startar API-konsol, där du kan enkelt skapa och köra REST API-anrop.

![Jobb – skapa val av region](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Ange parametrar för REST-anrop

Ange följande värden för att konstruera ett REST-anrop:

- **teamName**: Lag-ID som du skapade när du ställer in din [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/) konto (finns i den **Id** på din granskningsverktyget autentiseringsuppgifter skärmen).
- **ContentType**: Detta kan vara ”bild”, ”Text” eller ”Video”.
- **ContentId**: En anpassad ID-sträng. Den här strängen skickas till API: et och returneras via motringningen. Det är användbart för att associera interna identifierare eller metadata med resultatet från ett jobb för moderering.
- **workflowname**: Namnet på arbetsflödet som du skapade tidigare (eller ”standard” för standardarbetsflöde).
- **CallbackEndpoint**: (Valfritt) URL till får återanrop information när granskningen har slutförts.
- **Ocp-Apim-Subscription-Key**: Din nyckel för Content Moderator. Du hittar den på den **inställningar** fliken den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Fyll i begärandetexten

REST-anrop innehåller ett fält **ContentValue**. Klistra in innehållet rå text om du kontrollera text eller ange en bild eller video-URL Om du kontrollera bild/video. Du kan använda följande exempel bild-URL: [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Jobb – skapa konsolen frågeparametrar, rubriker och begäran text box](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Skicka din begäran

Välj **Skicka**. Om åtgärden lyckas den **svarsstatus** är `200 OK`, och **svarsinnehåll** visar ett ID för jobbet. Kopiera detta ID ska användas i följande steg.

![Granska – skapa konsolen svar innehåll visar åtkomstgransknings-ID](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Hämta jobbstatus för

Status och information om ett jobb som körs eller slutförts, gå till den [jobb – hämta](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) API refererar till sidan och klicka på knappen för din region (den region där din nyckel administreras).

![Jobb – Get valet](images/test-drive-region.png)

Ange parametrar för REST-anrop som i avsnittet ovan. Det här steget **JobId** är en unik ID-sträng som du fick när du skapade jobbet. Välj **Skicka**. Om åtgärden lyckas den **svarsstatus** är `200 OK`, och **svarsinnehåll** jobbet visar i JSON-format som liknar följande:

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![Jobb – hämta REST-anrop svar](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Granska nya review(s)

Om ditt innehåll jobb resulterade i att skapa en granskning, kan du visa den i den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com). Välj **granska** > **bild**/**Text**/**Video** (beroende på vilket innehåll du används). Det är klara att innehållet ska visas för mänsklig granskning. När en mänsklig moderator granskar automatiskt tilldelade taggar och förutsägelsedata och skickar ett sista moderering beslut, skickar jobb-API: et all denna information till den avsedda återanrop endpoint-slutpunkten.

## <a name="next-steps"></a>Nästa steg

I den här handboken beskrivs hur du skapar och frågar innehållsmoderering jobb med hjälp av REST-API. Sedan integrera jobb i ett scenario för slutpunkt till slutpunkt moderering som den [E-handel moderering](./ecommerce-retail-catalog-moderation.md) självstudien.