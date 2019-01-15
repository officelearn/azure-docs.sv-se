---
title: Moderering av jobb och human-i-the-loop granskningar - Content Moderator
titlesuffix: Azure Cognitive Services
description: Kombinera datorstödd moderering med funktioner för mänskliga-i-the-loop med Azure Content Moderator granska API för att få bästa resultat för ditt företag.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: a348b18d1ecc9c0e4405c54a8e554d932781ec92
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265353"
---
# <a name="content-moderation-jobs-and-reviews"></a>Innehållsmoderering jobb och granskningar

Kombinera datorstödd moderering med funktioner för mänskliga-i-the-loop med hjälp av Azure Content Moderator [granska API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) att få bästa resultat för ditt företag.

Granska API: et erbjuder de följande sätten att tar med mänsklig övervakning i innehållsmoderering processen:

* `Job` åtgärder för att starta datorstödd moderering och mänsklig granskning skapas som ett steg.
* `Review` åtgärder som används för att skapa en mänsklig granskning, utanför moderering steg.
* `Workflow` åtgärder för att hantera arbetsflöden som automatiserar sökning med tröskelvärden för att skapa en granskning.

Den `Job` och `Review` operations godkänna dina återanrop slutpunkter för att ta emot status och resultat.

Den här artikeln beskriver den `Job` och `Review` åtgärder. Läs den [översikt för arbetsflöden](workflow-api.md) för information om hur du skapar, redigerar och få arbetsflödesdefinitioner.

## <a name="job-operations"></a>Jobbåtgärder

### <a name="start-a-job"></a>Starta ett jobb
Använd den `Job.Create` att starta en moderering och mänsklig granskning för att skapa jobb. Content Moderator söker igenom innehållet och utvärderar avsedda arbetsflödet. Baserat på resultaten arbetsflöde kan det antingen skapar granskningar eller hoppar över steget. Den skickar även efter moderering aktiviteter och efter taggar i återanrop-slutpunkten.

Indata innehåller följande information:

- Granska team-ID.
- Innehållet som ska vara kontrollerad.
- Arbetsflödesnamnet. (Standardvärdet är ”standard”-arbetsflödet.)
- API-återanrop anslutningspunkt meddelanden.
 
Följande svar visar identifierare för det jobb som har startats. Du kan använda jobb-ID för att hämta jobbstatus och får detaljerad information.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

### <a name="get-job-status"></a>Hämta jobbstatus för

Använd den `Job.Get` åtgärden och jobb-ID för att hämta information om ett jobb som körs eller slutförts. Åtgärden returnerar direkt, medan moderering jobbet körs asynkront. Resultaten returneras via återanrop-slutpunkt.

Dina indata inkludera följande information:

- Granska lag-ID: Jobb-ID som returneras av den föregående åtgärden

Svaret innehåller följande information:

- Identifierare för granskning skapas. (Använda detta ID för att få resultat slutlig granskning.)
- Status för jobbet (slutförd eller pågående): Tilldelade moderering taggar (nyckel / värde-par).
- Jobbet Körningsrapport.
 
 
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
 
![Bildgranskning för mänskliga moderatorer](images/ocr-sample-image.PNG)

## <a name="review-operations"></a>Granska åtgärder

### <a name="create-reviews"></a>Skapa granskningar

Använd den `Review.Create` att skapa mänsklig granskning. Du ändra dem någon annanstans eller använda anpassad logik för att tilldela moderering-taggar.

Dina indata till den här åtgärden är:

- Innehåll att granskas.
- Tilldelade taggar (nyckelvärdepar) för granskning av mänskliga moderatorer.

Följande svar visar identifierare för granskning:

    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]


### <a name="get-review-status"></a>Hämta granskningsstatus för
Använd den `Review.Get` för att få resultaten när en mänsklig granskning för kontrollerad avbildningen har slutförts. Du meddelas via motringningen slutpunkten. 

Åtgärden returnerar två uppsättningar med taggar: 

* De taggar som tilldelats av tjänsten moderering
* Taggar när mänsklig granskning har slutförts

Dina indata är minst:

- Granska Teamnamn
- Granska-identifieraren som returneras av den föregående åtgärden

Svaret innehåller följande information:

- Granskningsstatus
- Taggar (nyckel / värde-par) som bekräftats av mänsklig granskare
- Taggar (nyckel / värde-par) som tilldelats av tjänsten moderering

Du kan se båda granskare tilldelade taggar (**reviewerResultTags**) och de inledande taggarna (**metadata**) i följande exempelsvar:

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

* Testkör den [jobbet API-konsol](try-review-api-job.md), och använda REST API-kodexempel. Om du är bekant med Visual Studio och C# kan också Kolla den [Snabbstart för jobb .NET](moderation-jobs-quickstart-dotnet.md). 
* För granskning, Kom igång med den [granska API-konsol](try-review-api-review.md), och använda REST API-kodexempel. Då se den [Snabbstart för granskningar .NET](moderation-reviews-quickstart-dotnet.md).
* Video granskningar använder den [Video granska Snabbstart](video-reviews-quickstart-dotnet.md), och lär dig hur du [lägga till betyg i video granskningen](video-transcript-reviews-quickstart-dotnet.md).
