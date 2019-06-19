---
title: Förbättra kunskapsbas - QnA Maker
titleSuffix: Azure Cognitive Services
description: Aktiv inlärning kan du förbättra kvaliteten på din kunskapsbas genom att föreslå alternativa frågor, baserat på användare-bidrag till dina frågor och svar-par. Du kan granska dessa förslag, antingen lägga till dem i befintliga frågor eller avvisa dem. Kunskapsbasen ändras inte automatiskt. Du måste acceptera förslag om alla eventuella ändringar ska börja gälla. Förslagen lägga till frågor men inte ändra eller ta bort befintliga frågor.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/19/2019
ms.author: diberry
ms.openlocfilehash: b73884e544ea1b8ee76c8a891048e6a8e17d6ab3
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204081"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Använda active learning för att förbättra din kunskapsbas

Aktiv inlärning kan du förbättra kvaliteten på din kunskapsbas genom att föreslå alternativa frågor, baserat på användare-bidrag till dina frågor och svar-par. Du kan granska dessa förslag, antingen lägga till dem i befintliga frågor eller avvisa dem. 

Kunskapsbasen ändras inte automatiskt. Du måste acceptera förslagen för ändringar ska börja gälla. Förslagen lägga till frågor men inte ändra eller ta bort befintliga frågor.

## <a name="what-is-active-learning"></a>Vad är aktiv inlärning?

QnA Maker lär sig nya fråga variationer med implicit och explicit feedback.
 
* [Implicit feedback](#how-qna-makers-implicit-feedback-works) – rankningen förstår när en användare fråga har flera svar med resultat som är mycket nära och ser detta som feedback. Du behöver inte göra något för att detta ska inträffa.
* [Explicit feedback](#how-you-give-explicit-feedback-with-the-train-api) – när flera svar med lite skillnad i resultat som returneras i kunskapsbasen klientprogrammet uppmanar användarna vilka fråga är rätt fråga. Explicit Användarfeedback skickas till QnA Maker med den [träna API](#train-api). 

Båda metoderna ger rankningen med liknande frågor som är klustrade.

## <a name="how-active-learning-works"></a>Hur active learning fungerar

Aktiv inlärning utlöses baserat på poäng för de översta några svar som returneras av QnA Maker. Om skillnaderna poäng ligger inom ett mindre intervall, sedan frågan betraktas som ett möjligt förslag (som en annan fråga) för var och en av de möjliga QnA-par. När du har accepterat den föreslagna frågan för en specifik QnA-par, avvisas de för andra par. Du måste komma ihåg att spara och träna när du tar emot förslag.

Aktiv inlärning ger bästa möjliga förslagen i fall där slutpunkterna får ett rimligt antal och mängd användningsfrågor. När 5 eller mer liknande frågor är klustrade visas föreslår varje halvtimme QnA Maker användarbaserade frågorna kunskapsbas Designer ska godkänna eller avvisa. Alla förslag är klustrade tillsammans efter likhet och övre förslag på alternativa frågor visas baserat på åtkomstfrekvensen för de specifika frågorna av slutanvändare.

När frågor föreslås i QnA Maker-portalen, måste du granska och godkänna eller avvisa dessa förslag. Det finns inte en API för att hantera förslag.

## <a name="how-qna-makers-implicit-feedback-works"></a>Så här fungerar QnA Maker implicit feedback

QnA Maker implicit feedback använder en algoritm för att fastställa poäng nära sedan aktiv inlärning förslag. Algoritm och fastställa närhet är inte en enkel beräkning. Intervall i följande exempel är inte avsedda att åtgärdas, men bör användas som en vägledning för att förstå effekten av algoritmen endast.

När en fråga poängen är mycket säker på, till exempel 80%, är en uppsättning resultat som anses för aktiv inlärning breda, cirka inom 10%. Eftersom förtroendepoäng minskar, till exempel 40%, minskar antal poäng, cirka inom % 4. 

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Hur du ger explicit feedback med Train-API

Det är viktigt att QnA Maker hämtar explicit feedback om vilka av svaren var det bästa svaret. Hur det bästa svaret bestäms är upp till dig och kan innehålla:

* Feedback från användare, välja ett av svaren.
* Affärslogik, till exempel fastställa en godtagbar poäng intervall.  
* En kombination av båda användaren feedback och affärslogik.

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>Uppgradera din runtime-versionen om du vill använda aktiv inlärning

Aktiv inlärning stöds i körningsversion 4.4.0 och senare. Om din kunskapsbas skapades på en tidigare version [uppgradera din runtime](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates) att använda den här funktionen. 

## <a name="turn-on-active-learning-to-see-suggestions"></a>Aktivera active learning för att se förslag

Aktiv inlärning är inaktiverat som standard. Aktivera den föreslagna frågor. När du aktiverar active inlärning måste skicka information från klientappen till QnA Maker. Mer information finns i [arkitektoniska flöde för att använda GenerateAnswer och träna API: er från en robot](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Välj **publicera** att publicera i knowledge base. Aktiv inlärning frågor samlas in från GenerateAnswer API förutsägelse slutpunkten endast. Frågorna till rutan i QnA Maker portal inverkar inte aktiv inlärning.

1. Om du vill aktivera active learning på QnA Maker-portalen, går du till det övre högra hörnet, väljer din **namn**går du till [ **tjänstinställningar**](https://www.qnamaker.ai/UserSettings).  

    ![Aktivera aktiv inlärning föreslagna fråga alternativ från tjänstinställningssidan. Välj ditt användarnamn på menyn längst upp till höger och välj sedan inställningarna för tjänsten.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Hitta QnA Maker-tjänsten och sedan växla **aktiv inlärning**. 

    [![Växla om funktionen aktiv inlärning på inställningssidan för tjänsten. Om det inte går att växla funktionen, kan du behöva uppgradera din tjänst.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    En gång **aktiv inlärning** är aktiverad, kunskapsbasen föreslår nya frågor med jämna mellanrum baserat på Användarinitierat frågor. Du kan inaktivera **aktiv inlärning** genom att ändra inställningen igen.

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>Godkänna en aktiv inlärning förslag i knowledge base

1. Om du vill se de föreslagna frågorna på den **redigera** kunskapsbas markerar **Visningsalternativ**och välj sedan **visa aktiv inlärning förslag**. 

    [![Välj Visa förslag om du vill se aktiv inlärning ny fråga alternativ på Redigera-avsnittet i portalen.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrera kunskapsbas med frågor och svar par att visa endast förslag genom att välja **filtrera efter förslag**.

    [![Med filtret genom att visa/dölj förslag för att visa endast aktiv inlärning föreslagna fråga alternativ.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Varje par med frågor och svar föreslår nya fråga alternativen med en bock `✔` , för att godkänna frågan eller en `x` att avvisa förslagen. Klicka på bockmarkeringen för att lägga till frågan. 

    [![Välj eller avvisa aktiv inlärning föreslagna fråga alternativ genom att välja grön bock eller red delete mark.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Du kan lägga till eller ta bort _alla förslag_ genom att välja **Lägg till alla** eller **avvisa alla** i kontextuella verktygsfältet.

1. Välj **spara och träna** att spara ändringarna i kunskapsbasen.

1. Välj **publicera** så att ändringarna ska vara tillgängliga från den [GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    När 5 eller mer liknande frågor är klustrade visas föreslår varje halvtimme QnA Maker alternativa frågor som du kan godkänna eller avvisa.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Arkitektoniska flöde för att använda GenerateAnswer och träna API: er från en robot

En bot eller andra klientprogram ska använda följande arkitektoniska flöde för att använda aktiv inlärning:

* Bot [får svaret från kunskapsbasen](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) med GenerateAnswer-API med hjälp av den `top` egenskapen för att hämta ett antal svar.
* Bot avgör explicit feedback:
    * Använda din egen [anpassad affärslogik](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user), filtrera bort låg poäng.
    * Visa lista över möjliga svar till användaren i bot eller klientprogram, och få användarens valda svar.
* Bot [skickar valda svar tillbaka till QnA Maker](#bot-framework-sample-code) med den [träna API](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Använd egenskapen längst upp i GenerateAnswer begäran för att få flera matchande svar

När du skickar en fråga till QnA Maker efter ett svar på `top` egenskapen för JSON-texten anger antalet svar ska returneras. 

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Använd egenskapen poäng tillsammans med affärslogik för att få svar på Visa användare

När klientprogrammet (till exempel en chattrobot) tar emot svaret, returneras på 3 viktiga frågor. Använd den `score` egenskapen att analysera avståndet mellan poäng. Det här närhet intervallet bestäms av egen affärslogik. 

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
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

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Klienten programmet uppföljning när frågor har liknande resultat

Ditt klientprogram visar frågor med ett alternativ för att användaren väljer _enskild fråga_ som representerar sin avsikt. 

När användaren väljer en av de befintliga frågorna, skickar klientprogrammet användarens val som feedback med QnA Maker träna API. Denna feedback är klar med aktivt learning feedback-loop. 

## <a name="train-api"></a>Träna API

Aktiv inlärning feedback skickas till QnA Maker med träna API POST-begäran. API-signaturen är:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Egenskapen för HTTP-begäran|Namn|Typ|Syfte|
|--|--|--|--|
|URL-parameter för väg|Kunskapsbas-ID|string|GUID för kunskapsbasen.|
|Host subdomain|Resursnamnet för QnAMaker|string|Värdnamnet för din QnA Maker i Azure-prenumerationen. Detta är tillgängligt på sidan inställningar när du har publicerat i knowledge base. |
|Huvud|Content-Type|string|Medietyp i texten som skickas till API: et. Standardvärdet är: `application/json`|
|Huvud|Auktorisering|string|Din slutpunktsnyckeln (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Publicera brödtext|JSON-objekt|JSON|Utbildning feedback|

JSON-texten har flera inställningar:

|Brödtext JSON-egenskap|Typ|Syfte|
|--|--|--|--|
|`feedbackRecords`|array|Lista över feedback.|
|`userId`|string|Användar-ID för den person som tar emot de föreslagna frågorna. Format för användar-ID är upp till dig. En e-postadress kan till exempel vara ett giltigt användar-ID i din arkitektur. Valfri.|
|`userQuestion`|string|Exakt denna av användarens fråga. Krävs.|
|`qnaID`|nummer|ID för fråga, finns i den [GenerateAnswer svar](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Det ser ut som ett exempel på JSON-texten:

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

Ett lyckat svar returneras statusen 204 och inget JSON-svarstexten. 

### <a name="batch-many-feedback-records-into-a-single-call"></a>Batch många feedback-poster i ett enda anrop

I klientsidan applikationen, exempelvis en robot du lagra data och sedan skicka många poster i en enda JSON-texten i den `feedbackRecords` matris. 

Det ser ut som ett exempel på JSON-texten:

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

## <a name="bot-framework-sample-code"></a>Exempelkod för bot framework

Din bot framework-kod måste anropa API träna om användarens fråga som ska användas för aktiv inlärning. Det finns två typer av kod för att skriva:

* Fastställer om frågan ska användas för aktiv inlärning
* Skicka fråga till QnA Maker träna API för aktiv inlärning

I den [Azure Bot exemplet](https://aka.ms/activelearningsamplebot), båda dessa aktiviteter planerats. 

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Exempel C# koden för att träna API med Bot Framework 4.x

Följande kod visar hur du skickar information till QnA Maker med Train-API. Detta [hela kodexemplet](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore) finns på GitHub.

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
/// <param name="host">Endpoint host of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string host, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = host + "/knowledgebases/" + kbId + "/train/";

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

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Node.js-kodexempel för träna API med Bot Framework 4.x 

Följande kod visar hur du skickar information till QnA Maker med Train-API. Detta [hela kodexemplet](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs) finns på GitHub.

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

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Aktiv inlärning sparas i den exporterade kunskapsbasen

När din app har aktiv inlärning aktiverat, och du exportera en app i `SuggestedQuestions` kolumnen i filen tsv behåller data aktiv inlärning. 

Den `SuggestedQuestions` kolumnen är ett JSON-objekt av information om implicit, `autosuggested`, och explicit, `usersuggested` feedback. Ett exempel på JSON-objektet för en enda Användarinitierat fråga om `help` är:

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

När du importera den här appen fortsätter aktiv inlärning att samla in information och rekommendera förslag på kunskapsbasen. 

## <a name="best-practices"></a>Bästa praxis

Metodtips när du använder aktiv inlärning finns [bästa praxis](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Nästa steg
 
> [!div class="nextstepaction"]
> [Använda metadata med GenerateAnswer API](metadata-generateanswer-usage.md)
