---
title: Kör jobb för innehåll avbrottsmoderering Azure innehåll kontrollant | Microsoft Docs
description: Lär dig hur du kör innehåll avbrottsmoderering jobb i API-konsolen.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/03/2017
ms.author: sajagtap
ms.openlocfilehash: 6f741be1001ae70d5fdbf6f374204aaad1601abe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351591"
---
# <a name="start-a-moderation-job-from-the-api-console"></a>Starta ett jobb för avbrottsmoderering från konsolen API

Använd API granska [jobbet operations](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) att initiera slutpunkt till slutpunkt innehåll avbrottsmoderering jobb för bild- eller innehåll i Azure innehåll kontrollanten. 

Jobbet avbrottsmoderering söker ditt innehåll med hjälp av innehåll kontrollant avbildningen Avbrottsmoderering API eller Text Avbrottsmoderering API. Sedan använder avbrottsmoderering jobbet arbetsflöden (definieras i verktyget granska) för att generera granskningar i verktyget granska. 

När en mänsklig kontrollant granskar automatiskt tilldelade taggar och förutsägelse data och skickar en slutlig avbrottsmoderering beslut, skickar granska API all information till API-slutpunkt.

## <a name="prerequisites"></a>Förutsättningar

Navigera till den [granska verktyget](https://contentmoderator.cognitive.microsoft.com/). Registrera dig om du inte har gjort det ännu. I verktyget granska [definiera ett anpassat arbetsflöde](Review-Tool-User-Guide/Workflows.md) till i den här `Job` igen.

## <a name="use-the-api-console"></a>Använda API-konsolen
Om du vill testa API: et med hjälp av konsolen online, behöver du några värden du anger i konsolen:
    
- `teamName`: Använd den `Id` från granska verktygets autentiseringsuppgifter skärmen. 
- `ContentId`: Det här sträng skickades till API: et och returnerade via återanropet. **ContentId** är användbart för att associera interna identifierare eller metadata med resultatet från ett jobb som måtta.- `Workflowname`: namnet på den [arbetsflöde som du skapade](Review-Tool-User-Guide/Workflows.md) i föregående avsnitt.
- `Ocp-Apim-Subscription-Key`: Finns på den **inställningar** fliken. Mer information finns i [översikt](overview.md).

Åtkomst till API-konsolen är från den **autentiseringsuppgifter** fönster.

### <a name="navigate-to-the-api-reference"></a>Navigera till API-referens
I den **autentiseringsuppgifter** väljer [API-referens för](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5).

  Den `Job.Create` öppnas.

### <a name="select-your-region"></a>Välj din region
För **öppna API-testet konsolen**, väljer du den region som bäst beskriver din plats.
  ![Jobbet – Skapa val av region](images/test-drive-job-1.png)

  Den `Job.Create` API-konsolen öppnas. 

### <a name="enter-parameters"></a>Ange parametrar

Ange värden för obligatoriska frågeparametrar och din prenumeration nyckel. I den **Begärandetext** ange platsen för den information som du vill genomsöka. I det här exemplet skriver du detta [exempelbild](https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png).

  ![Jobbet – Skapa konsolen frågeparametrar, rubriker och begäran brödtext rutan](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Skicka din begäran
Välj **Skicka**. Ett jobb-ID skapas. Kopiera den till att använda i nästa steg.

  `"JobId": "2018014caceddebfe9446fab29056fd8d31ffe"`

### <a name="open-the-get-job-details-page"></a>Öppna sidan Get Job
Välj **hämta**, och sedan öppna API: et genom att välja knappen som matchar din region.

  ![Jobb - skapa konsolen Get-resultat](images/test-drive-job-4.png)

### <a name="review-the-response"></a>Granska svaret

Ange värden för **teamName** och **JobID**. Ange nyckel för din prenumeration och välj sedan **skicka**. Följande meddelande visar information och exempel jobbstatus.

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

## <a name="navigate-to-the-review-tool"></a>Navigera till verktyget granskning
På innehåll kontrollant-instrumentpanelen väljer **granska** > **bild**. Bilden som du har läst visas, redo för mänsklig granskning.

  ![Granska verktyget bild av tre cyklister](images/ocr-sample-image.PNG)

## <a name="next-steps"></a>Nästa steg

Använda REST-API i koden eller starta med den [jobb .NET quickstart](moderation-jobs-quickstart-dotnet.md) att integrera med ditt program.
