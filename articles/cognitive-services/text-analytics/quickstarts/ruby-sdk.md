---
title: 'Snabbstart: Anropa tjänsten Textanalys kognitiv med hjälp av ruby SDK'
titleSuffix: Azure Cognitive Services
description: Få information och kod exempel som hjälper dig att snabbt komma igång med API för textanalys i Azure Cognitive Services.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: tasharm
ms.openlocfilehash: 3f18b77fe436328e79df351b9c5edcf6dc289ad7
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697275"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-ruby-sdk"></a>Snabbstart: Anropa tjänsten Textanalys med hjälp av ruby SDK

<a name="HOLTop"></a>


Använd den här snabb starten för att börja analysera språk med Textanalys SDK för ruby. Även om [Textanalys](//go.microsoft.com/fwlink/?LinkID=759711) REST API är kompatibel med de flesta programmeringsspråk, är SDK ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-ruby-sdk-samples/blob/master/samples/text_analytics.rb).

Se [API-definitionerna](//go.microsoft.com/fwlink/?LinkID=759346) för teknisk dokumentation för API:erna.

## <a name="prerequisites"></a>Förutsättningar

* [Ruby 2.5.5 eller senare](https://www.ruby-lang.org/)
* Text Analytics [SDK för ruby](https://rubygems.org/gems/azure_cognitiveservices_textanalytics)
 
[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="RubyProject"></a>

## <a name="create-a-ruby-project-and-install-the-sdk"></a>Skapa ett ruby-projekt och installera SDK: n

1. Skapa ett nytt ruby-projekt och Lägg till en ny `Gemfile`fil med namnet.
2. Lägg till Textanalys SDK till projektet genom att lägga till nedanstående kod i `Gemfile`.

    ```ruby
    source 'https://rubygems.org'
    gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
    ```

## <a name="create-a-text-analytics-client"></a>Skapa en text Analytics-klient

1. Skapa en ny fil med `TextAnalyticsExamples.rb` namnet i din favorit redigerare eller IDE. Importera Textanalys SDK.

2. Ett inloggnings objekt kommer att användas av Textanalys-klienten. Skapa den med `CognitiveServicesCredentials.new()` och skicka din prenumerations nyckel.

3. Skapa klienten med rätt Textanalys slut punkt.

    ```ruby
    require 'azure_cognitiveservices_textanalytics'
    
    include Azure::CognitiveServices::TextAnalytics::V2_1::Models
    
    credentials =
        MsRestAzure::CognitiveServicesCredentials.new("enter key here")
    # Replace 'westus' with the correct region for your Text Analytics subscription
    endpoint = String.new("https://westus.api.cognitive.microsoft.com/")
    
    textAnalyticsClient =
        Azure::TextAnalytics::Profiles::Latest::Client.new({
            credentials: credentials
        })
    textAnalyticsClient.endpoint = endpoint
    ```

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Sentimentanalys

Med hjälp av Textanalys SDK eller API kan du utföra sentiment analyser på en uppsättning text poster. I följande exempel visas de sentiment poängen för flera dokument.

1. Skapa en ny funktion `SentimentAnalysisExample()` som använder den text Analytics-klient som skapades ovan som en parameter.

2. Definiera en uppsättning `MultiLanguageInput` objekt som ska analyseras. Lägg till ett språk och en text för varje objekt. ID: t kan vara vilket värde som helst.

    ```ruby
    def SentimentAnalysisExample(client)
      # The documents to be analyzed. Add the language of the document. The ID can be any value.
      input_1 = MultiLanguageInput.new
      input_1.id = '1'
      input_1.language = 'en'
      input_1.text = 'I had the best day of my life.'
    
      input_2 = MultiLanguageInput.new
      input_2.id = '2'
      input_2.language = 'en'
      input_2.text = 'This was a waste of my time. The speaker put me to sleep.'
    
      input_3 = MultiLanguageInput.new
      input_3.id = '3'
      input_3.language = 'es'
      input_3.text = 'No tengo dinero ni nada que dar...'
    
      input_4 = MultiLanguageInput.new
      input_4.id = '4'
      input_4.language = 'it'
      input_4.text = "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
    ```

3. I samma funktion kombinerar du dokumenten till en lista. Lägg till den i `documents` fältet för ett `MultiLanguageBatchInput` objekt. 

4. Anropa klientens `sentiment()` funktion `MultiLanguageBatchInput` med objektet som en parameter för att skicka dokumenten. Skriv ut alla resultat som returneras.
    ```ruby
      input_documents =  MultiLanguageBatchInput.new
      input_documents.documents = [input_1, input_2, input_3, input_4]
    
      result = client.sentiment(
          multi_language_batch_input: input_documents
      )
      
      if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        puts '===== SENTIMENT ANALYSIS ====='
        result.documents.each do |document|
          puts "Document Id: #{document.id}: Sentiment Score: #{document.score}"
        end
      end
    end
    ```

5. Anropa funktionen `SentimentAnalysisExample()`.

    ```ruby
    SentimentAnalysisExample(textAnalyticsClient)
    ```

### <a name="output"></a>Output

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>Språkidentifiering

Textanalyss tjänsten kan identifiera språket i ett text dokument på ett stort antal språk och nationella inställningar. I följande exempel visas språket som flera dokument skrevs i.

1. Skapa en ny funktion som `DetectLanguageExample()` anropar den text Analytics-klient som har skapats ovan som en parameter. 

2. Definiera en uppsättning `LanguageInput` objekt som ska analyseras. Lägg till ett språk och en text för varje objekt. ID: t kan vara vilket värde som helst.

    ```ruby
    def DetectLanguageExample(client)
       # The documents to be analyzed.
       language_input_1 = LanguageInput.new
       language_input_1.id = '1'
       language_input_1.text = 'This is a document written in English.'
    
       language_input_2 = LanguageInput.new
       language_input_2.id = '2'
       language_input_2.text = 'Este es un document escrito en Español..'
    
       language_input_3 = LanguageInput.new
       language_input_3.id = '3'
       language_input_3.text = '这是一个用中文写的文件'
    ```

3. I samma funktion kombinerar du dokumenten till en lista. Lägg till den i `documents` fältet för ett `LanguageBatchInput` objekt. 

4. Anropa klientens `detect_language()` funktion `LanguageBatchInput` med objektet som en parameter för att skicka dokumenten. Skriv ut alla resultat som returneras.
    ```ruby
       input_documents = LanguageBatchInput.new
       input_documents.documents = [language_input_1, language_input_2, language_input_3]
    
    
       result = client.detect_language(
           language_batch_input: input_documents
       )
    
       if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
         puts '===== LANGUAGE DETECTION ====='
         result.documents.each do |document|
           puts "Document ID: #{document.id} , Language: #{document.detected_languages[0].name}"
         end
       else
         puts 'No results data..'
       end
     end
    ```

5. Anropa funktionen`DetectLanguageExample`

    ```ruby
    DetectLanguageExample(textAnalyticsClient)
    ```

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>Enhets igenkänning

Textanalyss tjänsten kan särskilja och extrahera olika entiteter (personer, platser och saker) i text dokument. I följande exempel visas de entiteter som finns i flera exempel dokument.

1. Skapa en ny funktion `Recognize_Entities()` som använder den text Analytics-klient som skapades ovan som en parameter.

2. Definiera en uppsättning `MultiLanguageInput` objekt som ska analyseras. Lägg till ett språk och en text för varje objekt. ID: t kan vara vilket värde som helst.

    ```ruby
      def RecognizeEntitiesExample(client)
        # The documents to be analyzed.
        input_1 = MultiLanguageInput.new
        input_1.id = '1'
        input_1.language = 'en'
        input_1.text = 'Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.'
    
        input_2 = MultiLanguageInput.new
        input_2.id = '2'
        input_2.language = 'es'
        input_2.text = 'La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.'
    ```

3. I samma funktion kombinerar du dokumenten till en lista. Lägg till den i `documents` fältet för ett `MultiLanguageBatchInput` objekt. 

4. Anropa klientens `entities()` funktion `MultiLanguageBatchInput` med objektet som en parameter för att skicka dokumenten. Skriv ut alla resultat som returneras.

    ```ruby
        input_documents =  MultiLanguageBatchInput.new
        input_documents.documents = [input_1, input_2]
    
        result = client.entities(
            multi_language_batch_input: input_documents
        )
    
        if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
          puts '===== ENTITY RECOGNITION ====='
          result.documents.each do |document|
            puts "Document ID: #{document.id}"
              document.entities.each do |entity|
                puts "\tName: #{entity.name}, \tType: #{entity.type == nil ? "N/A": entity.type},\tSub-Type: #{entity.sub_type == nil ? "N/A": entity.sub_type}"
                entity.matches.each do |match|
                  puts "\tOffset: #{match.offset}, \Length: #{match.length},\tScore: #{match.entity_type_score}"
                end
                puts
              end
          end
        else
          puts 'No results data..'
        end
      end
    ```

5. Anropa funktionen`RecognizeEntitiesExample`
    ```ruby
    RecognizeEntitiesExample(textAnalyticsClient)
    ```

### <a name="output"></a>Output

```console
===== ENTITY RECOGNITION =====
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

Textanalyss tjänsten kan extrahera nyckel fraser i meningar. I följande exempel visas de entiteter som finns i flera exempel dokument på flera språk.

1. Skapa en ny funktion `KeyPhraseExtractionExample()` som använder den text Analytics-klient som skapades ovan som en parameter.

2. Definiera en uppsättning `MultiLanguageInput` objekt som ska analyseras. Lägg till ett språk och en text för varje objekt. ID: t kan vara vilket värde som helst.

    ```ruby
    def KeyPhraseExtractionExample(client)
      # The documents to be analyzed.
      input_1 = MultiLanguageInput.new
      input_1.id = '1'
      input_1.language = 'ja'
      input_1.text = '猫は幸せ'
  
      input_2 = MultiLanguageInput.new
      input_2.id = '2'
      input_2.language = 'de'
      input_2.text = 'Fahrt nach Stuttgart und dann zum Hotel zu Fu.'
  
      input_3 = MultiLanguageInput.new
      input_3.id = '3'
      input_3.language = 'en'
      input_3.text = 'My cat is stiff as a rock.'
  
      input_4 = MultiLanguageInput.new
      input_4.id = '4'
      input_4.language = 'es'
      input_4.text = 'A mi me encanta el fútbol!'
      ```

3. I samma funktion kombinerar du dokumenten till en lista. Lägg till den i `documents` fältet för ett `MultiLanguageBatchInput` objekt. 

4. Anropa klientens `key_phrases()` funktion `MultiLanguageBatchInput` med objektet som en parameter för att skicka dokumenten. Skriv ut alla resultat som returneras.

    ```ruby
      input_documents =  MultiLanguageBatchInput.new
      input_documents.documents = [input_1, input_2, input_3, input_4]
    
      result = client.key_phrases(
          multi_language_batch_input: input_documents
      )
    
      if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        result.documents.each do |document|
          puts "Document Id: #{document.id}"
          puts '  Key Phrases'
          document.key_phrases.each do |key_phrase|
            puts "    #{key_phrase}"
          end
        end
      else
        puts 'No results data..'
      end
    end
    ```

5. Anropa funktionen`KeyPhraseExtractionExample`

    ```ruby
    KeyPhraseExtractionExample(textAnalyticsClient)
    ```

### <a name="output"></a>Output

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Textanalys med Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Se också

 [Översikt över Textanalys](../overview.md)  
 [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)
