---
title: Bing avbildningen överför efter insikter | Microsoft Docs
description: Konsolprogram som använder Bing avbildningen Sök-API för att ladda upp en bild och få insikter om bilden.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 12/07/2017
ms.author: v-gedod
ms.openlocfilehash: f0bf32a9951527a072fffe464f6b5f50d0f237a2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351459"
---
# <a name="tutorial-bing-image-upload-for-insights"></a>Självstudier: Bing avbildningen överför efter insikter

Bing avbildningen Sök API ger en `POST` alternativ för att överföra en avbildning och söka efter information som är relevanta för bilden. Den här C#-konsolprogram överför en bild med slutpunkten Image-sökning och få information om bilden.
Resultatet är en kort, JSON-objekt, till exempel följande:

![[JSON-resultaten]](media/cognitive-services-bing-images-api/jsonResult.jpg)

I den här självstudien beskrivs hur du:

> [!div class="checklist"]
> * Sök bild `/details` slutpunkt i en `POST` begäran
> * Ange huvuden för begäran
> * Använd URL-parametrar för att ange resultat
> * Överför avbildningen data och skicka den `POST` begäran
> * Skriv ut JSON-resultat till konsolen

## <a name="app-components"></a>Komponenter för appar

Självstudiekurs programmet innehåller tre delar:

> [!div class="checklist"]
> * Slutpunktskonfigurationen för att ange Bing Image-sökning slutpunkt och nödvändiga rubriker
> * Bild filöverföringar för `POST` begäran till slutpunkten
> * Parsning av JSON-resultat som är information som returnerades från den `POST` begäran

## <a name="scenario-overview"></a>Scenarioöversikt
Det finns [tre Image-sökning slutpunkter](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint). Den `/details` endpoint kan använda en `POST` begäran med bilddata i brödtexten i begäran.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Den `modules` följande för URL-parametern `/details?` anger vilken typ av information om resultaten innehåller:
* `modules=All`
* `modules=RecognizedEntities` (personer eller platser som visas i bilden)

Ange `modules=All` i den `POST` förfrågan om att hämta JSON-text som innehåller följande lista:
* `bestRepresentativeQuery` -en Bing-fråga som returnerar bilder liknar den överförda bilden
* `detectedObjects` till exempel en avgränsningsram eller aktiva punkter i avbildningen
* `image` metadata
* `imageInsightsToken` -token för en efterföljande `GET` begäran som hämtar `RecognizedEntities` (personer eller platser som visas i bilden) 
* `imageTags`
* `pagesIncluding` -Webbsidor med avbildningen
* `relatedSearches`
* `visuallySimilarImages`

Ange `modules=RecognizedEntities` i den `POST` förfrågan om att hämta endast den `imageInsightsToken`, som används i ett efterföljande `GET` begäran. Den identifierar eller placerar visas i bilden.

## <a name="webclient-and-headers-for-the-post-request"></a>WebClient och huvuden för POST-begäran
Skapa en `WebClient` objektet och ange rubrikerna. Alla förfrågningar till Bing Search API kräver en `Ocp-Apim-Subscription-Key`. En `POST` begäran om att ladda upp en avbildning måste även ange `ContentType: multipart/form-data`.

```
            WebClient client = new WebClient();
            client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            client.Headers["ContentType"] = "multipart/form-data"; 
```

## <a name="upload-the-image-and-get-results"></a>Överför avbildningen och få resultat

Den `WebClient` klassen innehåller den `UpLoadFile` metod för att formatera data för den `POST` begäran. Formateras den `RequestStream` och anropar `HttpWebRequest`, undvika mycket komplex.
Anropa `WebClient.UpLoadFile` med den `/details` slutpunkt och image-filen ska överföras. Svaret är binära data som enkelt kan omvandlas till JSON. 

Använda JSON-text för att initiera en instans av den `SearchResult` struktur (finns i [programkällkod](tutorial-image-post-source.md) för kontext).
```        
         const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";

        // The image to upload. Replace with your file and path.
        const string imageFile = "ansel-adams-tetons-snake-river.jpg";
            
        byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
        var json = System.Text.Encoding.Default.GetString(resp);

        // Create result object for return
        var searchResult = new SearchResult()
        {
            jsonResult = json,
            relevantHeaders = new Dictionary<String, String>()
        };
```

## <a name="print-the-results"></a>Skriva ut resultatet
Resten av koden Parsar JSON-resultat och skriver ut den till konsolen.

```
        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
```
## <a name="get-request-using-the-imageinsightstoken"></a>Hämta begäran med ImageInsightsToken
Att använda den `ImageInsightsToken` returneras med resultatet av en `POST`, skapa en `GET` begäran på följande:
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```
Om det finns identifierbar personer eller platser i avbildningen kan returnerar denna begäran information om dessa.
Den [Snabbstart](https://docs.microsoft.com/azure/cognitive-services/bing-image-search) innehåller flera kodexempel.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Bing avbildningen Sök API-referens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

