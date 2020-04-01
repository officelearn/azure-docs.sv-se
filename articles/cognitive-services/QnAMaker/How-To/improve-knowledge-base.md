---
title: Förbättra kunskapsbasen - QnA Maker
description: Förbättra kvaliteten på din kunskapsbas med aktivt lärande. Granska, acceptera eller avvisa, lägg till utan att ta bort eller ändra befintliga frågor.
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1eb0ed42f700c14350a5e4f1eff9b7592cbf8ef6
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474895"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>Acceptera aktivt lärande föreslagna frågor i kunskapsbasen


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

Aktiv inlärning ändrar kunskapsbasen eller söktjänsten när du har godkänt förslaget och sedan sparar och tränar. Om du godkänner förslaget läggs det till som en alternativ fråga.

## <a name="turn-on-active-learning"></a>Aktivera aktiv inlärning

För att kunna se föreslagna frågor måste du [aktivera aktiv inlärning](use-active-learning.md) för QnA Maker-resursen.

## <a name="view-suggested-questions"></a>Visa föreslagna frågor

1. Om du vill se de föreslagna frågorna väljer du **Visa alternativ**på sidan **Redigera** **kunskapsbas**och väljer sedan Visa aktiva utbildningsförslag .

    [![I avsnittet Redigera i portalen väljer du Visa förslag för att se det aktiva inlärningens nya frågealternativ.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrera kunskapsbasen med fråge- och svarspar för att bara visa förslag genom att välja **Filter efter förslag**.

    [![Använd filter för förslag växla för att visa endast den aktiva inlärningens föreslagna frågealternativ.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Varje QnA-par föreslår de nya frågealternativen med bock, `✔` `x` för att acceptera frågan eller avvisa förslagen. Markera kryssrutan för att lägga till frågan.

    [![Markera eller avvisa aktiva utbildningsalternativ för aktivt lärande genom att markera den gröna bocken eller den röda borttagningsmarkeringen.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Du kan lägga till eller ta bort _alla förslag_ genom att välja Lägg **till alla** eller Avvisa **alla** i det kontextuella verktygsfältet.

1. Välj **Spara och träna** om du vill spara ändringarna i kunskapsbasen.

1. Välj **Publicera** om du vill att ändringarna ska vara tillgängliga från [GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    När 5 eller fler liknande frågor är klustrade, var 30:e minut, föreslår QnA Maker de alternativa frågorna som du kan acceptera eller avvisa.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Arkitektoniskt flöde för att använda GenerateAnswer- och Train API:er från en bot

En bot eller annat klientprogram bör använda följande arkitektoniska flöde för att använda aktiv inlärning:

* Bot [får svaret från kunskapsbasen](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) med GenerateAnswer API, med hjälp av egenskapen `top` för att få ett antal svar.
* Bot bestämmer explicit feedback:
    * Med hjälp av din egen [anpassade affärslogik](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)filtrerar du bort låga poäng.
    * I bot eller klient-ansökan, visa lista över möjliga svar till användaren och få användarens valda svar.
* Bot [skickar valt svar tillbaka till QnA Maker](#bot-framework-sample-code) med Train [API](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Använd den översta egenskapen i GenerateAnswer-begäran för att få flera matchande svar

När du skickar en fråga till QnA `top` Maker för ett svar anger JSON-brödtextens egendom antalet svar som ska returneras.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Använd egenskapen score tillsammans med affärslogik för att få en lista med svar för att visa användare

När klientprogrammet (till exempel en chattrobot) får svaret returneras de tre vanligaste frågorna. Använd `score` egenskapen för att analysera närheten mellan poängen. Detta närhetsområde bestäms av din egen affärslogik.

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ *+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Uppföljning av klientprogram när frågor har liknande poäng

Klientprogrammet visar frågorna med ett alternativ för användaren att välja _den enda fråga_ som mest representerar deras avsikt.

När användaren väljer en av de befintliga frågorna skickar klientprogrammet användarens val som feedback med QnA Maker's Train API. Den här feedbacken slutför den aktiva feedbackloopen för inlärning.

## <a name="train-api"></a>Tränings-API

Feedback på aktiv inlärning skickas till QnA Maker med begäran om Tåg-API POST. API-signaturen är:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Egenskapen HTTP-begäran|Namn|Typ|Syfte|
|--|--|--|--|
|Parameter för URL-väg|Id för kunskapsbas|sträng|GUID för din kunskapsbas.|
|Anpassad underdomän|QnAMaker-resursnamn|sträng|Resursnamnet används som anpassad underdomän för QnA Maker. Detta är tillgängligt på sidan Inställningar när du har publicerat kunskapsbasen. Det är listat `host`som .|
|Huvud|Content-Type|sträng|Medietypen för brödtexten som skickas till API:et. Standardvärdet är:`application/json`|
|Huvud|Auktorisering|sträng|Din slutpunktsnyckel (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Post Kropp|JSON-objekt|JSON|Träningsfeedbacken|

JSON-kroppen har flera inställningar:

|JSON kropp egendom|Typ|Syfte|
|--|--|--|--|
|`feedbackRecords`|matris|Lista över feedback.|
|`userId`|sträng|Användar-ID för den person som accepterar de föreslagna frågorna. Användar-ID-formatet är upp till dig. En e-postadress kan till exempel vara ett giltigt användar-ID i din arkitektur. Valfri.|
|`userQuestion`|sträng|Exakt text i användarens fråga. Krävs.|
|`qnaID`|nummer|ID för fråga, som finns i [GenerateAnswer svar](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Ett exempel JSON kropp ser ut:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

Ett lyckat svar returnerar statusen 204 och ingen JSON-svarstext.

### <a name="batch-many-feedback-records-into-a-single-call"></a>Batch många feedbackposter i ett enda samtal

I klientprogrammet, till exempel en bot, kan du lagra data och sedan skicka många `feedbackRecords` poster i en enda JSON-brödtext i matrisen.

Ett exempel JSON kropp ser ut:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Exempelkod för robotarramverk

Din bot framework-kod måste anropa Train API, om användarens fråga ska användas för aktiv inlärning. Det finns två bitar av kod att skriva:

* Ta reda på om frågan ska användas för aktiv inlärning
* Skicka tillbaka frågan till QnA Maker's Train API för aktiv inlärning

I [exemplet Med Azure Bot](https://aka.ms/activelearningsamplebot)har båda dessa aktiviteter programmerats.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Exempel C#-kod för Tåg-API med Bot Framework 4.x

Följande kod visar hur du skickar tillbaka information till QnA Maker med Tåg-API:et.

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Exempel nod.js-kod för Tåg-API med Bot Framework 4.x

Följande kod visar hur du skickar tillbaka information till QnA Maker med Tåg-API:et.

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);

            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Aktivt lärande sparas i den exporterade kunskapsbasen

När din app har aktiverat aktiv inlärning och `SuggestedQuestions` du exporterar appen behåller kolumnen i tsv-filen aktiva utbildningsdata.

Kolumnen `SuggestedQuestions` är ett JSON-objekt med `autosuggested`information om `usersuggested` implicit, och explicit feedback. Ett exempel på detta JSON-objekt för `help` en enda användarfråga är:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

Du kan också använda api:et för hämtningsändringar för att granska dessa ändringar med REST eller någon av de språkbaserade SDK:erna:
* [REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.alterationsextensions.getasync?view=azure-dotnet)


När du importerar om den här appen fortsätter den aktiva inlärningen att samla in information och rekommendera förslag på din kunskapsbas.



## <a name="best-practices"></a>Bästa praxis

Bästa praxis finns i [Metodtips](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använda metadata med API för GenerateAnswer](metadata-generateanswer-usage.md)
