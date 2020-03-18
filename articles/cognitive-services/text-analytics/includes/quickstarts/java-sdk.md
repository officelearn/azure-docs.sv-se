---
title: 'Snabbstart: Textanalys v3-klientbiblioteket för Java | Microsoft Docs'
description: Kom igång med klientbiblioteket för Textanalys v3 för Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: 11092b74c0256d298dece0f909d8d7dd241e7b13
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371347"
---
<a name="HOLTop"></a>

[Referensdokumentation](https://aka.ms/azsdk-java-textanalytics-ref-docs) | [Källkod för biblioteket](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [Paket](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.3) | [Exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [Skapa en kostnadsfritt](https://azure.microsoft.com/free/)
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) med version 8 eller senare
* När du har en Azure-prenumeration kan du <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Skapa en resurs för Textanalys"  target="_blank">skapa en resurs för Textanalys <span class="docon docon-navigate-external x-hidden-focus"></span></a> i Microsoft Azure-portalen för att hämta din nyckel och slutpunkt. 
    * Du behöver nyckeln och slutpunkten från resursen som du skapade för att ansluta ditt program till API för textanalys. Du kommer att göra detta senare i snabbstarten.
    * Du kan använda den kostnadsfria prisnivån för att testa tjänsten och uppgradera till en betald nivå för produktion senare.

## <a name="setting-up"></a>Konfigurera

### <a name="add-the-client-library"></a>Lägga till klientbiblioteket

Skapa ett Maven-projekt i önskad IDE eller utvecklingsmiljö. Lägg sedan till följande beroende i projektets *pom.xml*-fil. Implementeringssyntaxen [för andra byggverktyg](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.3) finns online.

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

Skapa en Java-fil som heter `TextAnalyticsSamples.java`. Öppna filen och lägg till följande `import`-instruktioner:

```java
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

I Java-filen lägger du till en ny klass och lägger till Azure-resursens nyckel och slutpunkt enligt exemplet nedan.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Lägg till följande main-metod i klassen. Du definierar de anropade metoderna här senare.

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

Textanalys-klienten är ett `TextAnalyticsClient`-objekt som autentiserar till Azure med hjälp av din nyckel och ger funktioner för att acceptera text som enkla strängar eller som en batch. Du kan skicka text till API:et synkront eller asynkront. Svarsobjektet kommer att innehålla analysinformationen om varje dokument du skickar. 

## <a name="code-examples"></a>Kodexempel

* [Autentisera klienten](#authenticate-the-client)
* [Attitydanalys](#sentiment-analysis) 
* [Språkidentifiering](#language-detection)
* [Igenkänning av namngiven enhet](#named-entity-recognition-ner) 
* [Entitetslänkning](#entity-linking)
* [Extrahering av nyckelfraser](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa en metod för att instansiera `TextAnalyticsClient`-objektet med nyckeln och slutpunkten för din resurs för Textanalys.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .apiKey(new TextAnalyticsApiKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

I programmet `main()`-metod anropar du autentiseringsmetoden för att instansiera klienten.

## <a name="sentiment-analysis"></a>Sentimentanalys

Skapa en ny funktion med namnet `sentimentAnalysisExample()` som tar klienten du har skapat tidigare och anropar dess `analyzeSentiment()`-funktion. Det returnerade `AnalyzeSentimentResult`-objektet innehåller `documentSentiment` och `sentenceSentiments` om det lyckas och ett `errorMessage` om det misslyckas. 

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

Skapa en ny funktion med namnet `detectLanguageExample()` som tar klienten du har skapat tidigare och anropar dess `detectLanguage()`-funktion. Det returnerade `DetectLanguageResult`-objektet innehåller ett primärt språk som identifierats och en lista över andra språk som identifierats om det lyckas, eller ett `errorMessage` om det misslyckas.

> [!Tip]
> I vissa fall kan det vara svårt att förtydliga språk baserat på indata. Du kan använda parametern `countryHint` för att ange en landskod på 2 bokstäver. Som standard använder API:et ”US” som standardinställd countryHint. Om du vill ta bort detta beteende kan du återställa parametern genom att ställa in värdet på tom sträng, `countryHint = ""`. Om du ställa in ett annat standardvärde anger du egenskapen `TextAnalyticsClientOptions.DefaultCountryHint` och skickar den under klientens initiering.

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
## <a name="named-entity-recognition-ner"></a>Igenkänning av namngiven enhet (NER)

> [!NOTE]
> I version `3.0-preview`:
> * NER innehåller separata metoder för att identifiera personliga uppgifter. 
> * Entitetslänkning är en begäran som skiljer sig från NER.

Skapa en ny funktion med namnet `recognizeEntitiesExample()` som tar klienten du har skapat tidigare och anropar dess `recognizeEntities()`-funktion. Det returnerade `RecognizeEntitiesResult`-objektet innehåller en lista över `NamedEntity` om det lyckas och ett `errorMessage` om det misslyckas.

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

## <a name="using-ner-to-recognize-personal-information"></a>Använda NER för att identifiera personliga uppgifter

Skapa en ny funktion med namnet `recognizePIIEntitiesExample()` som tar klienten du har skapat tidigare och anropar dess `recognizePiiEntities()`-funktion. Det returnerade `RecognizePiiEntitiesResult`-objektet innehåller en lista över `NamedEntity` om det lyckas och ett `errorMessage` om det misslyckas. 

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

Skapa en ny funktion med namnet `recognizeLinkedEntitiesExample()` som tar klienten du har skapat tidigare och anropar dess `recognizeLinkedEntities()`-funktion. Det returnerade `RecognizeLinkedEntitiesResult`-objektet innehåller en lista över `LinkedEntity` om det lyckas och ett `errorMessage` om det misslyckas. Eftersom länkade entiteter identifieras unikt, grupperas förekomster av samma entitet under ett `LinkedEntity`-objekt som en lista över `LinkedEntityMatch`-objekt.

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

Skapa en ny funktion med namnet `extractKeyPhrasesExample()` som tar klienten du har skapat tidigare och anropar dess `extractKeyPhrases()`-funktion. Det returnerade `ExtractKeyPhraseResult`-objektet innehåller en lista över nyckelfraser om det lyckas och ett `errorMessage` om det misslyckas.

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
