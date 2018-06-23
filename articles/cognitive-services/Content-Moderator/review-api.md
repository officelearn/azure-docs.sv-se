---
title: Azure innehåll kontrollant - Avbrottsmoderering jobb och personal-i-the-loop granskningar | Microsoft Docs
description: Gäller mänsklig tillsyn datorn stödd avbrottsmoderering för bästa resultat.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 1/21/2018
ms.author: sajagtap
ms.openlocfilehash: 35b3cdaa410712c3fd08d3df4ebe4c83e3955d50
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351768"
---
# <a name="moderation-jobs-and-reviews"></a>Avbrottsmoderering jobb och granskning

Kombinera datorn stödd avbrottsmoderering med hr-i-the-loop funktioner med hjälp av Azure innehåll kontrollanten [granska API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) att få bästa möjliga resultat för ditt företag.

Granska API ger följande sätt att inkludera mänsklig tillsyn i innehåll avbrottsmoderering processen:

* `Job` åtgärder för att starta datorn stödd avbrottsmoderering och skapande av mänsklig granska som ett steg.
* `Review` operationer används för att skapa en mänsklig granskning, utanför avbrottsmoderering steg.
* `Workflow` åtgärder för att hantera arbetsflöden som automatiserar skanning med tröskelvärden för att skapa en granskning.

Den `Job` och `Review` operations accepterar dina återanrop slutpunkter för att ta emot status och resultat.

Den här artikeln beskriver den `Job` och `Review` åtgärder. Läs den [arbetsflöden översikt](workflow-api.md) för information om hur du skapar, redigera och hämta arbetsflödesdefinitioner för.

## <a name="job-operations"></a>Jobbåtgärder

### <a name="start-a-job"></a>Starta ett jobb
Använd den `Job.Create` åtgärd för att starta en gruppering och mänsklig granska för att skapa jobb. Innehåll kontrollanten söker igenom innehållet och utvärderar avsedda arbetsflödet. Baserat på resultatet arbetsflöde, den skapar granskningar eller hoppar över steget. Den skickar också efter ändring och efter granska taggar till återanrop-slutpunkten.

Indata innehåller följande information:

- Granska team-ID.
- Innehållet som ska vara kontrollerad.
- Namnet på arbetsflödet. (Standardvärdet är ”default” arbetsflödet.)
- API-återanrop peka för meddelanden.
 
Följande meddelande visar identifierare för det jobb som har startats. Du kan använda jobb-ID för att hämta jobbstatus och få detaljerad information.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

### <a name="get-job-status"></a>Hämta jobbstatus

Använd den `Job.Get` åtgärden och jobb-ID för att hämta information om ett jobb som körs eller har slutförts. Åtgärden returnerar omedelbart medan avbrottsmoderering jobbet körs asynkront. Resultaten returneras via återanrop slutpunkten.

Indata innehåller följande information:

- Granska grupp-ID: jobb-ID som returnerades av föregående åtgärd

Svaret innehåller följande information:

- Identifierare för granska skapas. (Använda detta ID för att hämta sista granska resultaten.)
- Status för jobbet (slutförd eller pågående): tilldelade avbrottsmoderering taggar (nyckel / värde-par).
- Jobbet körning av rapporten.
 
 
        {
            "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
            "TeamName": "some team name",
            "Status": "Complete",
            "WorkflowId": "OCR",
            "Type": "Image",
            "CallBackEndpoint": "",
            "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
            "ResultMetaData":[
            {
            "Key": "hasText",
            "Value": "True"
            },
            {
            "Key": "ocrText",
            "Value": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
            }
            ],
            "JobExecutionReport": [
            {
                "Ts": "2018-01-07T00:38:29.3238715",
                "Msg": "Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
                },
                {
                "Ts": "2018-01-07T00:38:29.2928416",
                "Msg": "Job marked completed and job content has been removed"
                },
                {
                "Ts": "2018-01-07T00:38:29.0856472",
                "Msg": "Execution Complete"
                },
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
 
![Granska bild för mänsklig moderatorer](images/ocr-sample-image.PNG)

## <a name="review-operations"></a>Granska operations

### <a name="create-reviews"></a>Skapa granskningar

Använd den `Review.Create` åtgärden för att skapa mänsklig granskningar. Du måttlig dem på en annan plats eller använda anpassade logik för att tilldela avbrottsmoderering-taggar.

Alla indata till den här åtgärden är:

- Innehåll som ska granskas.
- Tilldelade taggarna (nyckel/värde-par) för granskning av mänsklig kontrollanter.

Följande meddelande visar granska identifierare:

    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]


### <a name="get-review-status"></a>Hämta status för granskning
Använd den `Review.Get` Hämta resultaten efter en mänsklig granskning av kontrollerad avbildningen har slutförts. Du meddelas via angivna återanropet slutpunkten. 

Åtgärden returnerar två uppsättningar med taggar: 

* De taggar som tilldelats av tjänsten avbrottsmoderering
* Taggar när mänsklig granska slutfördes

Indata är minst:

- Granska Teamnamn
- Granska identifieraren som returneras av föregående åtgärd

Svaret innehåller följande information:

- Granskningsstatus
- Taggar (nyckel / värde-par) bekräftas av mänsklig granskare
- Taggar (nyckel-värdepar) som tilldelats av tjänsten avbrottsmoderering

Du ser både granskare tilldelade taggar (**reviewerResultTags**) och de inledande taggarna (**metadata**) i följande exempel svaret:

    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
            "key": "a",
            "value": "False"
        },
        {
            "key": "r",
            "value": "True"
        },
        {
            "key": "sc",
            "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

## <a name="next-steps"></a>Nästa steg

* Testkör den [jobbet API konsolen](try-review-api-job.md), och använda REST API-kodexempel. Om du är bekant med Visual Studio och C#, se även den [jobb .NET quickstart](moderation-jobs-quickstart-dotnet.md). 
* För granskningar, komma igång med den [granska API konsolen](try-review-api-review.md), och använda REST API-kodexempel. Se den [granskningar .NET quickstart](moderation-reviews-quickstart-dotnet.md).
* Video granskningar använder den [Video granska quickstart](video-reviews-quickstart-dotnet.md), och lära dig hur du [lägga till betyg i video granska](video-transcript-reviews-quickstart-dotnet.md).
