---
title: 'Snabb start: Textanalys v3-klient bibliotek för Java | Microsoft Docs'
description: Kom igång med v3-Textanalys klient biblioteket för Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/26/2020
ms.author: aahi
ms.reviewer: tasharm, assafi
ms.openlocfilehash: 79b4063d6b65d6861dd7864c4225e91f4ea5bc6d
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "78155426"
---
<a name="HOLTop"></a>

[Referens dokumentation](https://aka.ms/azsdk-java-textanalytics-ref-docs) | [Library Source Code](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [Package (maven) | -](https://oss.sonatype.org/#nexus-search;quick~com.azure) [exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) med version 8 eller senare
* När du har en Azure-prenumeration kan <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="skapa en Textanalys resurs"  target="_blank">skapa en Textanalys <span class="docon docon-navigate-external x-hidden-focus"></span> resurs</a> i Azure Portal för att hämta din nyckel och slut punkt. 
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till API för textanalys. Du kommer att göra detta senare i snabb starten.
    * Du kan använda den kostnads fria pris nivån för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-maven-project"></a>Skapa ett nytt Maven-projekt

Lägg till följande text analys beroende av ditt projekt. Den här versionen av beroendet använder version `3.0-preview` av API för textanalys. 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>
</dependencies>
```

Skapa en ny Java-fil i följande katalog: `\src\main\java`.

Öppna Java-filen och Lägg till följande `import`-satser:

```java
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

I Java-filen lägger du till en ny klass och lägger till Azure-resursens nyckel och slut punkt enligt nedan.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Lägg till följande huvud metod i-klassen. Du definierar de metoder som anropas här senare.

```java
public static void main(String[] args) {

    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizePIIEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

## <a name="object-model"></a>Objekt modell

Textanalys-klienten är ett `TextAnalyticsClient`-objekt som autentiserar till Azure med hjälp av din nyckel och ger funktioner för att acceptera text som enkla strängar eller som en batch. Du kan skicka text till API: et synkront eller asynkront. Objektet Response kommer att innehålla analys informationen för varje dokument som du skickar. 

## <a name="code-examples"></a>Kod exempel

* [Autentisera klienten](#authenticate-the-client)
* [Attitydanalys](#sentiment-analysis) 
* [Språk identifiering](#language-detection)
* [Igenkänning av namngiven entitet](#named-entity-recognition-ner) 
* [Länkning av entitet](#entity-linking)
* [Extrahering av nyckel fraser](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa en metod för att instansiera `TextAnalyticsClient`-objektet med `KEY` och `ENDPOINT` som skapats ovan.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .apiKey(new TextAnalyticsApiKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

Anropa autentiseringsmetoden i programmets `main()` metod för att instansiera klienten.

## <a name="sentiment-analysis"></a>Attitydanalys

Skapa en ny funktion som kallas `sentimentAnalysisExample()` som tar klienten som du skapade tidigare och anropar dess `analyzeSentiment()`-funktion. Det returnerade `AnalyzeSentimentResult`-objektet kommer att innehålla `documentSentiment` och `sentenceSentiments` om det lyckas, eller en `errorMessage` om inte. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
        System.out.printf(
            "Recognized document sentiment: %s, positive score: %.2f, neutral score: %.2f, negative score: %.2f.%n",
            documentSentiment.getSentiment(),
            documentSentiment.getSentimentScores().getPositive(),
            documentSentiment.getSentimentScores().getNeutral(),
            documentSentiment.getSentimentScores().getNegative());

        for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
            System.out.printf(
                "Recognized sentence sentiment: %s, positive score: %.2f, neutral score: %.2f, negative score: %.2f.%n",
                sentenceSentiment.getSentiment(),
                sentenceSentiment.getSentimentScores().getPositive(),
                sentenceSentiment.getSentimentScores().getNeutral(),
                sentenceSentiment.getSentimentScores().getNegative());
        }
}
```

### <a name="output"></a>Resultat

```console
Recognized document sentiment: positive, Positive Score: 1.00, Neutral Score: 0.00, Negative Score: 0.00.
Recognized sentence sentiment: positive, positive score: 1.00, neutral score: 0.00, negative score: 0.00.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```
## <a name="language-detection"></a>Språkidentifiering

Skapa en ny funktion som kallas `detectLanguageExample()` som tar klienten som du skapade tidigare och anropar dess `detectLanguage()`-funktion. Det returnerade `DetectLanguageResult`-objektet kommer att innehålla ett primärt språk identifierat, en lista över andra språk som har identifierats om det lyckas eller en `errorMessage` om det inte.

> [!Tip]
> I vissa fall kan det vara svårt att disambiguate språk baserat på indatamängden. Du kan använda parametern `countryHint` för att ange en landskod på 2 bokstäver. Som standard använder API: t "US" som standard-countryHint, för att ta bort det här alternativet kan du återställa den här parametern genom att ange det här värdet till tom sträng `countryHint = ""`. Ange ett annat standardvärde genom att ange egenskapen `TextAnalyticsClientOptions.DefaultCountryHint` och skicka den under klient initieringen.

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
## <a name="named-entity-recognition-ner"></a>Igenkänning av namngivna enheter (NER)

> [!NOTE]
> I version `3.0-preview`:
> * NER innehåller separata metoder för att identifiera personlig information. 
> * Enhets länkning är en separat begäran än NER.

Skapa en ny funktion som kallas `recognizeEntitiesExample()` som tar klienten som du skapade tidigare och anropar dess `recognizeEntities()`-funktion. Det returnerade `RecognizeEntitiesResult`-objektet kommer att innehålla en lista över `NamedEntity` om det lyckas eller om en `errorMessage` om inte.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, offset: %s, length: %s, score: %.2f.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory() == null || entity.getSubCategory().isEmpty() ? "N/A" : entity.getSubCategory(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>Resultat

```console
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, offset: 26, length: 7, score: 0.92.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, offset: 34, length: 9, score: 0.80.
```

## <a name="using-ner-to-recognize-personal-information"></a>Använda NER för att identifiera personlig information

Skapa en ny funktion som kallas `recognizePIIEntitiesExample()` som tar klienten som du skapade tidigare och anropar dess `recognizePiiEntities()`-funktion. Det returnerade `RecognizePiiEntitiesResult`-objektet kommer att innehålla en lista över `NamedEntity` om det lyckas eller om en `errorMessage` om inte. 

```java
static void recognizePIIEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Insurance policy for SSN on file 123-12-1234 is here by approved.";

    for (PiiEntity entity : client.recognizePiiEntities(text)) {
        System.out.printf(
            "Recognized personal identifiable information entity: %s, entity category: %s, entity sub-category: %s, offset: %s, length: %s, score: %.2f.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory() == null || entity.getSubCategory().isEmpty() ? "N/A" : entity.getSubCategory(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>Resultat

```console
Recognized personal identifiable information entity: 123-12-1234, entity category: U.S. Social Security Number (SSN), entity sub-category: N/A, offset: 33, length: 11, score: 0.85.
```

## <a name="entity-linking"></a>Länkning av entitet

Skapa en ny funktion som kallas `recognizeLinkedEntitiesExample()` som tar klienten som du skapade tidigare och anropar dess `recognizeLinkedEntities()`-funktion. Det returnerade `RecognizeLinkedEntitiesResult`-objektet kommer att innehålla en lista över `LinkedEntity` om det lyckas eller om en `errorMessage` om inte. Eftersom länkade entiteter identifieras unikt, grupperas förekomster av samma entitet under ett `LinkedEntity` objekt som en lista över `LinkedEntityMatch` objekt.

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
                linkedEntity.getId(),
                linkedEntity.getUrl(),
                linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getLinkedEntityMatches()) {
            System.out.printf("Text: %s, Offset: %s, Length: %s, Score: %.2f.%n",
                    linkedEntityMatch.getText(),
                    linkedEntityMatch.getOffset(),
                    linkedEntityMatch.getLength(),
                    linkedEntityMatch.getScore());
        }
    }
}
```

### <a name="output"></a>Resultat

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Offset: 11, Length: 116, Score: 0.78.
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Offset: 10, Length: 25, Score: 0.55.
Text: Gates, Offset: 5, Length: 161, Score: 0.55.
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Offset: 10, Length: 40, Score: 0.53.
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Offset: 9, Length: 0, Score: 0.47.
Text: Microsoft, Offset: 9, Length: 150, Score: 0.47.
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Offset: 7, Length: 54, Score: 0.25.
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Offset: 5, Length: 89, Score: 0.28.
```
## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

Skapa en ny funktion som kallas `extractKeyPhrasesExample()` som tar klienten som du skapade tidigare och anropar dess `extractKeyPhrases()`-funktion. Det returnerade `ExtractKeyPhraseResult`-objektet innehåller en lista över viktiga fraser om det lyckas eller om en `errorMessage` inte.

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
