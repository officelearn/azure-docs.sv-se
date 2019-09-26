---
title: Förbättra kunskaps basen – QnA Maker
titleSuffix: Azure Cognitive Services
description: Med aktiv inlärning kan du förbättra kvaliteten på din kunskaps bas genom att föreslå alternativa frågor, baserat på användar-och användar sändningar, till din fråga och svar-paret. Du kan granska dessa förslag, antingen lägga till dem i befintliga frågor eller avvisa dem. Kunskaps basen ändras inte automatiskt. Du måste acceptera förslagen för att ändringarna ska börja gälla. Dessa förslag lägger till frågor, men ändrar inte eller tar inte bort befintliga frågor.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: ab4447c8c07f8e8315c0258cc3254e5272ab7582
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71272440"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Använda aktiv inlärning för att förbättra kunskapsbasen

Med aktiv inlärning kan du förbättra kvaliteten på din kunskaps bas genom att föreslå alternativa frågor, baserat på användar-och användar sändningar, till din fråga och svar-paret. Du kan granska dessa förslag, antingen lägga till dem i befintliga frågor eller avvisa dem. 

Kunskaps basen ändras inte automatiskt. För att alla ändringar ska börja gälla måste du godkänna förslagen. Dessa förslag lägger till frågor, men ändrar inte eller tar inte bort befintliga frågor.

## <a name="what-is-active-learning"></a>Vad är aktiv inlärning?

QnA Maker lär sig nya variationer i frågan med implicit och uttrycklig feedback.
 
* [Implicit feedback](#how-qna-makers-implicit-feedback-works) – rankningen förstår när en användar fråga har flera svar med poäng som är mycket nära och som ser ut som feedback. Du behöver inte göra något för att detta ska ske.
* [Explicit feedback](#how-you-give-explicit-feedback-with-the-train-api) – när flera svar med lite variation i poängen returneras från kunskaps basen, ber klient programmet användaren som frågan är rätt fråga om. Användarens uttryckliga feedback skickas till QnA Maker med träna- [API: et](#train-api). 

Båda metoderna ger rangordningen med liknande frågor som är klustrade.

## <a name="how-active-learning-works"></a>Så här fungerar Active Learning

Aktiv inlärning utlöses baserat på resultaten av de viktigaste svaren som returneras av QnA Maker. Om Poäng skillnaderna ligger inom ett litet intervall, anses frågan vara ett möjligt förslag (som en alternativ fråga) för varje möjligt QnA-par. När du har accepterat den föreslagna frågan för ett speciellt QnA-par avvisas den för de andra paren. Du måste komma ihåg att spara och träna när du har accepterat förslag.

Active Learning ger bästa möjliga förslag i fall där slut punkterna får en rimlig mängd och olika användnings frågor. Om 5 eller fler liknande frågor grupperas, var 30: e minut, föreslår QnA Maker de användarbaserade frågorna till kunskaps bas verktyget för att godkänna eller avvisa. Alla förslag är grupperade efter likhet och de vanligaste förslagen för alternativa frågor som visas baserat på frekvensen för specifika frågor av slutanvändare.

När frågorna föreslås i QnA Maker portal måste du granska och godkänna eller avvisa dessa förslag. Det finns inget API för att hantera förslag.

## <a name="how-qna-makers-implicit-feedback-works"></a>Så här fungerar QnA Makers implicit feedback

QnA Makers implicita feedback använder en algoritm för att fastställa poängen och gör sedan aktiva inlärnings förslag. Algoritmen för att bestämma närhet är inte en enkel beräkning. Intervallen i följande exempel är inte avsedda att korrigeras utan bör användas som vägledning för att endast förstå effekten av algoritmen.

När en frågas Poäng är mycket trygg, t. ex. 80%, är poängen som beaktas för aktiv inlärning en bred, ungefär inom 10%. När förtroende poängen minskar, till exempel 40%, minskar intervallet för poängen, ungefär inom 4%. 

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Hur du ger explicit feedback med träna-API: et

Det är viktigt att QnA Maker får uttrycklig feedback om vilka svar som var det bästa svaret. Hur det bästa svaret fastställs är upp till dig och kan innehålla:

* Feedback från användare och välj ett av svaren.
* Affärs logik, till exempel att fastställa ett acceptabelt Poäng intervall.  
* En kombination av både feedback och affärs logik.

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>Uppgradera din körnings version för att använda aktiv inlärning

Active Learning stöds i runtime-version 4.4.0 och senare. Om din kunskaps bas har skapats på en tidigare version kan du [Uppgradera körningen](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) för att använda den här funktionen. 

## <a name="turn-on-active-learning-to-see-suggestions"></a>Aktivera aktiv inlärning för att se förslag

Aktiv inlärning är inaktiverat som standard. Aktivera det om du vill se föreslagna frågor. När du har aktiverat aktiv inlärning måste du skicka information från klient appen till QnA Maker. Mer information finns i [arkitektoniskt flöde för att använda GenerateAnswer och träna API: er från en bot](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Välj **publicera** för att publicera kunskaps basen. Aktiva inlärnings frågor samlas endast in från GenerateAnswer API förutsägelse-slut punkten. Frågorna i test fönstret i QnA Makers portalen påverkar inte aktiv inlärning.

1. Om du vill aktivera aktiv inlärning i QnA Maker portal går du till det övre högra hörnet och väljer ditt **namn**, gå till [**tjänst inställningar**](https://www.qnamaker.ai/UserSettings).  

    ![Aktivera alternativ för den föreslagna frågan i Active Learning på sidan tjänst inställningar. Välj ditt användar namn på menyn längst upp till höger och välj sedan tjänst inställningar.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Hitta QnA Maker tjänsten och växla sedan **aktiv inlärning**. 

    [![På sidan tjänst inställningar växlar du till funktionen aktiv inlärning. Om du inte kan växla funktionen kan du behöva uppgradera tjänsten.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > Den exakta versionen på föregående bild visas som ett exempel. Din version kan vara annorlunda. 

    När **aktiv inlärning** har Aktiver ATS föreslår kunskaps basen nya frågor med jämna mellanrum baserat på frågor som skickats av användaren. Du kan inaktivera **aktiv inlärning** genom att växla inställningen igen.

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>Acceptera ett aktivt utbildnings förslag i kunskaps basen

Active Learning ändrar kunskaps basen eller Search Service när du har godkänt förslaget och sedan sparar och tränar. Om du godkänner förslaget kommer det att läggas till som en alternativ fråga.

1. Om du vill se de föreslagna frågorna går du till sidan **Redigera** kunskaps bas och väljer **visnings alternativ**. Välj sedan **Visa aktiva utbildnings förslag**. 

    [![I redigera-avsnittet i portalen väljer du Visa förslag för att se de aktiva alternativen för inlärnings nya frågor.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrera kunskaps basen med fråge-och svars par för att bara visa förslag genom att välja **Filtrera efter förslag**.

    [![Använd alternativet filtrera efter förslag för att bara visa de aktiva inlärnings alternativ som föreslås av den aktuella frågan.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Varje QNA-par föreslår den nya frågans alternativ med en bock `✔` markering, för att acceptera frågan `x` eller för att avvisa förslagen. Markera kryss rutan för att lägga till frågan. 

    [![Välj eller avvisa den aktiva inlärningens föreslagna fråge alternativ genom att markera den gröna bocken eller det röda ta bort-märket.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Du kan lägga till eller ta bort _alla förslag_ genom att välja **Lägg till alla** eller **Ignorera alla** i kontext verktygsfältet.

1. Välj **Spara och träna** för att spara ändringarna i kunskaps basen.

1. Välj **publicera** för att tillåta att ändringarna är tillgängliga från [GenerateAnswer-API: et](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    Om 5 eller fler liknande frågor grupperas, var 30: e minut, rekommenderar QnA Maker de alternativa frågorna som du kan acceptera eller avvisa.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Arkitektur flöde för att använda GenerateAnswer och träna API: er från en robot

En robot eller något annat klient program bör använda följande arkitektur flöde för att använda aktiv inlärning:

* Bot [hämtar svaret från kunskaps basen](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) med GenerateAnswer-API: et med hjälp `top` av egenskapen för att få ett antal svar.
* Bot avgör explicit feedback:
    * Filtrera ut låga resultat med din egen [anpassade affärs logik](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user).
    * I robot-eller klient programmet visar du en lista över möjliga svar på användaren och får användarens valda svar.
* Robot [skickar det valda svaret tillbaka till QNA Maker](#bot-framework-sample-code) med [träna-API: et](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Använd egenskapen Top i GenerateAnswer-begäran för att få flera matchande svar

När du skickar en fråga till QNA Maker för ett svar anger `top` egenskapen för JSON-texten antalet svar som ska returneras. 

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Använd score-egenskapen tillsammans med affärs logiken för att få en lista med svar för att visa användare

När klient programmet (t. ex. en chat-robot) tar emot svaret returneras de tre främsta frågorna. `score` Använd egenskapen för att analysera avståndet mellan poängen. Det här närhets intervallet bestäms av din egen affärs logik. 

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

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Uppföljning av klient program när frågor har liknande resultat

Ditt klient program visar frågorna med ett alternativ för användaren att välja _den enda fråga_ som representerar deras avsikt. 

När användaren väljer en av de befintliga frågorna skickar klient programmet användarens val som feedback med hjälp av QnA Makers träna API. Den här feedbacken Slutför den aktiva inlärnings-loopen. 

## <a name="train-api"></a>Tränings-API

Aktiva inlärnings synpunkter skickas till QnA Maker med anropet träna API POST. API-signaturen är:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Egenskap för HTTP-begäran|Name|type|Syfte|
|--|--|--|--|
|URL-rutt parameter|Kunskaps bas-ID|sträng|Din kunskaps bas-GUID.|
|Värd under domän|Resurs namn för QnAMaker|sträng|Värd namnet för din QnA Maker i din Azure-prenumeration. Detta är tillgängligt på sidan inställningar när du har publicerat kunskaps basen. |
|Huvud|Content-Type|sträng|Medie typen för den brödtext som skickas till API: et. Standardvärdet är:`application/json`|
|Huvud|Authorization|sträng|Din slut punkts nyckel (EndpointKey XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX).|
|Publicera brödtext|JSON-objekt|JSON|Feedback om utbildningen|

JSON-texten har flera inställningar:

|JSON-Body-egenskap|type|Syfte|
|--|--|--|--|
|`feedbackRecords`|array|Lista över feedback.|
|`userId`|sträng|Användar-ID för den person som godkänner de föreslagna frågorna. Formatet för användar-ID är upp till dig. En e-postadress kan till exempel vara ett giltigt användar-ID i din arkitektur. Valfritt.|
|`userQuestion`|sträng|Den exakta texten för användarens fråga. Obligatoriskt.|
|`qnaID`|nummer|ID för fråga som finns i [GenerateAnswer-svaret](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Ett exempel på en JSON-text ser ut så här:

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

Ett lyckat svar returnerar statusen 204 och ingen JSON-svars text. 

### <a name="batch-many-feedback-records-into-a-single-call"></a>Gruppera många återkopplings poster till ett enda anrop

I program på klient sidan, till exempel en bot, kan du lagra data och sedan skicka många poster i en enda JSON-text i `feedbackRecords` matrisen. 

Ett exempel på en JSON-text ser ut så här:

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

## <a name="bot-framework-sample-code"></a>Exempel kod för bot Framework

Din bot Framework-kod måste anropa tågets API, om användarens fråga ska användas för aktiv inlärning. Det finns två typer av kod att skriva:

* Avgöra om frågan ska användas för aktiv inlärning
* Skicka tillbaka frågan till QnA Makers tåg-API för aktiv inlärning

I [Azure bot-exemplet](https://aka.ms/activelearningsamplebot)har båda dessa aktiviteter programmerats. 

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Exempel C# kod för träna API med bot Framework 4. x

Följande kod visar hur du skickar information tillbaka till QnA Maker med träna-API. Detta [fullständiga kod exempel](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore) finns på GitHub.

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

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Exempel på Node. js-kod för träna API med bot Framework 4. x 

Följande kod visar hur du skickar information tillbaka till QnA Maker med träna-API. Detta [fullständiga kod exempel](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs) finns på GitHub.

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

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Aktiv inlärning sparas i den exporterade kunskaps basen

När du har aktiverat aktiv inlärning i appen och du exporterar appen, `SuggestedQuestions` behåller kolumnen i TSV-filen de aktiva inlärnings data. 

Kolumnen är ett JSON-objekt med information om implicit, `autosuggested`och explicit, `usersuggested` feedback. `SuggestedQuestions` Ett exempel på detta JSON-objekt för en enskild användare som har skickat `help` frågan av är:

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

Du kan också använda nedladdnings-API: et för att granska dessa ändringar, med hjälp av REST eller någon av de språkbaserade SDK: erna:
* [REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.alterationsextensions.getasync?view=azure-dotnet)


När du importerar om den här appen fortsätter den aktiva inlärningen att samla in information och rekommendera förslag för din kunskaps bas. 



## <a name="best-practices"></a>Bästa praxis

För bästa praxis när du använder aktiv inlärning, se [metod tips](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Nästa steg
 
> [!div class="nextstepaction"]
> [Använda metadata med GenerateAnswer-API](metadata-generateanswer-usage.md)
