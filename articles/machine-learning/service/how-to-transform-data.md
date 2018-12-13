---
title: 'Transformeringar: Förbered data Python SDK'
titleSuffix: Azure Machine Learning service
description: Läs mer om omvandla och rensa data med Azure Machine Learning Data Prep SDK. Använda transformeringen metoder för att lägga till kolumner, filtrera bort oönskade rader eller kolumner och sedan imputera värden som saknas.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: d32244cd49ebd42192b2388215f79a64cacb3caa
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53186148"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>Transformera data med Azure Machine Learning Data Prep SDK

I den här artikeln får du lära dig olika metoder för att läsa in data med hjälp av den [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk). SDK: N erbjuder funktioner som gör det enkelt att lägga till kolumner, filtrera bort oönskade rader eller kolumner och sedan imputera värden som saknas.

Det finns för närvarande funktioner för följande uppgifter:

- [Lägg till kolumnen med ett uttryck](#column)
- [Sedan imputera värden som saknas](#impute-missing-values)
- [Härled kolumner med exempel](#derive-column-by-example)
- [Filtrering](#filtering)
- [Anpassad Python-transformeringar](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>Lägg till kolumnen med ett uttryck

Azure Machine Learning Data Prep SDK innehåller `substring` uttryck som du kan använda för att beräkna ett värde från befintliga kolumner och anger att värdet i en ny kolumn. I det här exemplet läser in data och försök att lägga till kolumner som indata.

```python
import azureml.dataprep as dprep

# loading data
dataflow = dprep.read_csv(path=r'data\crime0-10.csv')
dataflow.head(3)
```

||ID|Ärendenummer|Date|Blockera|IUCR|Primär typ|Beskrivning|Platsbeskrivning|Kvarhållande|Inrikes|...|Ward|Community-området|FBI: S kod|X-koordinat|Y-koordinaten|År|Uppdaterad den|Latitud|Longitud|Plats|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|07/05/2015 23:50:00: 00|050XX N NEWLAND PARA|0820|STÖLD|500 USD OCH UNDER|GATA|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|07/05/2015 11:30:00 PM|011XX W MORSE PARA|0460|BATTERI|ENKEL|GATA|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015:42:46 24: 00|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|07/05/2015 23:20:00: 00|121XX S FRONT PARA|0486|BATTERI|INRIKES BATTERI ENKEL|GATA|false|true|...|9|53|08B|||2015|07/12/2015:42:46 24: 00|


Använd den `substring(start, length)` uttryck för att extrahera prefixet från fall talkolumnen och placera den strängen i en ny kolumn `Case Category`. Skicka den `substring_expression` variabeln den `expression` parametern skapar en ny beräknad kolumn som kör uttrycket för varje post.

```python
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dataflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||ID|Ärendenummer|Case kategori|Date|Blockera|IUCR|Primär typ|Beskrivning|Platsbeskrivning|Kvarhållande|...|Ward|Community-området|FBI: S kod|X-koordinat|Y-koordinaten|År|Uppdaterad den|Latitud|Longitud|Plats|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|TYPOGRAFI|07/05/2015 23:50:00: 00|050XX N NEWLAND PARA|0820|STÖLD|500 USD OCH UNDER|GATA|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|TYPOGRAFI|07/05/2015 11:30:00 PM|011XX W MORSE PARA|0460|BATTERI|ENKEL|GATA|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015:42:46 24: 00|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|TYPOGRAFI|07/05/2015 23:20:00: 00|121XX S FRONT PARA|0486|BATTERI|INRIKES BATTERI ENKEL|GATA|false|true|...|9|53|08B|||2015|07/12/2015:42:46 24: 00|



Använd den `substring(start)` uttryck för att extrahera endast siffran från fall talkolumnen och skapa en ny kolumn. Konvertera den till en numerisk typ med den `to_number()` fungera, och skicka kolumnnamnet sträng som en parameter.

```python
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dataflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
case_id.head(3)
```

||ID|Ärendenummer|Ärende-Id|Date|Blockera|IUCR|Primär typ|Beskrivning|Platsbeskrivning|Kvarhållande|...|Ward|Community-området|FBI: S kod|X-koordinat|Y-koordinaten|År|Uppdaterad den|Latitud|Longitud|Plats|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|329907.0|07/05/2015 23:50:00: 00|050XX N NEWLAND PARA|0820|STÖLD|500 USD OCH UNDER|GATA|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466,-87.800174996)|
|1|10139776|HY329265|329265.0|07/05/2015 11:30:00 PM|011XX W MORSE PARA|0460|BATTERI|ENKEL|GATA|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015:42:46 24: 00|42.008124017|-87.65955018|(42.008124017,-87.65955018)|
|2|10140270|HY329253|329253.0|07/05/2015 23:20:00: 00|121XX S FRONT PARA|0486|BATTERI|INRIKES BATTERI ENKEL|GATA|false|true|...|9|53|08B|||2015|07/12/2015:42:46 24: 00|

## <a name="impute-missing-values"></a>Sedan imputera värden som saknas

SDK: N kan sedan imputera saknade värden i angivna kolumnerna. I det här exemplet att läsa in värden för latitud och longitud och försök sedan imputera värden som saknas i indata.

```python
import azureml.dataprep as dprep

# loading input data
df = dprep.read_csv(r'data\crime0-10.csv')
df = df.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
df = df.to_number(['Latitude', 'Longitude'])
df.head(5)
```

||ID|Kvarhållande|Latitud|Longitud|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|NaN|NaN|
|3|10139885|false|41.902152|-87.754883|
|4|10140379|false|41.885610|-87.657009|

Den tredje posten saknar värden för latitud och longitud. För att sedan imputera de saknade värdena, använder du `ImputeMissingValuesBuilder` att lära dig ett fast uttryck. Det kan sedan imputera kolumner med antingen ett beräknat `MIN`, `MAX`, `MEAN` värde, eller en `CUSTOM` värde. När `group_by_columns` har angetts värden som saknas kommer imputeras gruppvis med `MIN`, `MAX`, och `MEAN` beräknas per grupp.

Kontrollera den `MEAN` värdet för en latitud kolumn med hjälp av den `summarize()` funktion. Den här funktionen accepterar en matris med kolumner i den `group_by_columns` parametern för att ange nivå för aggregering. Den `summary_columns` parametern accepterar en `SummaryColumnsValue` anropa. Funktionsanropet anger kolumnnamnet på aktuella nya beräknade fältnamn och `SummaryFunction` att utföra.

```python
df_mean = df.summarize(group_by_columns=['Arrest'],
                       summary_columns=[dprep.SummaryColumnsValue(column_id='Latitude',
                                                                 summary_column_name='Latitude_MEAN',
                                                                 summary_function=dprep.SummaryFunction.MEAN)])
df_mean = df_mean.filter(dprep.col('Arrest') == 'false')
df_mean.head(1)
```

||Kvarhållande|Latitude_MEAN|
|-----|-----|----|
|0|false|41.878961|

Den `MEAN` värdet för Latitude ser ut korrekt, använder den `ImputeColumnArguments` funktionen sedan imputera den. Den här funktionen accepterar en `column_id` sträng, och en `ReplaceValueFunction` kan ange vilken typ av impute. Sedan imputera det med 42 baserat på externa kunskapskällor för longitudvärdet saknas.

Sedan imputera steg kan sammanlänkas i en `ImputeMissingValuesBuilder` objekt, med hjälp av funktionen builder `impute_missing_values()`. Den `impute_columns` parametern accepterar en matris med `ImputeColumnArguments` objekt. Anropa den `learn()` för att lagra impute stegen och använder sedan ett dataflöde objekt med `to_dataflow()`.

```python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id='Latitude',
                                          impute_function=dprep.ReplaceValueFunction.MEAN)
# impute with custom value 42
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=42)
# get instance of ImputeMissingValuesBuilder
impute_builder = df.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
# call learn() to learn a fixed program to impute missing values
impute_builder.learn()
# call to_dataflow() to get a data flow with impute step added
df_imputed = impute_builder.to_dataflow()

# check impute result
df_imputed.head(5)
```

||ID|Kvarhållande|Latitud|Longitud|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|41.878961|42.000000|
|3|10139885|false|41.902152|-87.754883|
|4|10140379|false|41.885610|-87.657009|

I resultatet ovan visas saknas latitud har tillräknade med den `MEAN` värdet för `Arrest=='false'` grupp. Saknas longitud har tillräknade med 42.

```python
imputed_longitude = df_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>Härled kolumner med exempel

En av de mer avancerade verktyg i Azure Machine Learning Data Prep SDK är möjligheten att härleda kolumner med hjälp av exempel på önskat resultat. På så sätt kan du ge SDK ett exempel så att det kan generera kod för att uppnå avsedda transformeringen.

```python
import azureml.dataprep as dprep
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
dataflow.head(10)
```

||DATE|REPORTTPYE|HOURLYDRYBULBTEMPF|HOURLYRelativeHumidity|HOURLYWindSpeed|
|----|----|----|----|----|----|
|0|1/1/2015 0:54|FM-15|22|50|10|
|1|1/1/2015 1:00|FM-12|22|50|10|
|2|1/1/2015 1:54|FM-15|22|50|10|
|3|1/1/2015 2:54|FM-15|22|50|11|
|4|1/1/2015 3:54|FM-15|24|46|13|
|5|1/1/2015 4:00|FM-12|24|46|13|
|6|1/1/2015 4:54|FM-15|22|52|15|
|7|1/1/2015 5:54|FM-15|23|48|17|
|8|1/1/2015 6:54|FM-15|23|50|14|
|9|1/1/2015 7:00|FM-12|23|50|14|

Anta att du behöver ansluta till den här filen med en datauppsättning där datum och tid är i formatet ”10 mars 2018 | 2 AM - 4 AM ”.

```python
builder = dataflow.builders.derive_column_by_example(source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(source_data=df.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview() 
```

||DATE|date_timerange|
|----|----|----|
|0|1/1/2015 0:54|Den 1 januari 2015 12 AM - 2 AM|
|1|1/1/2015 1:00|Den 1 januari 2015 12 AM - 2 AM|
|2|1/1/2015 1:54|Den 1 januari 2015 12 AM - 2 AM|
|3|1/1/2015 2:54|Den 1 januari 2015 2 AM - 4 AM|
|4|1/1/2015 3:54|Den 1 januari 2015 2 AM - 4 AM|
|5|1/1/2015 4:00|Den 1 januari 2015 4 AM - 06: 00|
|6|1/1/2015 4:54|Den 1 januari 2015 4 AM - 06: 00|
|7|1/1/2015 5:54|Den 1 januari 2015 4 AM - 06: 00|
|8|1/1/2015 6:54|Den 1 januari 2015 06: 00 - 08: 00|
|9|1/1/2015 7:00|Den 1 januari 2015 06: 00 - 08: 00|

Koden ovan skapar först hjälpverktyg för den härledda kolumnen. Du anger en matris med källkolumner att tänka på (`DATE`), och ett namn för den nya kolumnen som ska läggas till. Som det första exemplet skickar in den andra raden (index 1) och ge ett förväntat värde för den härledda kolumnen.

Slutligen kan du anropa `builder.preview()` och kan se den härledda kolumnen bredvid källkolumnen. Formatet verkar rätt, men du kan bara se värden för samma datum ”1 januari 2015”.

Nu kan skicka in antalet rader som du vill `skip` från överkanten för att se rader längre ner.

```
builder.preview(skip=30)
```

||DATE|date_timerange|
|-----|-----|-----|
|30|1/1/2015 22:54|Den 1 januari 2015 10 PM - 12: 00|
|31|1/1/2015 23:54|Den 1 januari 2015 10 PM - 12: 00|
|32|1/1/2015 23:59|Den 1 januari 2015 10 PM - 12: 00|
|33|1/2/2015 0:54|Den 1 februari 2015 12 AM - 2 AM|
|34|1/2/2015 1:00|Den 1 februari 2015 12 AM - 2 AM|
|35|1/2/2015 1:54|Den 1 februari 2015 12 AM - 2 AM|
|36|1/2/2015 2:54|Den 1 februari 2015 2 AM - 4 AM|
|37|1/2/2015 3:54|Den 1 februari 2015 2 AM - 4 AM|
|38|1/2/2015 4:00|Den 1 februari 2015 4 AM - 06: 00|
|39|1/2/2015 4:54|Den 1 februari 2015 4 AM - 06: 00|

Här kan du se ett problem med det genererade programmet. Endast baseras på ett exempel som du angav ovan, valde Härled programmet att tolka data som ”dag/månad/år”, vilket inte är vad du vill i det här fallet. För att åtgärda problemet, ange ett annat exempel med hjälp av den `add_example()` fungera på den `builder` variabeln.

```python
builder.add_example(source_data=preview_df.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
builder.preview(skip=30, count=10)
```

||DATE|date_timerange|
|-----|-----|-----|
|30|1/1/2015 22:54|Den 1 januari 2015 10 PM - 12: 00|
|31|1/1/2015 23:54|Den 1 januari 2015 10 PM - 12: 00|
|32|1/1/2015 23:59|Den 1 januari 2015 10 PM - 12: 00|
|33|1/2/2015 0:54|Den 2 januari 2015 12 AM - 2 AM|
|34|1/2/2015 1:00|Den 2 januari 2015 12 AM - 2 AM|
|35|1/2/2015 1:54|Den 2 januari 2015 12 AM - 2 AM|
|36|1/2/2015 2:54|Den 2 januari 2015 2 AM - 4 AM|
|37|1/2/2015 3:54|Den 2 januari 2015 2 AM - 4 AM|
|38|1/2/2015 4:00|Den 2 januari 2015 4 AM - 06: 00|
|39|1/2/2015 4:54|Den 2 januari 2015 4 AM - 06: 00|

Nu hanterar rader ”1/2/2015' som” Jan 2 2015, men om du tittar längre ned den härledda kolumnen ser du att värdena i slutet ingenting i härledda kolumnen. Du måste ange ett annat exempel för rad 66 för att åtgärda som.

```python
builder.add_example(source_data=preview_df.iloc[66], example_value='Jan 29, 2015 8PM-10PM')
builder.preview(count=10)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Den 1 januari 2015 10 PM - 12: 00|
|1|1/1/2015 23:54|Den 1 januari 2015 10 PM - 12: 00|
|2|1/1/2015 23:59|Den 1 januari 2015 10 PM - 12: 00|
|3|1/2/2015 0:54|Den 2 januari 2015 12 AM - 2 AM|
|4|1/2/2015 1:00|Den 2 januari 2015 12 AM - 2 AM|
|5|1/2/2015 1:54|Den 2 januari 2015 12 AM - 2 AM|
|6|1/2/2015 2:54|Den 2 januari 2015 2 AM - 4 AM|
|7|1/2/2015 3:54|Den 2 januari 2015 2 AM - 4 AM|
|8|1/2/2015 4:00|Den 2 januari 2015 4 AM - 06: 00|
|9|1/2/2015 4:54|Den 2 januari 2015 4 AM - 06: 00|

Att separera datum och tid med ' |', du lägger till ett annat exempel. Den här tiden, i stället för att skicka en rad från förhandsversionen av konstruera en ordlista med kolumnnamn som ska värdet för den `source_data` parametern.

```python
builder.add_example(source_data={'DATE': '11/11/2015 0:54'}, example_value='Nov 11, 2015 | 12AM-2AM')
builder.preview(count=10)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Ingen|
|1|1/1/2015 23:54|Ingen|
|2|1/1/2015 23:59|Ingen|
|3|1/2/2015 0:54|Ingen|
|4|1/2/2015 1:00|Ingen|
|5|1/2/2015 1:54|Ingen|
|6|1/2/2015 2:54|Ingen|
|7|1/2/2015 3:54|Ingen|
|8|1/2/2015 4:00|Ingen|
|9|1/2/2015 4:54|Ingen|

Detta hade tydligt negativa effekter, som nu endast rader som har värden i härledda kolumnen är de som motsvarar exakt de exempel som vi tillhandahålls. Anropa `list_examples()` på builder-objektet för att visa en lista över aktuella exempel produkter.

```python
examples = builder.list_examples()
```

| |DATE|Exempel|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 1:00|Den 1 januari 2015 12 AM - 2 AM|-1|
|1|1/2/2015 0:54|Den 2 januari 2015 12 AM - 2 AM|-2|
|2|1/29/2015 20:54|Den 29 januari 2015 20: 00 – 10 PM|-3|
|3|11/11/2015 0:54|11 november 2015 \| 12 AM - 2 AM|-4|

I det här fallet har inkonsekvent exempel angetts. Åtgärda problemet genom att byta ut tre första exemplen med rätt (inklusive ' |' mellan datum och tid).

Åtgärda inkonsekvent exempel genom att ta bort exempel som är felaktiga (genom att antingen skicka `example_row` från pandas-DataFrame eller genom att skicka in `example_id` värde) och sedan lägger till nya ändras exempel tillbaka.

```python
builder.delete_example(example_id=-1)
builder.delete_example(example_row=examples.iloc[1])
builder.delete_example(example_row=examples.iloc[2])
builder.add_example(examples.iloc[0], 'Jan 1, 2015 | 12AM-2AM')
builder.add_example(examples.iloc[1], 'Jan 2, 2015 | 12AM-2AM')
builder.add_example(examples.iloc[2], 'Jan 29, 2015 | 8PM-10PM')
builder.preview()
```

| | DATE | date_timerange |
| -------- | -------- | -------- |
| 0 | 1/1/2015 0:54 | Den 1 januari 2015 \| 12 AM - 2 AM |
| 1 | 1/1/2015 1:00 | Den 1 januari 2015 \| 12 AM - 2 AM |
| 2 | 1/1/2015 1:54 | Den 1 januari 2015 \| 12 AM - 2 AM |
| 3 | 1/1/2015 2:54 | Den 1 januari 2015 \| 2 AM - 4 AM |
| 4 | 1/1/2015 3:54 | Den 1 januari 2015 \| 2 AM - 4 AM |
| 5 | 1/1/2015 4:00 | Den 1 januari 2015 \| 4 AM - 06: 00|
| 6 | 1/1/2015 4:54 | Den 1 januari 2015 \| 4 AM - 06: 00|
| 7 | 1/1/2015 5:54 | Den 1 januari 2015 \| 4 AM - 06: 00|
| 8 | 1/1/2015 6:54 | Den 1 januari 2015 \| 06: 00 - 08: 00|
| 9 | 1/1/2015 7:00 | Den 1 januari 2015 \| 06: 00 - 08: 00|

Nu verkar vara korrekta data och du anropar `to_dataflow()` på builder, vilket returnerar ett dataflöde med de härledda kolumner har lagts till.

```python
dataflow = builder.to_dataflow()
df = dataflow.to_pandas_dataframe()
```

## <a name="filtering"></a>Filtrering

SDK innehåller metoder `Dataflow.drop_columns` och `Dataflow.filter` så att du kan filtrera bort kolumner eller rader.

### <a name="initial-setup"></a>Första installation

```python
import azureml.dataprep as dprep
from datetime import datetime
dataflow = dprep.read_csv(path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|N|1|0|0|0|0|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|N|1|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|N|5|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|N|1|0|0|0|0|1|.00|0|0|3.25|

### <a name="filtering-columns"></a>Filtrera kolumner

Filtrera kolumner och Använd `Dataflow.drop_columns`. Den här metoden tar en lista med kolumner att släppa eller en mer komplex argumentet anropas `ColumnSelector`.

#### <a name="filtering-columns-with-list-of-strings"></a>Filtrera kolumner med lista med strängar

I det här exemplet `drop_columns` tar en lista med strängar. Varje sträng måste exakt matcha den önskade kolumnen att släppa.

```python
dataflow = dataflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|0|0|0|0|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|0|0|0|0|1|.00|0|0|3.25|

#### <a name="filtering-columns-with-regex"></a>Kolumner med regex-filtrering

Du kan också använda den `ColumnSelector` uttryck att ta bort kolumner som matchar ett regex-uttryck. Ta bort alla kolumner som matchar uttrycket i det här exemplet `Column*|.*longitude|.*latitude`.

```python
dataflow = dataflow.drop_columns(dprep.ColumnSelector('Column*|.*longitud|.*latitude', True, True))
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|1|.00|0|0|3.25|

## <a name="filtering-rows"></a>Filtrera rader

Använd om du vill filtrera rader `DataFlow.filter`. Den här metoden tar ett Azure Machine Learning Data Prep SDK-uttryck som ett argument och returnerar ett nytt dataflöde med raderna som uttrycket utvärderas som True. Uttryck skapas med hjälp av uttrycket builders (`col`, `f_not`, `f_and`, `f_or`) och regelbundna operatörer (>, <>, =, < =, ==,! =).

### <a name="filtering-rows-with-simple-expressions"></a>Filtrera rader med enkla uttryck

Använda Uttrycksverktyget `col`, ange kolumnnamnet som en Strängargumentet `col('column_name')`. Använd det här uttrycket i kombination med något av följande standard operatorer >, <>, =, < =, ==,! = för att skapa ett uttryck som `col('Tip_amount') > 0`. Slutligen skickar det inbyggda uttrycket i den `Dataflow.filter` funktion.

I det här exemplet `dataflow.filter(col('Tip_amount') > 0)` returnerar ett nytt dataflöde med rader där värdet för `Tip_amount` är större än 0.

> [!NOTE] 
> `Tip_amount` först omvandlas till numeriska, vilket gör att vi kan skapa ett uttryck för att jämföra den med andra numeriska värden.

```python
dataflow = dataflow.to_number(['Tip_amount'])
dataflow = dataflow.filter(dprep.col('Tip_amount') > 0)
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-01 19:33:28|2013-08-01 19:35:21|5|.00|0.08|0|4.58|
|1|2013-08-05 13:16:38|2013-08-05 13:18:24|1|.00|0,30|0|3.8|
|2|2013-08-05 14:11:42|2013-08-05 14:12:47|1|.00|1,05|0|4.55|
|3|2013-08-05 14:15:56|2013-08-05 14:18:04|5|.00|2.22|0|5.72|
|4|2013-08-05 14:42:14|2013-08-05 14:42:38|1|.00|0.88|0|4.38|

### <a name="filtering-rows-with-complex-expressions"></a>Filtrera rader med komplexa uttryck

Om du vill filtrera med komplexa uttryck, kombinera en eller flera enkla uttryck med uttryck builders `f_not`, `f_and`, eller `f_or`.

I det här exemplet `Dataflow.filter` returnerar ett nytt dataflöde med rader där `'Passenger_count'` är mindre än 5 och `'Tolls_amount'` är större än 0.

```python
dataflow = dataflow.to_number(['Passenger_count', 'Tolls_amount'])
dataflow = dataflow.filter(dprep.f_and(dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-08 12:16:00|2013-08-08 12:16:00|1.0|.00|2.25|5,00|19.75|
|1|2013-08-12 14:43:53|2013-08-12 15:04:50|1.0|5.28|6.46|5.33|32.29|
|2|2013-08-12 19:48:12|12-08-2013 20:03:42|1.0|5.50|1,00|10.66|30.66|
|3|2013-08-13 06:11:06|2013-08-13 06:30:28|1.0|9.57|7.47|5.33|44.8|
|4|2013-08-16 20:33:50|2013-08-16 20:48:50|1.0|5.63|3.00|5.33|27.83|

Det är också möjligt att filtrera rader som kombinerar flera Uttrycksverktyget för att skapa ett kapslade uttryck.

> [!NOTE]
> `lpep_pickup_datetime` och `Lpep_dropoff_datetime` först konverteras till datetime, vilket gör att vi kan skapa ett uttryck för att jämföra den med andra datetime-värden.

```python
dataflow = dataflow.to_datetime(['lpep_pickup_datetime', 'Lpep_dropoff_datetime'], ['%Y-%m-%d %H:%M:%S'])
dataflow = dataflow.to_number(['Total_amount', 'Trip_distance'])
mid_2013 = datetime(2013,7,1)
dataflow = dataflow.filter(
    dprep.f_and(
        dprep.f_or(
            dprep.col('lpep_pickup_datetime') > mid_2013,
            dprep.col('Lpep_dropoff_datetime') > mid_2013),
        dprep.f_and(
            dprep.col('Total_amount') > 40,
            dprep.col('Trip_distance') < 10)))
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-13 06:11:06 + 00:00|2013-08-13 06:30:28 + 00:00|1.0|9.57|7.47|5.33|44.80|
|1|2013-08-23 12:28:20 + 00:00|2013-08-23 12:50:28 + 00:00|2.0|8.22|8.08|5.33|40.41|
|2|2013-08-25 09:12:52 + 00:00|2013-08-25 09:34:34 + 00:00|1.0|8.80|8.33|5.33|41,66|
|3|2013-08-25 16:46:51 + 00:00|2013-08-25 17:13:55 + 00:00|2.0|9.66|7.37|5.33|44.20|
|4|2013-08-25 17:42:11 + 00:00|2013-08-25 18:02:57 + 00:00|1.0|9.60|6.87|5.33|41.20|

## <a name="custom-python-transforms"></a>Anpassad Python-transformeringar

Det kommer alltid att scenarier när det enklaste alternativet för att göra en omvandling är skriva egna skript. SDK innehåller tre tillägg punkter som du kan använda för anpassad Python-skript.

- Ny kolumn för skript
- Nytt skript-filter
- Omvandla partition

Var och en av tillägg som stöds i båda skala upp och skala ut körning. En stor fördel med att använda dessa tillägg är att du inte behöver att hämta alla data för att skapa en dataram. Anpassad Python kod körs precis som transformeringar i skala, av partitionen och vanligtvis parallellt.

### <a name="initial-data-preparation"></a>Inledande dataförberedelse

Starta genom att läsa in vissa data från Azure Blob.

```python
import azureml.dataprep as dprep
col = dprep.col

df = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
df.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale County|10171002158| |
|1|ALABAMA|1|101710|Hale County|10171002162| |
|2|ALABAMA|1|101710|Hale County|10171002156| |
|3|ALABAMA|1|101710|Hale County|10171000588|2|
|4|ALABAMA|1|101710|Hale County|10171000589| |

Trimma ned datauppsättningen och utföra vissa grundläggande transformeringar.

```python
df = df.keep_columns(['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
df = df.replace_na(columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
df = df.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
df.head(5)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1.017100e + 10|Ingen|
|1|ALABAMA|Hale County|1.017100e + 10|Ingen|
|2|ALABAMA|Hale County|1.017100e + 10|Ingen|
|3|ALABAMA|Hale County|1.017100e + 10|2|
|4|ALABAMA|Hale County|1.017100e + 10|Ingen|

Leta efter null-värden med hjälp av följande filter.

```python
df.filter(col('MAM_MTH00numvalid_1011').is_null()).head(5)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1.017100e + 10|Ingen|
|1|ALABAMA|Hale County|1.017100e + 10|Ingen|
|2|ALABAMA|Hale County|1.017100e + 10|Ingen|
|3|ALABAMA|Hale County|1.017100e + 10|Ingen|
|4|ALABAMA|Hale County|1.017100e + 10|Ingen|

### <a name="transform-partition"></a>Omvandla partition

Funktionen pandas ersätta alla null-värden med 0. Den här koden körs av partition, inte på den hela datauppsättningen i taget. Det innebär att på en stor datauppsättning, den här koden kan köras parallellt eftersom körningen bearbetar data partition av partition.

Python-skriptet måste definiera en funktion som kallas `transform()` som tar två argument, `df` och `index`. Den `df` argumentet ska vara en pandas-dataframe som innehåller data för partitionen och `index` argumentet är en unik identifierare för partitionen. Transform-funktionen helt kan redigera den överförda dataramen, men måste returnera en dataram. Alla bibliotek som Python-skriptet importerar måste finnas i miljön där dataflödet körs.

```python
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
df.head(5)
```

||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1.017100e + 10|0.0|
|1|ALABAMA|Hale County|1.017100e + 10|0.0|
|2|ALABAMA|Hale County|1.017100e + 10|0.0|
|3|ALABAMA|Hale County|1.017100e + 10|2.0|
|4|ALABAMA|Hale County|1.017100e + 10|0.0|

### <a name="new-script-column"></a>Ny kolumn för skript

Du kan använda Python-kod för att skapa en ny kolumn som har namnet på delstaten och Tillståndsnamn och även för att utnyttja Tillståndsnamn. Gör detta genom att använda den `new_script_column()` metoden på dataflödet.

Python-skriptet måste definiera en funktion som kallas `newvalue()` som tar ett argument `row`. Den `row` argumentet är en dict (`key`: kolumnnamnet `val`: aktuellt värde) och kommer att skickas till den här funktionen för varje rad i datauppsättningen. Den här funktionen måste returnera ett värde som ska användas i den nya kolumnen. Alla bibliotek som Python-skriptet importerar måste finnas i miljön där dataflödet körs.

```python
df = df.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
df.head(5)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|Hale County, Alabama|1.017100e + 10|0.0|
|1|ALABAMA|Hale County|Hale County, Alabama|1.017100e + 10|0.0|
|2|ALABAMA|Hale County|Hale County, Alabama|1.017100e + 10|0.0|
|3|ALABAMA|Hale County|Hale County, Alabama|1.017100e + 10|2.0|
|4|ALABAMA|Hale County|Hale County, Alabama|1.017100e + 10|0.0|

### <a name="new-script-filter"></a>Nytt skript-Filter

Skapa en Python-uttryck för att filtrera de uppgifter som skall endast rader där ”Hale' inte är i den nya `county_state` kolumn. Uttrycket returnerar `True` om vi vill hålla raden och `False` att släppa raden.

```python
df = df.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
df.head(5)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e + 10|1.0|
|1|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e + 10|0.0|
|2|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e + 10|0.0|
|3|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e + 10|0.0|
|4|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e + 10|0.0|
