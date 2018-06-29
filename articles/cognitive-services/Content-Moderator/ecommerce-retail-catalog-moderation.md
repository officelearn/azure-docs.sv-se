---
title: e-handel katalogen avbrottsmoderering med machine learning och AI med Azure innehåll kontrollant | Microsoft Docs
description: Måttlig automatiskt e-handel kataloger med machine learning och AI
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/25/2017
ms.author: sajagtap
ms.openlocfilehash: 6177758eaa3e611ad67da0778d889df48b052d90
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37095759"
---
# <a name="ecommerce-catalog-moderation-with-machine-learning"></a>e-handel katalogen avbrottsmoderering med machine learning

Vi Lär dig hur du implementerar machine-learning-baserade intelligent e-handel katalogen avbrottsmoderering genom att kombinera datorn stödd AI-teknik med mänsklig avbrottsmoderering att tillhandahålla en intelligent katalogen system i den här självstudiekursen.

![Klassificerad produktbilder](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="business-scenario"></a>Affärsscenario

Använd dator-stödd tekniker för att klassificera och måttlig produktbilder i dessa kategorier:

1. Vuxen (nakenhet)
2. Dyr (något)
3. Kändisars
4. USA flaggor
5. Toys
6. Pennor

## <a name="tutorial-steps"></a>Självstudiekursens steg

Guiden leder dig genom stegen:

1. Registrera dig och skapa ett team av innehåll kontrollanten.
2. Konfigurera avbrottsmoderering taggar (etiketter) för potentiella ryktbarhet och flaggan innehåll.
3. Använda innehåll kontrollant avbildningen API för att söka efter potentiella innehåll för vuxna och dyr.
4. Använd datorn Vision API för att söka efter potentiella Kändisars.
5. Använda anpassade Vision tjänsten om du vill söka efter möjlig förekomst av flaggor.
6. Presentera nuanced sökresultatet för mänsklig granskning och slutgiltiga beslutsfattande.

## <a name="create-a-team"></a>Skapa ett team

Referera till den [Quickstart](quick-start.md) sidan om du vill registrera dig för innehåll kontrollant och skapa ett team. Observera den **Team ID** från den **autentiseringsuppgifter** sidan.


## <a name="define-custom-tags"></a>Definiera anpassade taggar

Referera till den [taggar](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) artikeln om du vill lägga till anpassade taggar. Förutom inbyggt **vuxna** och **dyr** taggar, nya etiketter kan granska-verktyget för att visa beskrivande namn för taggarna.

I vårt fall vi definiera dessa anpassade taggar (**Kändisarnas**, **flaggan**, **oss**, **leksaken**, **penna**):

![Konfigurera anpassade taggar](images/tutorial-ecommerce-tags2.PNG)

## <a name="list-your-api-keys-and-endpoints"></a>Visa API-nycklar och slutpunkter

1. Kursen använder tre API: er och motsvarande nycklar och API-slutpunkter.
2. API-slutpunkter skiljer sig beroende på din prenumeration regioner och granska Innehållsteamet kontrollant-ID.

> [!NOTE]
> Guiden är utformad för att använda prenumerationen nycklar i regionerna visas i följande slutpunkter. Se till att matcha dina API-nycklar med region URI: er annars dina nycklar inte fungerar med följande slutpunkter:

         // Your API keys
        public const string ContentModeratorKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string ComputerVisionKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string CustomVisionKey = "XXXXXXXXXXXXXXXXXXXX";

        // Your end points URLs will look different based on your region and Content Moderator Team ID.
        public const string ImageUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/moderate/v1.0/ProcessImage/Evaluate";
        public const string ReviewUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0/teams/YOURTEAMID/reviews";
        public const string ComputerVisionUri = "https://westcentralus.api.cognitive.microsoft.com/vision/v1.0";
        public const string CustomVisionUri = "https://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/Prediction/XXXXXXXXXXXXXXXXXXXX/url";

## <a name="scan-for-adult-and-racy-content"></a>Söka efter innehåll för vuxna och dyr

1. Funktionen tar en bild-URL och en matris med nyckel-värdepar som parametrar.
2. Innehåll kontrollanten avbildningen API för att få resultat vuxen och Racy anropas.
3. Om poängen är större än 0,4 (intervall är från 0 till 1) det anger att värdet i den **ReviewTags** matris till **SANT**.
4. Den **ReviewTags** matris används för att markera taggen motsvarande i verktyget granska.

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

## <a name="scan-for-celebrities"></a>Sök efter Kändisars

1. Registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) av den [datorn Vision API](https://azure.microsoft.com/services/cognitive-services/computer-vision/).
2. Klicka på den **hämta API-nyckel** knappen.
3. Acceptera villkoren.
4. Om du vill logga in, Välj från listan över Internet-konton som är tillgängliga.
5. Observera API-nycklar som visas på sidan service.
    
   ![Datorn Vision API-nycklar](images/tutorial-computer-vision-keys.PNG)
    
6. Referera till projektet källkoden för den funktion som söker igenom avbildningen med datorn Vision API.

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

## <a name="classify-into-flags-toys-and-pens"></a>Klassificera i flaggor och toys pennor

1. [Logga in](https://azure.microsoft.com/en-us/services/cognitive-services/custom-vision-service/) till den [anpassad Vision API preview](https://www.customvision.ai/).
2. Använd den [Quickstart](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) att bygga din anpassade klassificerare för att upptäcka potentiella flaggor och toys pennor.
   ![Anpassade Vision utbildning bilder](images/tutorial-ecommerce-custom-vision.PNG)
3. [Hämta förutsägelse slutpunkts-URL](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api) för din anpassade klassificerare.
4. Referera till projektet källkoden för att se den funktion som anropar anpassade klassificerare förutsägelse slutpunkten för genomsökning av avbildningen.

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
 
## <a name="reviews-for-human-in-the-loop"></a>Granskning av hr-i-the-loop

1. I föregående avsnitt skannade du inkommande avbildningar för vuxna och dyr (innehåll kontrollant) Kändisars (dator Vision) och flaggor (anpassad Vision).
2. Baserat på vår matchar tröskelvärden för varje sökning, tillgängliggöra nuanced fall för mänsklig granskning i verktyget granska.
        offentlig statisk bool CreateReview (string ImageUrl, KeyValuePair [] Metadata) {

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

## <a name="submit-batch-of-images"></a>Skicka batchen med bilder

1. Den här kursen förutsätter en ”C:Test”-katalog med en textfil som innehåller en lista över bild-URL: er.
2. Följande kod kontrollerar om filen finns och läser in alla URL: er i minnet.
            Sök efter en test-katalog för en textfil med listan över bild-URL: er att skanna var topdir = @ ”C:\test\"; var Urlsfile = topdir +” Urls.txt ”;

            if (!Directory.Exists(topdir))
                return;

            if (!File.Exists(Urlsfile))
            {
                return;
            }

            // Read all image URLs in the file
            var Urls = File.ReadLines(Urlsfile);

## <a name="initiate-all-scans"></a>Initiera alla genomsökningar

1. Översta funktionen loop genom avbildning URL: er i textfilen som vi nämnt tidigare.
2. Söks igenom dem med varje API och om matchar förtroende poäng faller inom vårt kriterier, skapar du en granskning för mänsklig kontrollanter.
             för varje bild-URL i filen... foreach (var Url-adresser) {/ / Initiatize en ny granska taggar array ReviewTags = ny KeyValuePair [MAXTAGSCOUNT];

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

Alla Microsoft kognitiva Services SDK: er och prover licensieras med MIT-licensen. Mer information finns i [licens](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Utvecklarens regler för uppförande

Utvecklare som använder kognitiva tjänster, inklusive den här klientbiblioteket & exemplet förväntas följa ”Developer koden för genomföra för Microsoft kognitiva Services”, finns på http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>Nästa steg

Skapa och utöka självstudier med hjälp av den [projicera källfilerna](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) på Github.
