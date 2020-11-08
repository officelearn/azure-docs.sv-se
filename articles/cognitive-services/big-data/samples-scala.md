---
title: Cognitive Services för Scala-exempel för Big data
description: Använd Cognitive Services för Azure Databricks för att köra din MMLSpark-pipeline för Big data.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: c47aa803774343b39efeabe3452f1b256cc64c0d
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363280"
---
# <a name="quick-examples"></a>Snabba exempel

Följande kodfragment är klara att köras och hjälper dig att komma igång med att använda Cognitive Services i Spark. Exemplen nedan finns i Scala.

Exemplen använder följande Cognitive Services:

- Textanalys – Hämta sentiment (eller stämningen) för en uppsättning meningar.
- Visuellt innehåll – Hämta taggarna (beskrivningar med ett ord) som är associerade med en uppsättning bilder.
- Bildsökning i Bing – Sök på webben efter bilder som är relaterade till en fråga med naturligt språk.
- Tal-till-text – att skriva av ljudfiler för att extrahera textbaserade avskrifter.
- Avvikelse detektor – identifiera avvikelser inom en tids serie data.

## <a name="prerequisites"></a>Förutsättningar

1. Följ stegen i [komma igång](getting-started.md) för att konfigurera din Azure Databricks och Cognitive Servicess miljö. I den här kursen får du veta hur du installerar MMLSpark och hur du skapar ett Spark-kluster i Databricks.
1. När du har skapat en ny antecknings bok i Azure Databricks kopierar du den **delade koden** nedan och klistrar in den i en ny cell i antecknings boken.
1. Välj ett tjänst exempel nedan och kopiera klistra in det i en andra ny cell i din bärbara dator.
1. Ersätt någon av plats hållarna för tjänste prenumerations nyckeln med din egen nyckel.
1. Klicka på knappen Kör (triangel-ikonen) i cellens övre högra hörn och välj sedan **Kör cell**.
1. Visa resultatet i en tabell under cellen.

## <a name="shared-code"></a>Delad kod
Kom igång genom att lägga till den här koden i projektet:

```scala
import com.microsoft.ml.spark.cognitive._
import spark.implicits._ 

val serviceKey = "ADD-YOUR-SUBSCRIPTION-KEY"
val location = "eastus"
```

## <a name="text-analytics"></a>Textanalys

Tjänsten [textanalys](../text-analytics/index.yml) tillhandahåller flera algoritmer för att extrahera intelligenta insikter från text. Vi kan till exempel hitta sentiment för den angivna texten. Tjänsten returnerar ett resultat mellan `0.0` och `1.0` där låga poäng indikerar negativa sentiment och höga poäng indikerar positiv sentiment.  Exemplet nedan använder tre enkla meningar och returnerar sentiment-poängen för var och en.

```scala
import org.apache.spark.sql.functions.col

val df = Seq(
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US")
).toDF("text", "language")

val sentiment = new TextSentiment()
    .setTextCol("text")
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language")

display(sentiment.transform(df).select(col("text"), col("sentiment")(0).getItem("score").alias("sentiment")))
```

### <a name="expected-result"></a>Förväntat resultat

| text                                      | sentiment                                             |
|:------------------------------------------|:------------------------------------------------------|
| Jag är så glad idag, dess solig!           | 0.9789592027664185                                    |
| Jag är frustrerad genom den här trafik som skynda på trafik | 0.023795604705810547                                  |
| Kognitiva tjänster på Spark aint-dåliga  | 0.8888956308364868                                    |

## <a name="computer-vision"></a>Visuellt innehåll

[Visuellt innehåll](../computer-vision/index.yml) analyserar bilder för att identifiera strukturen som ansikten, objekt och beskrivningar av naturligt språk.
I det här exemplet Taggar vi en lista med bilder. Taggar är en beskrivning av saker i bilden, till exempel igenkännliga objekt, människor, landskap och åtgärder.

```scala
// Create a dataframe with the image URLs
val df = Seq(
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg")
).toDF("image")

// Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
val analysis = new AnalyzeImage()
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setVisualFeatures(Seq("Categories","Color","Description","Faces","Objects","Tags"))
    .setOutputCol("results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

// Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select(col("image"), col("results").getItem("tags").getItem("name")).alias("results")))

// Uncomment for full results with all visual feature requests
//display(analysis.transform(df).select(col("image"), col("results")))
``` 

### <a name="expected-result"></a>Förväntat resultat

| image | tags |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | [' flytta ' ' man ' ' man ' ' s ' ', "' Sport ' ' ' skateboard ' ' Nilsson ' ' skjorta ' ' shirt ' ' Black ' ' Park ' ' ' s ' ' s ' ' s ' ' s ' ' för ' ' för ' ' för '
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | [' hund ' ' utomhus ' ' stängsel ' ' liten ' ' brun ' ', "framför" "bänken" står i tabell ' "strand" "bad" "," i "Tabell", "" bad "", "-", "-" spår "]                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | [' gräs ' ' ' hus ' ' ' hus ' ' ' Home ' ' ' Home ' ' ' Small ' ' kyrkslaviska ' ' sten ' ' stor ' ' hög ' ' varv ' ' i grönt ' ' ledande ' ' Get ' ' bricka ' ' bänk ' ' gata ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' för ' ' ' ' ' ' ' ' ' ' ' 

## <a name="bing-image-search"></a>Bildsökning i Bing

[Bildsökning i Bing](../bing-image-search/overview.md) söker på webben för att hämta avbildningar relaterade till en användares naturliga språk fråga. I det här exemplet använder vi en text fråga som söker efter bilder med citat tecken. Den returnerar en lista med bild-URL: er som innehåller foton som är relaterade till vår fråga.


```scala
import org.apache.spark.ml.Pipeline

// Number of images Bing will return per query
val imgsPerBatch = 10

// A list of offsets, used to page into the search results
val df = (0 until 100).map(o => Tuple1(o*imgsPerBatch)).toSeq.toDF("offset")

// Run the Bing Image Search service with our text query
val bingSearch = new BingImageSearch()
    .setSubscriptionKey(bingSearchKey)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images")

// Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
val getUrls = BingImageSearch.getUrlTransformer("images", "url")

// This displays the full results returned, uncomment to use
// display(bingSearch.transform(bingParameters))

// Since we have two services, they are put into a pipeline
val pipeline = new Pipeline().setStages(Array(bingSearch, getUrls))

// Show the results of your search: image URLs
display(pipeline.fit(df).transform(df))
```

### <a name="expected-result"></a>Förväntat resultat

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |

## <a name="speech-to-text"></a>Tal till text

Tjänsten [tal-till-text](../speech-service/index-speech-to-text.yml) konverterar data strömmar eller filer av talade ljud till text. I det här exemplet ska vi skriva två ljudfiler. Den första filen är lätt att förstå och den andra är mer utmanande.

```scala
import org.apache.spark.sql.functions.col

// Create a dataframe with audio URLs, tied to the column called "url"
val df = Seq(("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav"),
             ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3")).toDF("url")

// Run the Speech-to-text service to translate the audio into text
val speechToText = new SpeechToTextSDK()
    .setSubscriptionKey(serviceKey)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked")

// Show the results of the translation
display(speechToText.transform(df).select(col("url"), col("text").getItem("DisplayText")))
```

### <a name="expected-result"></a>Förväntat resultat

| url | Visnings |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | Anpassad tal innehåller verktyg som gör det möjligt att visuellt inspektera igenkännings kvaliteten för en modell genom att jämföra ljuddata med motsvarande igenkännings resultat från den anpassade tal portalen. Du kan spela upp överfört ljud och avgöra om det angivna igenkännings resultatet är korrekt. Med det här verktyget kan du snabbt kontrol lera kvaliteten på Microsofts baseline-tal till text modellen eller en tränad anpassad modell utan att behöva skriva om ljud data. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Lägg till en gentleman Sir tro-kontroll av visuella objekt.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Jag hör mig. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Jag gillar att se till att det är möjligt för radio som jag kan höra och. |

## <a name="anomaly-detector"></a>Avvikelseidentifiering

[Avvikelse detektor](../anomaly-detector/index.yml) är perfekt för att upptäcka överträdelser i dina tids serie data. I det här exemplet använder vi tjänsten för att hitta avvikelser i hela tids serien.

```scala
import org.apache.spark.sql.functions.{col, lit}

val anomalyKey = "84a2c303cc7e49f6a44d692c27fb9967"

val df = Seq(
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0),
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0)
  ).toDF("timestamp", "value").withColumn("group", lit("series1"))

// Run the Anomaly Detector service to look for irregular data
val anamolyDetector = new SimpleDetectAnomalies()
    .setSubscriptionKey(anomalyKey)
    .setLocation("eastus")
    .setTimestampCol("timestamp")
    .setValueCol("value")
    .setOutputCol("anomalies")
    .setGroupbyCol("group")
    .setGranularity("monthly")

// Show the full results of the analysis with the anomalies marked as "True"
display(anamolyDetector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
```

### <a name="expected-result"></a>Förväntat resultat

| timestamp            |   värde | isAnomaly   |
|:---------------------|--------:|:------------|
| 1972-01-01T00:00:00Z |     826 | Falskt       |
| 1972-02-01T00:00:00Z |     799 | Falskt       |
| 1972-03-01T00:00:00Z |     890 | Falskt       |
| 1972-04-01T00:00:00Z |     900 | Falskt       |
| 1972-05-01T00:00:00Z |     766 | Falskt       |
| 1972-06-01T00:00:00Z |     805 | Falskt       |
| 1972-07-01T00:00:00Z |     821 | Falskt       |
| 1972-08-01T00:00:00Z |   20000 | Sant        |
| 1972-09-01T00:00:00Z |     883 | Falskt       |
| 1972-10-01T00:00:00Z |     898 | Falskt       |
| 1972-11-01T00:00:00Z |     957 | Falskt       |
| 1972-12-01T00:00:00Z |     924 | Falskt       |
| 1973-01-01T00:00:00Z |     881 | Falskt       |
| 1973-02-01T00:00:00Z |     837 | Falskt       |
| 1973-03-01T00:00:00Z |    9000 | Sant        |