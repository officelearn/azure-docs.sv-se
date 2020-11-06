---
title: Cognitive Services för Big data-python-exempel
description: Försök Cognitive Services exempel i python för Azure Databricks för att köra din MMLSpark-pipeline för Big data.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 1a4fe2492433aa793d1a7e4be41c5f93043848a5
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337873"
---
# <a name="python-samples-for-cognitive-services-for-big-data"></a>Python-exempel för Cognitive Services för Big data

Följande kodfragment är klara att köras och hjälper dig att komma igång med att använda Cognitive Services i Spark med python.

I exemplen i den här artikeln används följande Cognitive Services:

- Textanalys – Hämta sentiment (eller stämningen) för en uppsättning meningar.
- Visuellt innehåll – Hämta taggarna (beskrivningar med ett ord) som är associerade med en uppsättning bilder.
- Bildsökning i Bing – Sök på webben efter bilder som är relaterade till en fråga med naturligt språk.
- Tal-till-text – att skriva av ljudfiler för att extrahera textbaserade avskrifter.
- Avvikelse detektor – identifiera avvikelser inom en tids serie data.

## <a name="prerequisites"></a>Förutsättningar

1. Följ stegen i [komma igång](getting-started.md) för att konfigurera din Azure Databricks och Cognitive Servicess miljö. Den här självstudien visar hur du installerar MMLSpark och hur du skapar ett Spark-kluster i Databricks.
1. När du har skapat en ny antecknings bok i Azure Databricks kopierar du den **delade koden** nedan och klistrar in den i en ny cell i antecknings boken.
1. Välj ett tjänst exempel nedan och kopiera klistra in det i en andra ny cell i din bärbara dator.
1. Ersätt någon av plats hållarna för tjänste prenumerations nyckeln med din egen nyckel.
1. Klicka på knappen Kör (triangel-ikonen) i cellens övre högra hörn och välj sedan **Kör cell**.
1. Visa resultatet i en tabell under cellen.

## <a name="shared-code"></a>Delad kod

För att komma igång måste vi lägga till den här koden i projektet:

```python
from mmlspark.cognitive import *

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key = "ADD_YOUR_SUBSCRIPION_KEY"
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY"
# An Anomaly Dectector subscription key
anomaly_key = "ADD_YOUR_SUBSCRIPION_KEY"

# Validate the key
assert service_key != "ADD_YOUR_SUBSCRIPION_KEY"
```    

## <a name="text-analytics-sample"></a>Textanalys exempel

Tjänsten [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) tillhandahåller flera algoritmer för att extrahera intelligenta insikter från text. Vi kan till exempel hitta sentiment för den angivna texten. Tjänsten returnerar ett resultat mellan 0,0 och 1,0 där låga poäng indikerar negativa sentiment och höga poäng indikerar positiv sentiment.  Det här exemplet använder tre enkla meningar och returnerar sentiment för var och en.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format
display(sentiment.transform(df).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-result"></a>Förväntat resultat

| text                                      | sentiment                                             |
|:------------------------------------------|:------------------------------------------------------|
| Jag är så glad idag, dess solig!           | positivt                                              |
| Jag är frustrerad genom den här trafik som skynda på trafik | negativt                                              |
| Kognitiva tjänster på Spark aint-dåliga  | positivt                                              |

## <a name="computer-vision-sample"></a>Visuellt innehåll exempel

[Visuellt innehåll](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) analyserar bilder för att identifiera strukturen som ansikten, objekt och beskrivningar av naturligt språk. I det här exemplet Taggar vi en lista med bilder. Taggar är en beskrivning av saker i bilden, till exempel igenkännliga objekt, människor, landskap och åtgärder.

```python

# Create a dataframe with the image URLs
df = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select("image", "analysis_results.description.tags"))
```

### <a name="expected-result"></a>Förväntat resultat

| image | tags |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | [' flytta ' ' man ' ' man ' ' s ' ', "' Sport ' ' ' skateboard ' ' Nilsson ' ' skjorta ' ' shirt ' ' Black ' ' Park ' ' ' s ' ' s ' ' s ' ' s ' ' för ' ' för ' ' för '
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | [' hund ' ' utomhus ' ' stängsel ' ' liten ' ' brun ' ', "framför" "bänken" står i tabell ' "strand" "bad" "," i "Tabell", "" bad "", "-", "-" spår "]                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | [' gräs ' ' ' hus ' ' ' hus ' ' ' Home ' ' ' Home ' ' ' Small ' ' kyrkslaviska ' ' sten ' ' stor ' ' hög ' ' varv ' ' i grönt ' ' ledande ' ' Get ' ' bricka ' ' bänk ' ' gata ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' för ' ' ' ' ' ' ' ' ' ' '


## <a name="bing-image-search-sample"></a>Bildsökning i Bing exempel

[Bildsökning i Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview) söker på webben för att hämta avbildningar relaterade till en användares naturliga språk fråga. I det här exemplet använder vi en text fråga som söker efter bilder med citat tecken. Den returnerar en lista med bild-URL: er som innehåller foton som är relaterade till vår fråga.

```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 10
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(100)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bing_search_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")

# This displays the full results returned, uncomment to use
# display(bingSearch.transform(bingParameters))

# Since we have two services, they are put into a pipeline
pipeline = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
display(pipeline.transform(bingParameters))
```

### <a name="expected-result"></a>Förväntat resultat

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |


## <a name="speech-to-text-sample"></a>Tal till text-exempel
Tjänsten [tal-till-text](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-speech-to-text) konverterar data strömmar eller filer av talade ljud till text. I det här exemplet ska vi skriva två ljudfiler. Den första filen är lätt att förstå och den andra är mer utmanande.

```python

# Create a dataframe with our audio URLs, tied to the column called "url"
df = spark.createDataFrame([("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav",),
                           ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3",)
                           ], ["url"])

# Run the Speech-to-text service to translate the audio into text
speech_to_text = (SpeechToTextSDK()
    .setSubscriptionKey(service_key)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked"))

# Show the results of the translation
display(speech_to_text.transform(df).select("url", "text.DisplayText"))
```

### <a name="expected-result"></a>Förväntat resultat

| url | Visnings |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | Anpassad tal innehåller verktyg som gör det möjligt att visuellt inspektera igenkännings kvaliteten för en modell genom att jämföra ljuddata med motsvarande igenkännings resultat från den anpassade tal portalen. Du kan spela upp överfört ljud och avgöra om det angivna igenkännings resultatet är korrekt. Med det här verktyget kan du snabbt kontrol lera kvaliteten på Microsofts baseline-tal till text modellen eller en tränad anpassad modell utan att behöva skriva om ljud data. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Lägg till en gentleman Sir tro-kontroll av visuella objekt.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Jag hör mig. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Jag gillar att se till att det är möjligt för radio som jag kan höra och. |


## <a name="anomaly-detector-sample"></a>Exempel på avvikelse detektor

[Avvikelse detektor](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/) är perfekt för att upptäcka överträdelser i dina tids serie data. I det här exemplet använder vi tjänsten för att hitta avvikelser i hela tids serien.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df = spark.createDataFrame([
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
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomaly_key)
  .setLocation("eastus")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
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

## <a name="arbitrary-web-apis"></a>Godtyckliga webb-API: er

Med HTTP i Spark kan alla webb tjänster användas i din Big data-pipeline. I det här exemplet använder vi [World Bank-API: et](http://api.worldbank.org/v2/country/) för att få information om olika länder runtom i världen.

```python
from requests import Request
from mmlspark.io.http import HTTPTransformer, http_udf
from pyspark.sql.functions import udf, col

# Use any requests from the python requests library
def world_bank_request(country):
  return Request("GET", "http://api.worldbank.org/v2/country/{}?format=json".format(country))

# Create a dataframe with spcificies which countries we want data on
df = (spark.createDataFrame([("br",),("usa",)], ["country"])
  .withColumn("request", http_udf(world_bank_request)(col("country"))))

# Much faster for big data because of the concurrency :)
client = (HTTPTransformer()
      .setConcurrency(3)
      .setInputCol("request")
      .setOutputCol("response"))

# Get the body of the response
def get_response_body(resp):
  return resp.entity.content.decode()

# Show the details of the country data returned
display(client.transform(df).select("country", udf(get_response_body)(col("response")).alias("response")))
```

### <a name="expected-result"></a>Förväntat resultat

| land   | svar |
|:----------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| br | [{"sida": 1, "sidor": 1, "per_page": "50", "totalt": 1}, [{"ID": "BRA", "iso2Code": "BR", "namn": "Brasilien", "region": {"ID": "LCN", "iso2Code": "ZJ", "värde": "Latinamerika & Karibien"}, "adminregion": {"ID": "LAC", "iso2Code": "XJ", "value": "Latinamerika & Karibien (exklusive hög inkomst)"}, "incomeLevel": {"ID": "UMC", "iso2Code": "XT", "värde": "övre mittersta inkomst"}, "lendingType": {"ID": "IBD", "iso2Code": "XF", "value": "IBRD"}, "capitalCity": "Brasilien", "longitud": "-47.9292", "latitud": "-15,7801"}]] |
| Förenta  | [{"sida": 1, "sidor": 1, "per_page": "50", "totalt": 1}, [{"ID": "USA", "iso2Code": "US", "namn": "USA", "region": {"ID": "NAC", "iso2Code": "XU", "värde": "Nordamerika"}, "adminregion": {"ID": "", "iso2Code": "", "värde": ""}, "incomeLevel": {"ID": "HIC", "iso2Code": "XD", "värde": "hög inkomst"}, "lendingType": {"ID": "LNX", "iso2Code": "XX", "value": "inte klassificerad"}, "capitalCity": "Washington D.C.", "longitud": "-77.032", "latitud": "38.8895"}]] |

## <a name="see-also"></a>Se även

* [Recept: avvikelse identifiering](./recipes/anomaly-detection.md)
* [Recept: Art Explorer](./recipes/art-explorer.md)
