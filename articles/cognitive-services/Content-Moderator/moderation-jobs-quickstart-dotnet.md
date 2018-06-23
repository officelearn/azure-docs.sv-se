---
title: Azure innehåll kontrollant - Start avbrottsmoderering jobb med hjälp av .NET | Microsoft Docs
description: Hur du startar avbrottsmoderering jobb med hjälp av Azure innehåll kontrollant SDK för .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/06/2018
ms.author: sajagtap
ms.openlocfilehash: a103875607355993e216ce1ddea02009fc8fa1c4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351693"
---
# <a name="start-moderation-jobs-using-net"></a>Starta avbrottsmoderering jobb med hjälp av .NET

Den här artikeln innehåller information och kodexempel som hjälper dig att komma igång med innehåll kontrollant SDK för .NET för att:
 
- Starta en avbrottsmoderering jobbet för att söka och skapa omdömen för mänsklig moderatorer
- Hämta status för väntande granskning
- Spåra och få slutlig status för granska
- Skicka resultatet till återanrop Url

Den här artikeln förutsätter att du redan är bekant med Visual Studio och C#.

## <a name="sign-up-for-content-moderator-services"></a>Registrera dig för innehåll kontrollant services

Innan du kan använda innehåll kontrollant tjänster via REST API eller SDK behöver du en prenumeration för.
Referera till den [Quickstart](quick-start.md) att lära dig hur du kan hämta nyckeln.

## <a name="define-a-custom-moderation-workflow"></a>Definiera en anpassad avbrottsmoderering arbetsflöde

Ett jobb med måtta skannar ditt innehåll med hjälp av API: er och använder en **arbetsflöde** att avgöra om du vill skapa granskningar eller inte.
Medan verktyget granska innehåller ett standardarbetsflöde som, vi [definiera ett anpassat arbetsflöde](Review-Tool-User-Guide/Workflows.md) för denna Snabbstart.

Du kan använda namnet på arbetsflödet i koden som startar avbrottsmoderering jobb.

## <a name="create-your-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Lägg till en ny **konsolapp (.NET Framework)** projekt i lösningen.

   Namnge projektet i koden, **CreateReviews**.

1. Välj det här projektet som Startprojekt enda för lösningen.

1. Lägg till en referens till den **ModeratorHelper** projektet sammansättningen som du skapade i den [innehåll kontrollant klienten helper quickstart](content-moderator-helper-quickstart-dotnet.md).

### <a name="install-required-packages"></a>Installera de paket som krävs

Installera följande NuGet-paket:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Uppdatera programmet använder instruktioner

Ändra programmet använder instruktioner.

    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using ModeratorHelper;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="initialize-application-specific-settings"></a>Initiera programspecifika inställningar

Lägg till följande konstanter och statiska fält till den **programmet** klassen i Program.cs.

> [!NOTE]
> Du kan ange TeamName konstant till namn som du använde när du har skapat prenumerationen innehåll kontrollanten. Du kan hämta TeamName från den [innehåll kontrollant webbplats](https://westus.contentmoderator.cognitive.microsoft.com/).
> När du loggar in, markera **autentiseringsuppgifter** från den **inställningar** (kugghjulet)-menyn.
>
> Teamnamnet på din är värdet för den **ID** i den **API** avsnitt.


    /// <summary>
    /// The moderation job will use this workflow that you defined earlier.
    /// See the quickstart article to learn how to setup custom workflows.
    /// </summary>
    private const string WorkflowName = "OCR";
    
    /// <summary>
    /// The name of the team to assign the job to.
    /// </summary>
    /// <remarks>This must be the team name you used to create your 
    /// Content Moderator account. You can retrieve your team name from
    /// the Conent Moderator web site. Your team name is the Id associated 
    /// with your subscription.</remarks>
    private const string TeamName = "***";

    /// <summary>
    /// The URL of the image to create a review job for.
    /// </summary>
    private const string ImageUrl =
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png";

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are ralative the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const int latencyDelay = 45;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Revies show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "";

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Lägg till kod i automatisk måttlig, skapa en granskning och hämta jobbinformation om

> [!Note]
> I praktiken kan du ange motringning URL **CallbackEndpoint** till den URL som tar emot resultaten av manuell granskning (via en HTTP POST-begäran).

Starta genom att lägga till följande kod i **Main** metod.

    using (TextWriter writer = new StreamWriter(OutputFile, false))
    {
        using (var client = Clients.NewClient())
        {
            writer.WriteLine("Create review job for an image.");
            var content = new Content(ImageUrl);
        
            // The WorkflowName contains the nameof the workflow defined in the online review tool.
            // See the quickstart article to learn more.
            var jobResult = client.Reviews.CreateJobWithHttpMessagesAsync(
                    TeamName, "image", "contentID", WorkflowName, "application/json", content, CallbackEndpoint);

            // Record the job ID.
            var jobId = jobResult.Result.Body.JobIdProperty;

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                jobResult.Result.Body, Formatting.Indented));

            Thread.Sleep(2000);
            writer.WriteLine();

            writer.WriteLine("Get review job status.");
            var jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
                    TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                    jobDetails.Result.Body, Formatting.Indented));

            Console.WriteLine();
            Console.WriteLine("Perform manual reviews on the Content Moderator site.");
            Console.WriteLine("Then, press any key to continue.");
            Console.ReadKey();

            Console.WriteLine();
            Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
            Thread.Sleep(latencyDelay * 1000);

            writer.WriteLine("Get review details.");
            jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
            TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
            jobDetails.Result.Body, Formatting.Indented));
        }
        writer.Flush();
        writer.Close();
    }

> [!NOTE]
> Nyckeln för tjänsten för ditt innehåll kontrollant har en begäranden per hastighetsbegränsning för andra (RPS). Om du överskrider gränsen som utlöser ett undantag med 429 felkoden SDK. 
>
> En kostnadsfri nivå-nyckel har en gräns för överföringshastigheten en RPS.

## <a name="run-the-program-and-review-the-output"></a>Kör programmet och granska utdata

Du kan se följande exempel på utdata i konsolen:

    Perform manual reviews on the Content Moderator site.
    Then, press any key to continue.

Logga in på innehåll kontrollanten granska verktyget om du vill se granska väntande bilden.

Använd den **nästa** knapp för att skicka.

![Granska bild för mänsklig moderatorer](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Finns på utdata i loggfilen

> [!NOTE]
> I utdatafilen, strängarna **Teamname**, **ContentId**, **CallBackEndpoint**, och **WorkflowId** speglar de värden som du använde tidigare.

    Create moderation job for an image.
    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

    Get review details.
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": [
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


## <a name="your-callback-url-if-provided-receives-this-response"></a>URL: en motringning får om det här svaret.

Ett svar som i följande exempel visas:

> [!NOTE]
> I svaret återanrop strängarna **ContentId** och **WorkflowId** speglar de värden som du använde tidigare.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe",
        "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
        "WorkFlowId": "OCR",
        "Status": "Complete",
        "ContentType": "Image",
        "CallBackType": "Job",
        "ContentId": "contentID",
        "Metadata": {
            "hastext": "True",
            "ocrtext": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
            "imagename": "contentID"
        }
    }


## <a name="next-steps"></a>Nästa steg

[Hämta Visual Studio-lösningen](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) för denna och andra innehåll kontrollant Snabbstart för .NET, och komma igång med din integrering.
