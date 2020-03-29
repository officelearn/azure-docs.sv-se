---
title: Använda modereringsjobb med REST API-konsolen - Innehållsmoderator
titleSuffix: Azure Cognitive Services
description: Använd gransknings-API:ets jobbåtgärder för att initiera heltäckande jobb för moderering av innehåll för bild- eller textinnehåll i Azure Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 83ee8e0c0583cba72da8702e196f0f38128f8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "72935940"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Definiera och använda modereringsjobb (REST)

Ett modereringsjobb fungerar som ett slags omslag för funktionaliteten för innehållsmoderering, arbetsflöden och recensioner. Den här guiden visar hur du använder rest-API:erna för att initiera och kontrollera jobb för innehållsmoderering. När du har förstått API:ernas struktur kan du enkelt portera dessa anrop till valfri REST-kompatibel plattform.

## <a name="prerequisites"></a>Krav

- Logga in eller skapa ett konto på webbplatsen för [granskning](https://contentmoderator.cognitive.microsoft.com/) av innehållsmoderator.
- (Valfritt) [Definiera ett anpassat arbetsflöde](./Review-Tool-User-Guide/Workflows.md) som ska användas med jobbet. Du kan också använda standardarbetsflödet.

## <a name="create-a-job"></a>Skapa ett jobb

Om du vill skapa ett modereringsjobb går du till referenssidan [Jobb - Skapa](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API och väljer knappen för din prenumerationsregion (du hittar den i slutpunkts-URL:en på sidan **Autentiseringsuppgifter** i [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/)). Detta startar API-konsolen, där du enkelt kan konstruera och köra REST API-anrop.

![Jobb - Skapa val av sidregion](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Ange parametrar för REST-anrop

Ange följande värden för att konstruera REST-anropet:

- **teamName**: Det grupp-ID som du skapade när du konfigurerade [granskningsverktygskontot](https://contentmoderator.cognitive.microsoft.com/) (finns i **fältet ID** på skärmen Autentiseringsuppgifter för granskningsverktyget).
- **ContentType**: Detta kan vara "Image", "Text" eller "Video".
- **ContentId**: En anpassad identifierare sträng. Den här strängen skickas till API:et och returneras via motringningen. Det är användbart för att associera interna identifierare eller metadata med resultatet av ett modereringsjobb.
- **Arbetsflödesnamn:** Namnet på det arbetsflöde som du tidigare skapade (eller "standard" för standardarbetsflödet).
- **CallbackEndpoint**: (Valfritt) URL:en för att ta emot motringningsinformation när granskningen är klar.
- **Ocp-Apim-Prenumeration-Key:** Din Innehåll Moderator nyckel. Du hittar detta på fliken **Inställningar** i [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Fyll i begärandeorganet

Brödtexten i REST-anropet innehåller ett fält, **ContentValue**. Klistra in råtextinnehållet om du modererar text eller ange en bild- eller video-URL om du modererar bild/video. Du kan använda följande exempelbild-URL:[https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Jobb - Skapa konsolfrågeparametrar, rubriker och rutan Begär](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Skicka din förfrågan

Välj **Skicka**. Om åtgärden lyckas är `200 OK` **svarsstatusen** och rutan **Svarsinnehåll** visar ett ID för jobbet. Kopiera det här ID:et som du kan använda i följande steg.

![Recension - Innehållsrutan Skapa konsolsvar visar gransknings-ID:t](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Hämta jobbstatus

Om du vill ha status och information om ett jobb som körs eller slutförs går du till referenssidan [Jobb - Hämta](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) API och väljer knappen för din region (den region där nyckeln administreras).

![Jobb - Hämta regionval](images/test-drive-region.png)

Ange REST-samtalsparametrarna som i avsnittet ovan. För det här steget är **JobId** den unika ID-sträng som du fick när du skapade jobbet. Välj **Skicka**. Om åtgärden lyckas är `200 OK` **svarsstatusen** och rutan **Svarsinnehåll** visar jobbet i JSON-format, till exempel följande:

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

![Jobb - Få REST-samtalssvar](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Granska den nya översikten(erna)

Om ditt innehållsjobb ledde till att en granskning skapades kan du visa det i [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com). Välj **Granska** > **bildtextvideo**/**Text**/**Video** (beroende på vilket innehåll du har använt). Innehållet ska visas, redo för mänsklig granskning. När en mänsklig moderator har granskar de automatiskt tilldelade taggarna och förutsägelsedata och skickar ett slutligt beslut om moderering skickar jobb-API:et all den här informationen till slutpunkten för motringning.

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du skapar och frågar jobb för innehållsmoderering med REST-API:et. Därefter integrerar jobb i ett scenario med slutåtermoderning, till exempel självstudiekursen [för moderering av e-handel.](./ecommerce-retail-catalog-moderation.md)