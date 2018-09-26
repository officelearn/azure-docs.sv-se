---
title: Azure Content Moderator – Start moderering jobb med hjälp av .NET | Microsoft Docs
description: Hur du startar moderering jobb med hjälp av Azure Content Moderator-SDK för .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/10/2018
ms.author: sajagtap
ms.openlocfilehash: 3761552f81bd733f9c93fab40db07ef6f5a6a7f6
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181609"
---
# <a name="start-moderation-jobs-using-net"></a>Starta moderering jobb med hjälp av .NET

Den här artikeln innehåller information och kodexempel som hjälper dig att komma igång med den [Content Moderator-SDK för .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) till:
 
- Starta ett jobb för moderering för att söka och skapa granskningar för mänskliga moderatorer
- Hämta status för väntande granskning
- Spåra och få den slutgiltiga statusen för granskningen
- Skicka resultatet till Motringnings-Url

Den här artikeln förutsätter att du redan är bekant med Visual Studio och C#.

## <a name="sign-up-for-content-moderator"></a>Registrera dig för Content Moderator

Innan du kan använda Content Moderator-tjänster via REST-API: et eller SDK: N, måste en prenumerationsnyckel.
Referera till den [snabbstarten](quick-start.md) att lära dig hur du kan hämta nyckeln.

## <a name="sign-up-for-a-review-tool-account-if-not-completed-in-the-previous-step"></a>Registrera dig för ett konto för granskning-verktyget om inte slutförts i föregående steg

Om du har fått din Content Moderator från Azure-portalen, även [registrera granska verktyget konto](https://contentmoderator.cognitive.microsoft.com/) och skapa en granskningsteam. Du behöver Id-teamet och granskningsverktyget att anropa granska API för att starta ett jobb och visa granskningarna i granskningsverktyget.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Se till att din API-nyckel kan anropa API: et för granskning för att skapa en granskning

När du har slutfört föregående steg, kan det sluta med två Content Moderator-nycklar om du utgick från Azure-portalen. 

Om du planerar att använda Azure-tillhandahållna API-nyckeln i SDK-exempel, följer du stegen som beskrivs i den [med hjälp av Azure-nyckel med granska API: et](review-tool-user-guide/credentials.md#use-the-azure-account-with-the-review-tool-and-review-api) avsnittet så att dina program kan anropa API: et för granskning och skapa granskningar.

Om du använder den kostnadsfria utvärderingsversionen nyckeln som genererats av granskningsverktyget granska verktyget kontot redan vet om nyckeln och därför inga ytterligare åtgärder krävs.

## <a name="define-a-custom-moderation-workflow"></a>Definiera en anpassad moderering arbetsflöde

Ett jobb för moderering söker igenom innehållet med hjälp av API: er och använder en **arbetsflöde** att avgöra om du vill skapa granskningar eller inte.
Medan granskningsverktyget innehåller ett standardarbetsflöde, vi [definierar ett anpassat arbetsflöde](Review-Tool-User-Guide/Workflows.md) för den här snabbstarten.

Du använder namnet på arbetsflödet i din kod som startar moderering av jobbet.

## <a name="create-your-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Lägga till en ny **konsolapp (.NET Framework)** projekt i lösningen.

   Namnge projektet i exempelkoden **CreateReviews**.

1. Välj det här projektet som enda Startprojekt för lösningen.

### <a name="install-required-packages"></a>Installera de paket som krävs

Installera följande NuGet-paket:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Uppdatera programmet använder uttryck

Ändra programmet är med hjälp av uttryck.

    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="create-the-content-moderator-client"></a>Skapa Content Moderator-klienten

Lägg till följande kod för att skapa en Content Moderator-klient för din prenumeration.

> [!IMPORTANT]
> Uppdatera den **AzureRegion** och **CMSubscriptionKey** fält med värdena för din region identifierare och prenumeration nyckel.


    /// <summary>
    /// Wraps the creation and configuration of a Content Moderator client.
    /// </summary>
    /// <remarks>This class library contains insecure code. If you adapt this 
    /// code for use in production, use a secure method of storing and using
    /// your Content Moderator subscription key.</remarks>
    public static class Clients
    {
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, westus.
        /// </summary>
        private static readonly string AzureRegion = "YOUR API REGION";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"https://{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR API KEY";

        /// <summary>
        /// Returns a new Content Moderator client for your subscription.
        /// </summary>
        /// <returns>The new client.</returns>
        /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
        /// When you have finished using the client,
        /// you should dispose of it either directly or indirectly. </remarks>
        public static ContentModeratorClient NewClient()
        {
            // Create and initialize an instance of the Content Moderator API wrapper.
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

            client.Endpoint = AzureBaseURL;
            return client;
        }
    }

### <a name="initialize-application-specific-settings"></a>Initiera programspecifika inställningar

Lägg till följande konstanter och statiska fält till den **programmet** klassen i Program.cs.

> [!NOTE]
> Du kan ange TeamName konstant namnet du använde när du skapade prenumerationen Content Moderator. Du kan hämta TeamName från den [Content Moderator-webbplats](https://westus.contentmoderator.cognitive.microsoft.com/).
> När du har loggat in väljer **autentiseringsuppgifter** från den **inställningar** (kugghjulet)-menyn.
>
> Ditt Teamnamn är värdet för den **Id** i den **API** avsnittet.


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
    /// the Content Moderator web site. Your team name is the Id associated 
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
    /// <remarks>Relative paths are relative to the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const int latencyDelay = 45;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Reviews show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "";

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Lägg till kod till automatisk måttlig, skapa en granskning, så får Jobbinformationen

> [!Note]
> I praktiken ställer du in Webbadressen för återanrop **CallbackEndpoint** till den URL som tar emot resultatet av manuell granskning (via en HTTP POST-begäran).

Starta genom att lägga till följande kod till den **Main** metod.

    using (TextWriter writer = new StreamWriter(OutputFile, false))
    {
        using (var client = Clients.NewClient())
        {
            writer.WriteLine("Create review job for an image.");
            var content = new Content(ImageUrl);
        
            // The WorkflowName contains the name of the workflow defined in the online review tool.
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
> Din nyckel för Content Moderator-tjänsten har en begäranden per sekund (RPS) hastighetsbegränsning. Om du överskrider gränsen utlöser ett undantag med en 429 felkod i SDK: N. 
>
> En nyckel för kostnadsfria nivån har en hastighetsbegränsning för en RPS.

## <a name="run-the-program-and-review-the-output"></a>Kör programmet och granska resultatet

Du kan se följande exempel på utdata i konsolen:

    Perform manual reviews on the Content Moderator site.
    Then, press any key to continue.

Logga in på Content Moderator granska verktyg för att se den väntande avbildningen granska.

Använd den **nästa** knapp för att skicka.

![Bild granskning för mänskliga moderatorer](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Se exempel på utdata i loggfilen

> [!NOTE]
> I utdatafilen, strängarna **Teamname**, **ContentId**, **CallBackEndpoint**, och **WorkflowId** de värden som du använde tidigare.

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


## <a name="your-callback-url-if-provided-receives-this-response"></a>Motringnings-Url om du får det här svaret.

Du ser ett svar som i följande exempel:

> [!NOTE]
> I svaret återanrop strängarna **ContentId** och **WorkflowId** de värden som du använde tidigare.

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

Hämta den [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) och [Visual Studio-lösning](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) för denna och andra Content Moderator-Snabbstart för .NET, och kom igång med din integrering.
