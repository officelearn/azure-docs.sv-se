---
title: Kör jobb för innehållsmoderering med API-konsolen – Content Moderator
titlesuffix: Azure Cognitive Services
description: Använd granska API-jobbet åtgärderna för att initiera jobb innehållsmoderering för slutpunkt till slutpunkt för bild eller text innehåll i Azure Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 9fac80ff152b0f7b9c36c182759d41245364fff9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55862436"
---
# <a name="start-a-moderation-job-from-the-api-console"></a>Starta ett jobb för moderering från API-konsol

Använd granskning API [jobbet operations](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) att initiera jobb innehållsmoderering för slutpunkt till slutpunkt för bild eller text innehåll i Azure Content Moderator. 

Moderering av jobbet söker igenom ditt innehåll med hjälp av innehåll Moderator Image Moderering API eller API för Moderering av Text. Sedan använder moderering jobbet arbetsflöden (definieras i granskningsverktyget) för att generera granskningar i granskningsverktyget. 

När en mänsklig moderator granskar automatiskt tilldelade taggar och förutsägelsedata och skickar ett sista moderering beslut, skickar granska API: et all information som ditt API-slutpunkten.

## <a name="prerequisites"></a>Förutsättningar

Navigera till den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/). Registrera dig om du inte har gjort det ännu. I Verktyg för granskning [definierar ett anpassat arbetsflöde](Review-Tool-User-Guide/Workflows.md) till i den här `Job` igen.

## <a name="use-the-api-console"></a>Använd API-konsol
Om du vill testa API: et med hjälp av konsolen online, behöver du några värden för att komma in i konsolen:
    
- `teamName`: Använd den `Id` fältet från din granskningsverktyget autentiseringsuppgifter skärm. 
- `ContentId`: Den här strängen skickas till API: et och returneras via motringningen. **ContentId** är användbart för att associera interna identifierare eller metadata med resultatet från ett jobb för moderering.- `Workflowname`: Namnet på den [arbetsflöde som du skapade](Review-Tool-User-Guide/Workflows.md) i föregående avsnitt.
- `Ocp-Apim-Subscription-Key`: På den **inställningar** fliken. Mer information finns i [Översikt](overview.md).

Komma åt API-konsolen är från den **autentiseringsuppgifter** fönster.

### <a name="navigate-to-the-api-reference"></a>Gå till API-referens
I den **autentiseringsuppgifter** väljer [API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5).

  Den `Job.Create` öppnas.

### <a name="select-your-region"></a>Välj region
För **Open API testkonsolen**, väljer du den region som bäst beskriver din plats.
  ![Jobb – skapa val av region](images/test-drive-job-1.png)

  Den `Job.Create` API-konsolen öppnas. 

### <a name="enter-parameters"></a>Ange parametrar

Ange värden för obligatoriska parametrar och din prenumerationsnyckel. I den **Begärandetext** anger platsen för den information som du vill genomsöka. I det här exemplet använder vi detta [exempelbild](https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png).

  ![Jobb – skapa konsolen frågeparametrar, rubriker och begäran text box](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Skicka din begäran
Välj **Skicka**. Ett jobb-ID skapas. Kopiera det här för att använda i nästa steg.

  `"JobId": "2018014caceddebfe9446fab29056fd8d31ffe"`

### <a name="open-the-get-job-details-page"></a>Öppna sidan Hämta jobb
Välj **hämta**, och sedan öppna API: et genom att välja knappen som matchar din region.

  ![Jobb – skapa konsolen Get-resultat](images/test-drive-job-4.png)

### <a name="review-the-response"></a>Granska svaret

Ange värden för **teamName** och **JobID**. Ange din prenumerationsnyckel och välj sedan **skicka**. Följande svar visar exempel jobbstatus och information.

```
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": 
        [
            {
            "Ts": "2018-01-07T00:38:26.7714671",
                "Msg": "Successfully got hasText response from Moderator"
            },
            {
                "Ts": "2018-01-07T00:38:26.4181346",
                "Msg": "Getting hasText from Moderator"
            },
            {
                "Ts": "2018-01-07T00:38:25.5122828",
                "Msg": "Starting Execution - Try 1"
            }
        ]
    }
```

## <a name="navigate-to-the-review-tool"></a>Gå till verktyg för granskning
Content Moderator-instrumentpanelen, klicka **granska** > **bild**. Bilden som du skannade visas, redo för mänsklig granskning.

  ![Kontrollera att verktyget avbildning av tre cyklister](images/ocr-sample-image.PNG)

## <a name="next-steps"></a>Nästa steg

Använda REST-API i koden eller börja med den [Snabbstart för jobb .NET](moderation-jobs-quickstart-dotnet.md) att integrera med ditt program.
