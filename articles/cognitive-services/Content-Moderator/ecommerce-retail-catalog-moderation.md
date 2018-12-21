---
title: 'Självstudie: katalogmoderering för e-handel – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Automoderera kataloger för e-handel med maskininlärning och AI.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: sajagtap
ms.openlocfilehash: 285590435a7e3c31d45d5d154d4e430ed3252838
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256238"
---
# <a name="tutorial-ecommerce-catalog-moderation-with-machine-learning"></a>Självstudie: katalogmoderering för e-handel med maskininlärning

I den här självstudien lär du dig implementera maskininläringsbaserad intelligent katalogmoderering för e-handel genom att kombinera maskinassisterade AI-tekniker med mänsklig moderering för att skapa ett intelligent katalogsystem.

![Klassificerade produktbilder](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="business-scenario"></a>Affärsscenario

Använd maskinassisterade tekniker för att klassificera och ändra produktbilder i dessa kategorier:

1. Vuxen (nakenhet)
2. Vågat (laddat)
3. Kändisar
4. Amerikanska flaggor
5. Leksaker
6. Pennor

## <a name="tutorial-steps"></a>Självstudiesteg

Den här självstudien vägleder dig genom dessa steg:

1. Registrera dig och skapa ett Content Moderator-team.
2. Konfigurera modereringstaggar (etiketter) för potentiellt kändis- och flagginnehåll.
3. Använd Content Moderators bild-API för att söka efter potentiellt vuxet eller vågat innehåll.
4. Använd Computer Vision API för att söka efter potentiella kändisar.
5. Använd Custom Vision-tjänsten för att söka efter möjlig förekomst av flaggor.
6. Presentera nyanserade sökresultat för mänsklig granskning och slutgiltigt beslutsfattande.

## <a name="create-a-team"></a>Skapa ett team

Gå till sidan [Snabbstart](quick-start.md) för att registrera dig för Content Moderator och skapa ett team. Anteckna **Team-ID** från sidan **Autentiseringsuppgifter**.


## <a name="define-custom-tags"></a>Definiera anpassade taggar

Information om hur du lägger till anpassade taggar finns i artikeln [Taggar](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags). Utöver de inbyggda taggarna för **adult** (vuxet) och **racy** (vågat) innehåll gör de nya taggarna att granskningsverktyget kan visa beskrivande namn för taggarna.

I vårt fall definierar vi dessa anpassade taggar (**celebrity** (kändis), **flag** (flagga), **us** (USA), **toy** (leksak) och **pen** (penna)):

![Konfigurera anpassade taggar](images/tutorial-ecommerce-tags2.PNG)

## <a name="list-your-api-keys-and-endpoints"></a>Lista dina API-nycklar och slutpunkter

1. I självstudiekursen används tre API:er och motsvarande nycklar och API-slutpunkter.
2. Dina API-slutpunkter är olika baserat på din prenumerationsregioner och Content Moderators granskningsteams-ID.

> [!NOTE]
> Självstudien är utformad för att använda prenumerationsnycklar i de regioner som visas i följande slutpunkter. Se till att matcha dina API-nycklar med region-URI:erna; annars fungerar nycklarna kanske inte med följande slutpunkter:

         // Your API keys
        public const string ContentModeratorKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string ComputerVisionKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string CustomVisionKey = "XXXXXXXXXXXXXXXXXXXX";

        // Your end points URLs will look different based on your region and Content Moderator Team ID.
        public const string ImageUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/moderate/v1.0/ProcessImage/Evaluate";
        public const string ReviewUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0/teams/YOURTEAMID/reviews";
        public const string ComputerVisionUri = "https://westcentralus.api.cognitive.microsoft.com/vision/v1.0";
        public const string CustomVisionUri = "https://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/Prediction/XXXXXXXXXXXXXXXXXXXX/url";

## <a name="scan-for-adult-and-racy-content"></a>Söka efter vuxet eller vågat innehåll

1. Funktionen tar en bild-URL och en matris med nyckel/värde-par som parametrar.
2. Den anropar Content Moderators bild-API för att få poängen för vuxet och vågat innehåll.
3. Om poängen är högre än 0,4 (intervallet är från 0 till 1) anger den värdet i matrisen **ReviewTags** till **True** (Sant).
4. Matrisen **ReviewTags** (Granskningstaggar) används för att markera motsvarande tagg i granskningsverktyget.

        public static bool EvaluateAdultRacy(string ImageUrl, ref KeyValuePair[] ReviewTags)
        {
            float AdultScore = 0;
            float RacyScore = 0;

            var File = ImageUrl;
            string Body = $"{{\"DataRepresentation\":\"URL\",\"Value\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(ImageUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // {“answers”:[{“answer”:“Hello”,“questions”:[“Hi”],“score”:100.0}]}
                // Parse the response body. Blocking!
                GetAdultRacyScores(response.Content.ReadAsStringAsync().Result, out AdultScore, out RacyScore);
            }

            ReviewTags[0] = new KeyValuePair();
            ReviewTags[0].Key = "a";
            ReviewTags[0].Value = "false";
            if (AdultScore > 0.4)
            {
                ReviewTags[0].Value = "true";
            }

            ReviewTags[1] = new KeyValuePair();
            ReviewTags[1].Key = "r";
            ReviewTags[1].Value = "false";
            if (RacyScore > 0.3)
            {
                ReviewTags[1].Value = "true";
            }
            return response.IsSuccessStatusCode;
        }

## <a name="scan-for-celebrities"></a>Söka efter kändisar

1. Registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) av [API för visuellt innehåll](https://azure.microsoft.com/services/cognitive-services/computer-vision/).
2. Klicka på knappen **Ladda ned API-nyckel**.
3. Godkänn villkoren.
4. Logga in genom att välja från listan över tillgängliga Internetkonton.
5. Observera de API-nycklar som visas på tjänstsidan.
    
   ![Nycklar för API för visuellt innehåll](images/tutorial-computer-vision-keys.PNG)
    
6. Se projektkällkoden för den funktion som söker igenom bilden med API för visuellt innehåll.

         public static bool EvaluateComputerVisionTags(string ImageUrl, string ComputerVisionUri, string ComputerVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage Response = CallAPI(ComputerVisionUri, ComputerVisionKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (Response.IsSuccessStatusCode)
            {
                ReviewTags[2] = new KeyValuePair();
                ReviewTags[2].Key = "cb";
                ReviewTags[2].Value = "false";

                ComputerVisionPrediction CVObject = JsonConvert.DeserializeObject<ComputerVisionPrediction>(Response.Content.ReadAsStringAsync().Result);

                if ((CVObject.categories[0].detail != null) && (CVObject.categories[0].detail.celebrities.Count() > 0))
                {                 
                    ReviewTags[2].Value = "true";
                }
            }

            return Response.IsSuccessStatusCode;
        }

## <a name="classify-into-flags-toys-and-pens"></a>Klassificera som flaggor, leksaker och pennor

1. [Logga in](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) på [förhandsversionen av Custom Vision API](https://www.customvision.ai/).
2. Använd [snabbstarten](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) för att skapa din anpassade klassificerare för att identifiera potentiella förekomster av flaggor, leksaker och pennor.
   ![Custom Vision-träningsbilder](images/tutorial-ecommerce-custom-vision.PNG)
3. [Hämta slutpunkts-URL för förutsägelse](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api) för din anpassade klassificerare.
4. Se projektkällkoden för att se den funktion som anropar din anpassade klassificerares förutsägelseslutpunkt för att söka igenom bilden.

        public static bool EvaluateCustomVisionTags(string ImageUrl, string CustomVisionUri, string CustomVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(CustomVisionUri, CustomVisionKey, CallType.POST,
                                                   "Prediction-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // Parse the response body. Blocking!
                SaveCustomVisionTags(response.Content.ReadAsStringAsync().Result, ref ReviewTags);
            }
            return response.IsSuccessStatusCode;
        }       
 
## <a name="reviews-for-human-in-the-loop"></a>Granskningar för human-in-the-loop

1. I föregående avsnitt sökte du igenom inkommande bilder efter vuxet och vågat innehåll (Content Moderator), kändisar (Visuellt innehåll) och flaggor (Custom Vision).
2. Baserat på vår matchningströskelvärden för varje sökning görs nyanserade fall tillgängliga för mänsklig granskning i granskningsverktyget.
        public static bool CreateReview(string ImageUrl, KeyValuePair[] Metadata) {

            ReviewCreationRequest Review = new ReviewCreationRequest();
            Review.Item[0] = new ReviewItem();
            Review.Item[0].Content = ImageUrl;
            Review.Item[0].Metadata = new KeyValuePair[MAXTAGSCOUNT];
            Metadata.CopyTo(Review.Item[0].Metadata, 0);

            //SortReviewItems(ref Review);

            string Body = JsonConvert.SerializeObject(Review.Item);

            HttpResponseMessage response = CallAPI(ReviewUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            return response.IsSuccessStatusCode;
        }

## <a name="submit-batch-of-images"></a>Skicka batch med bilder

1. Den här självstudiekursen förutsätter en ”C:Test”-katalog med en textfil som har en lista över bild-URL:er.
2. Följande kod kontrollerar om filen finns och läser in alla URL:er i minnet.
            // Sök efter en testkatalog för en textfil med listan över de bild-URL:er som ska genomsökas var topdir = @"C:\test\"; var Urlsfile = topdir + "Urls.txt";

            if (!Directory.Exists(topdir))
                return;

            if (!File.Exists(Urlsfile))
            {
                return;
            }

            // Read all image URLs in the file
            var Urls = File.ReadLines(Urlsfile);

## <a name="initiate-all-scans"></a>Initiera alla genomsökningar

1. Den här toppnivåfunktionen loopar igenom alla bildwebbadresser i den textfil som vi nämnde tidigare.
2. Den söker igenom dem med varje API, och om förtroendepoängen faller inom våra kriterier skapar den en granskning för mänskliga moderatorer.
             // för varje bild-IRL i filen... foreach (var Url in Urls) { // Initiera en ny matris med granskningstaggar ReviewTags = new KeyValuePair[MAXTAGSCOUNT];

                // Evaluate for potential adult and racy content with Content Moderator API
                EvaluateAdultRacy(Url, ref ReviewTags);

                // Evaluate for potential presence of celebrity (ies) in images with Computer Vision API
                EvaluateComputerVisionTags(Url, ComputerVisionUri, ComputerVisionKey, ref ReviewTags);

                // Evaluate for potential presence of custom categories other than Marijuana
                EvaluateCustomVisionTags(Url, CustomVisionUri, CustomVisionKey, ref ReviewTags);

                // Create review in the Content Moderator review tool
                CreateReview(Url, ReviewTags);
            }

## <a name="license"></a>Licens

Alla SDK:er i Microsoft Cognitive Services och exempel är licensierade med MIT-licensen. Mer information finns i [LICENS](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Uppförandekod för utvecklare

Utvecklare som använder Cognitive Services, inklusive det här klientbiblioteket och exemplet, förväntas följa ”Uppförandekod för utvecklare i Microsoft Cognitive Services”, som finns på http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>Nästa steg

Skapa och utöka självstudien med hjälp av [projektets källfiler](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) på GitHub.
