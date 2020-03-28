---
title: 'Snabbstart: Text Analytics v3-klientbibliotek för Java | Microsoft-dokument'
description: Kom igång med v3 Text Analytics-klientbiblioteket för Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: a0e6b5b7d5cedc821ee34bdd219ae07bb9d43199
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "79481916"
---
<a name="HOLTop"></a>

[Referensdokumentation](https://aka.ms/azsdk-java-textanalytics-ref-docs) | [Bibliotekskod](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [Paketprover](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics) [Package](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.3) | 

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) med version 8 eller högre
* När du har din <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure-prenumeration"  target="_blank">skapar Skapa en <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Text Analytics-resurs en Text Analytics-resurs i Azure-portalen för att hämta din nyckel och slutpunkt.  När den har distribuerats klickar du på **Gå till resurs**.
    * Du behöver nyckeln och slutpunkten från den resurs du skapar för att ansluta ditt program till Text Analytics API. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
    * Du kan använda den`F0`kostnadsfria prisnivån ( ) för att prova tjänsten och uppgradera senare till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

### <a name="add-the-client-library"></a>Lägga till klientbiblioteket

Skapa ett Maven-projekt i önskad IDE- eller utvecklingsmiljö. Lägg sedan till följande beroende i projektets *pom.xml-fil.* Du hittar implementeringssyntaxen [för andra byggverktyg](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.3) online.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.3</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Vill du visa hela snabbstartskodfilen på en gång? Du hittar den [på GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java), som innehåller kodexemplen i den här snabbstarten. 

Skapa en Java-fil med namnet `TextAnalyticsSamples.java`. Öppna filen och lägg `import` till följande satser:

```java
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

Lägg till en ny klass i java-filen och lägg till din azure-resursnyckel och slutpunkt enligt nedan.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Lägg till följande huvudmetod i klassen. Du kommer att definiera de metoder som kallas här senare.

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizePIIEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

## <a name="object-model"></a>Objektmodell

Text Analytics-klienten `TextAnalyticsClient` är ett objekt som autentiserar till Azure med hjälp av nyckeln och tillhandahåller funktioner för att acceptera text som enstaka strängar eller som en batch. Du kan skicka text till API-programmet synkront eller asynkront. Svarsobjektet innehåller analysinformationen för varje dokument som du skickar. 

## <a name="code-examples"></a>Kodexempel

* [Autentisera klienten](#authenticate-the-client)
* [Attitydanalys](#sentiment-analysis) 
* [Språkidentifiering](#language-detection)
* [Namngiven entitetsigenkänning](#named-entity-recognition-ner) 
* [Entitetslänkning](#entity-linking)
* [Extraktion av nyckelfraser](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa en metod för `TextAnalyticsClient` att instansiera objektet med nyckeln och slutpunkten för din Text Analytics-resurs.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .apiKey(new TextAnalyticsApiKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

Anropa autentiseringsmetoden i programmets `main()` metod för att instansiera klienten.

## <a name="sentiment-analysis"></a>Sentimentanalys

Skapa en ny `sentimentAnalysisExample()` funktion som anropas som tar `analyzeSentiment()` klienten som du skapade tidigare och anropa dess funktion. `AnalyzeSentimentResult` Det returnerade objektet `documentSentiment` `sentenceSentiments` kommer att innehålla `errorMessage` och om det lyckas, eller en om inte. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
    }
}
```

### <a name="output"></a>Resultat

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

## <a name="language-detection"></a>Språkidentifiering

Skapa en ny `detectLanguageExample()` funktion som anropas som tar `detectLanguage()` klienten som du skapade tidigare och anropa dess funktion. Det returnerade `DetectLanguageResult` objektet kommer att innehålla ett primärt språk som `errorMessage` har upptäckts, en lista över andra språk som har identifierats om det lyckas eller ett om inte.

> [!Tip]
> I vissa fall kan det vara svårt att disambiguate språk baserat på indata. Du kan `countryHint` använda parametern för att ange en landskod med två bokstäver. Som standard api använder "USA" som standardlandInta, för att ta bort detta beteende kan `countryHint = ""`du återställa den här parametern genom att ställa in det här värdet till tom sträng . Om du vill ange `TextAnalyticsClientOptions.DefaultCountryHint` en annan standard anger du egenskapen och skickar den under klientens initiering.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getScore());
}
```

### <a name="output"></a>Resultat

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```
## <a name="named-entity-recognition-ner"></a>Namngiven entitetsigenkänning (NER)

> [!NOTE]
> I `3.0-preview`version :
> * NER innehåller separata metoder för att upptäcka personlig information. 
> * Entitetslänkning är en separat begäran än NER.

Skapa en ny `recognizeEntitiesExample()` funktion som anropas som tar `recognizeEntities()` klienten som du skapade tidigare och anropa dess funktion. Det returnerade `RecognizeEntitiesResult` objektet innehåller `NamedEntity` en lista över `errorMessage` om det lyckas, eller en om inte.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Resultat

```console
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.92.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

## <a name="using-ner-to-recognize-personal-information"></a>Använda NER för att känna igen personlig information

Skapa en ny `recognizePIIEntitiesExample()` funktion som anropas som tar `recognizePiiEntities()` klienten som du skapade tidigare och anropa dess funktion. Det returnerade `RecognizePiiEntitiesResult` objektet innehåller `NamedEntity` en lista över `errorMessage` om det lyckas, eller en om inte. 

```java
static void recognizePIIEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Insurance policy for SSN on file 123-12-1234 is here by approved.";

    for (PiiEntity entity : client.recognizePiiEntities(text)) {
        System.out.printf(
            "Recognized personal identifiable information entity: %s, entity category: %s, %nentity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Resultat

```console
Recognized personal identifiable information entity: 123-12-1234, entity category: U.S. Social Security Number (SSN), 
entity sub-category: null, score: 0.85.
```

## <a name="entity-linking"></a>Entitetslänkning

Skapa en ny `recognizeLinkedEntitiesExample()` funktion som anropas som tar `recognizeLinkedEntities()` klienten som du skapade tidigare och anropa dess funktion. Det returnerade `RecognizeLinkedEntitiesResult` objektet innehåller `LinkedEntity` en lista över `errorMessage` om det lyckas, eller en om inte. Eftersom länkade entiteter identifieras unikt grupperas förekomster `LinkedEntity` av samma `LinkedEntityMatch` entitet under ett objekt som en lista över objekt.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
            "to develop and sell BASIC interpreters for the Altair 8800. " +
            "During his career at Microsoft, Gates held the positions of chairman, " +
            "chief executive officer, president and chief software architect, " +
            "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
                linkedEntity.getName(),
                linkedEntity.getDataSourceEntityId(),
                linkedEntity.getUrl(),
                linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getLinkedEntityMatches()) {
            System.out.printf("Text: %s, Score: %.2f%n",
                    linkedEntityMatch.getText(),
                    linkedEntityMatch.getConfidenceScore());
        }
    }
}
```

### <a name="output"></a>Resultat

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.78
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.55
Text: Gates, Score: 0.55
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.53
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.47
Text: Microsoft, Score: 0.47
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.25
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.28
```
## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

Skapa en ny `extractKeyPhrasesExample()` funktion som anropas som tar `extractKeyPhrases()` klienten som du skapade tidigare och anropa dess funktion. Det returnerade `ExtractKeyPhraseResult` objektet innehåller en lista med nyckelfraser om det lyckas, eller en `errorMessage` om inte.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>Resultat

```console
Recognized phrases: 
cat
veterinarian
```
