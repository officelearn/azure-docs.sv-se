---
title: 'Snabbstart: Anropa tjänsten Text Analytics med hjälp av Go SDK'
titleSuffix: Azure Cognitive Services
description: Hämta information och exempel på kod som hjälper dig att snabbt komma igång med API för textanalys i Microsoft Cognitive Services.
services: cognitive-services
author: laramume
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/23/2019
ms.author: aahi
ms.openlocfilehash: 44def29292bc882fdaa08ff76667742756f178b8
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299613"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-go-sdk"></a>Snabbstart: Anropa tjänsten Text Analytics med hjälp av Go SDK 
<a name="HOLTop"></a>

Använd den här snabbstarten om du vill analysera språk med Text Analytics SDK för Go. Den här artikeln visar hur du identifiera språk, analysera sentiment, extrahera nyckelfraser och identifiera länkade entiteter. REST API är kompatibla med de flesta programmeringsspråk, innehåller SDK ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/cognitiveservices).

## <a name="prerequisites"></a>Nödvändiga komponenter

* Text Analytics [SDK för Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Du måste även ha [slutpunkten och åtkomstnyckeln](../How-tos/text-analytics-how-to-access-key.md) som genererades åt dig vid registreringen.

## <a name="set-up-a-new-project"></a>Konfigurera ett nytt projekt

Skapa ett nytt Go-projekt i Kodredigerare eller IDE. Lägg sedan till följande importuttryck i Go-filen.

```golang
import (
    "context"
    "encoding/json"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics"
    "github.com/Azure/go-autorest/autorest"
)
```

Lägg till följande funktioner i projektet som de flesta parametrar och egenskaper för den här snabbstarten förväntar dig sträng och bool pekare.

```golang
// returns a pointer to the string value passed in.
func StringPointer(v string) *string {
    return &v
}

// returns a pointer to the bool value passed in.
func BoolPointer(v bool) *bool {
    return &v
}
```

## <a name="create-text-analytics-client-and-authenticate-credentials"></a>Skapa Text Analytics-klient och autentisera autentiseringsuppgifter

I huvudfunktionen i ditt projekt, skapa en ny `TextAnalytics` objekt. Använd rätt Azure-regionen för din Text Analytics-prenumeration. Till exempel: `https://eastus.api.cognitive.microsoft.com`. Om du använder en utvärderingsversion nyckel, behöver du inte uppdatera platsen.

```golang
//Replace 'eastus' with the correct region for your Text Analytics subscription
textAnalyticsClient := textanalytics.New("https://eastus.api.cognitive.microsoft.com")
```

Skapa en variabel för din nyckel och skickar den till funktionen `autorest.NewCognitiveServicesAuthorizer` som sedan skickas till klientens `authorizer` egenskapen.

```golang
subscriptionKey := "<<subscriptionKey>>"
textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscriptionKey)
```

## <a name="sentiment-analysis"></a>Sentimentanalys

Skapa en ny funktion som kallas `SentimentAnalysis()` som tar klienten som skapade tidigare. Skapa en lista över `MultiLanguageInput` objekt, som innehåller de dokument som du vill analysera. Varje objekt innehåller en `id`, `Language` och en `text` attribut. Den `text` attributet lagrar texten som ska analyseras, `language` är språket för dokumentet och `id` kan vara vilket värde. 

```golang
func SentimentAnalysis(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("I had the best day of my life."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("1"),
            Text:StringPointer("This was a waste of my time. The speaker put me to sleep."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("es"),
            ID:StringPointer("2"),
            Text:StringPointer("No tengo dinero ni nada que dar..."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("it"),
            ID:StringPointer("3"),
            Text:StringPointer("L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

I samma funktion, anropa `textAnalyticsclient.Sentiment()` och få resultatet. Sedan gå igenom resultat och skriva ut varje dokument-ID och attitydsresultatet. Ett värde närmare 0 anger negativ känsla, medan en poäng närmare 1 anger positiv känsla.

```golang
result, _ := textAnalyticsclient.Sentiment(ctx, BoolPointer(false), &batchInput)
batchResult := textanalytics.SentimentBatchResult{}
jsonString, _ := json.Marshal(result.Value)
json.Unmarshal(jsonString, &batchResult)

// Printing sentiment results
for _,document := range *batchResult.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Sentiment Score: %f\n",*document.Score)
}

// Printing document errors
fmt.Println("Document Errors")
for _,error := range *batchResult.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
```

I huvudfunktionen i ditt projekt, anropa `SentimentAnalysis()`.

### <a name="output"></a>Utdata

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Språkspårning

Skapa en ny funktion som kallas `LanguageDetection()` som tar klienten som skapade tidigare. Skapa en lista över `LanguageInput` objekt, som innehåller de dokument som du vill analysera. Varje objekt innehåller en `id` och en `text` attribut. Den `text` attributet lagrar texten som ska analyseras, och `id` kan vara vilket värde. 

```golang
func LanguageDetection(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.LanguageInput {
        textanalytics.LanguageInput {
            ID:StringPointer("0"),
            Text:StringPointer("This is a document written in English."),
        },
        textanalytics.LanguageInput {
            ID:StringPointer("1"),
            Text:StringPointer("Este es un document escrito en Español."),
        },
        textanalytics.LanguageInput {
            ID:StringPointer("2"),
            Text:StringPointer("这是一个用中文写的文件"),
        },
    }

    batchInput := textanalytics.LanguageBatchInput{Documents:&inputDocuments}
}
```

I samma funktion, anropa `textAnalyticsclient.DetectLanguage()` och få resultatet. Sedan gå igenom resultat och skriva ut varje dokument-ID och identifierat språk.

```golang
result, _ := textAnalyticsclient.DetectLanguage(ctx, BoolPointer(false), &batchInput)

// Printing language detection results
for _,document := range *result.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Detected Languages with Score: ")
    for _,language := range *document.DetectedLanguages{
        fmt.Printf("%s %f,",*language.Name, *language.Score)
    }
    fmt.Println()
}

// Printing document errors
fmt.Println("Document Errors")
for _,error := range *result.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
```

I huvudfunktionen i ditt projekt, anropa `LanguageDetection()`.

### <a name="output"></a>Utdata

```console
Document ID: 0 Detected Languages with Score: English 1.000000,
Document ID: 1 Detected Languages with Score: Spanish 1.000000,
Document ID: 2 Detected Languages with Score: Chinese_Simplified 1.000000,
```

## <a name="entity-recognition"></a>Igenkänning av entiteter

Skapa en ny funktion som kallas `ExtractEntities()` som tar klienten som skapade tidigare. Skapa en lista över `MultiLanguageInput` objekt, som innehåller de dokument som du vill analysera. Varje objekt innehåller en `id`, `language`, och en `text` attribut. Den `text` attributet lagrar texten som ska analyseras, `language` är språket för dokumentet och `id` kan vara vilket värde. 

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("es"),
            ID:StringPointer("1"),
            Text:StringPointer("La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

I samma funktion, `call textAnalyticsclient.Entities()` och få resultatet. Iterera sedan resultaten och Skriv ut varje dokument är ID: T och extraherade entiteter poäng.

```golang
    result, _ := textAnalyticsclient.Entities(ctx, BoolPointer(false), &batchInput)

    // Printing extracted entities results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Entities:\n")
        for _,entity := range *document.Entities{
            fmt.Printf("\t\tName: %s\tType: %s",*entity.Name, *entity.Type)
            if entity.SubType != nil{
                fmt.Printf("\tSub-Type: %s\n", *entity.SubType)
            }
            fmt.Println()
            for _,match := range *entity.Matches{
                fmt.Printf("\t\t\tOffset: %v\tLength: %v\tScore: %f\n", *match.Offset, *match.Length, *match.EntityTypeScore)
            }
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
```

I huvudfunktionen i ditt projekt, anropa `ExtractEntities()`.

### <a name="output"></a>Utdata

```console
Document ID: 0
    Extracted Entities:
        Name: Microsoft Type: Organization
            Offset: 0   Length: 9   Score: 1.000000
        Name: Bill Gates    Type: Person
            Offset: 25  Length: 10  Score: 0.999847
        Name: Paul Allen    Type: Person
            Offset: 40  Length: 10  Score: 0.998841
        Name: April 4   Type: Other
            Offset: 54  Length: 7   Score: 0.800000
        Name: April 4, 1975 Type: DateTime  Sub-Type: Date

            Offset: 54  Length: 13  Score: 0.800000
        Name: BASIC Type: Other
            Offset: 89  Length: 5   Score: 0.800000
        Name: Altair 8800   Type: Other
            Offset: 116 Length: 11  Score: 0.800000

Document ID: 1
    Extracted Entities:
        Name: Microsoft Type: Organization
            Offset: 21  Length: 9   Score: 0.999756
        Name: Redmond (Washington)  Type: Location
            Offset: 60  Length: 7   Score: 0.991128
        Name: 21 kilómetros Type: Quantity  Sub-Type: Dimension

            Offset: 71  Length: 13  Score: 0.800000
        Name: Seattle   Type: Location
            Offset: 88  Length: 7   Score: 0.999878
```

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

Skapa en ny funktion som kallas `ExtractKeyPhrases()` som tar klienten som skapade tidigare. Skapa en lista över `MultiLanguageInput` objekt, som innehåller de dokument som du vill analysera. Varje objekt innehåller en `id`, `language`, och en `text` attribut. Den `text` attributet lagrar texten som ska analyseras, `language` är språket för dokumentet och `id` kan vara vilket värde.

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("ja"),
            ID:StringPointer("0"),
            Text:StringPointer("猫は幸せ"),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("de"),
            ID:StringPointer("1"),
            Text:StringPointer("Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("2"),
            Text:StringPointer("My cat might need to see a veterinarian."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("es"),
            ID:StringPointer("3"),
            Text:StringPointer("A mi me encanta el fútbol!"),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

Anropa textAnalyticsclient.KeyPhrases() i samma funktion, och få resultat. Sedan gå igenom resultat och skriva ut varje dokument-ID och extrahera nyckelfraser.

```golang
    result, _ := textAnalyticsclient.KeyPhrases(ctx, BoolPointer(false), &batchInput)

    // Printing extracted key phrases results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Key Phrases:\n")
        for _,keyPhrase := range *document.KeyPhrases{
            fmt.Printf("\t\t%s\n",keyPhrase)
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
```

I huvudfunktionen i ditt projekt, anropa `ExtractKeyPhrases()`.

### <a name="output"></a>Utdata

```console
Document ID: 0
    Extracted Key Phrases:
        幸せ

Document ID: 1
    Extracted Key Phrases:
        Stuttgart
        Hotel
        Fahrt
        Fu

Document ID: 2
    Extracted Key Phrases:
        cat
        veterinarian

Document ID: 3
    Extracted Key Phrases:
        fútbol
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Textanalys med Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Se också

 [Översikt över Textanalys](../overview.md) [Vanliga frågor och svar](../text-analytics-resource-faq.md)
