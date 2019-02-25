---
title: 'Läs in: Förbered data Python SDK'
titleSuffix: Azure Machine Learning service
description: Läs mer om att läsa in data med Azure Machine Learning Data Prep SDK. Du kan läsa in olika typer av indata, ange data filtyper och parametrar eller använda smart läsning av SDK-funktioner för att automatiskt identifiera filtyp.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 2/22/2019
ms.custom: seodec18
ms.openlocfilehash: 0fe77a1093bec52c3786a9ae623a2d63e1ba82ce
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750947"
---
# <a name="load-and-read-data-with-azure-machine-learning"></a>Läsa in och läsa data med Azure Machine Learning

I den här artikeln får du lära dig olika metoder för att läsa in data med hjälp av Azure Machine Learning Data Prep SDK. Referensdokumentation för SDK finns i den [översikt](https://aka.ms/data-prep-sdk). SDK stöder flera funktioner för data datainmatning, inklusive:

* Läsa in från många filtyper med parsning parametern inferens (kodning, avgränsare, rubriker)
* Typ konverze med inferens under inläsningen av filen
* Stödet för MS SQL Server och Azure Data Lake Storage

I följande tabell visas en uppsättning funktioner som används för att läsa in data från vanliga filtyper.

| Filtyp | Funktion | Referenslänk |
|-------|-------|-------|
|Alla|`auto_read_file()`|[Referens](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#auto-read-file-path--filepath--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|Text|`read_lines()`|[Referens](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-lines-path--filepath--header--azureml-dataprep-api-engineapi-typedefinitions-promoteheadersmode----promoteheadersmode-none--0---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---skip-rows--int---0--skip-mode--azureml-dataprep-api-engineapi-typedefinitions-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|CSV|`read_csv()`|[Referens](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-csv-path--filepath--separator--str--------header--azureml-dataprep-api-engineapi-typedefinitions-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---quoting--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-engineapi-typedefinitions-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false--archive-options--azureml-dataprep-api--archiveoption-archiveoptions---none-----azureml-dataprep-api-dataflow-dataflow)|
|Excel|`read_excel()`|[Referens](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-excel-path--filepath--sheet-name--str---none--use-column-headers--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|Fast bredd|`read_fwf()`|[Referens](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-fwf-path--filepath--offsets--typing-list-int---header--azureml-dataprep-api-engineapi-typedefinitions-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-engineapi-typedefinitions-skipmode----skipmode-none--0---include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|JSON|`read_json()`|[Referens](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-json-path--filepath--encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---flatten-nested-arrays--bool---false--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|

## <a name="load-data-automatically"></a>Läsa in data automatiskt

Läs in data automatiskt utan att ange filtypen genom att använda den `auto_read_file()` funktion. Typ av filen och argument som krävs för att läsa den är härledd automatiskt.

```python
import azureml.dataprep as dprep

dataflow = dprep.auto_read_file(path='./data/any-file.txt')
```

Det här är användbart för att automatiskt upptäcka filtyp, kodning och andra parsning argument allt från en lämplig startpunkt. Funktionen utför automatiskt följande som vanligen utförs vid inläsning av avgränsad data:

* Procedurens och ställa in avgränsaren
* Hoppar över tomma poster överst i filen
* Procedurens och ange rubrikraden

Om du vet filen Skriv förbereds i förväg och vill styra hur tolkas uttryckligen kan du också använda fil-specifika funktioner.

## <a name="load-text-line-data"></a>Läsa in text raddata

Om du vill läsa enkel textdata i en dataflöde, använda den `read_lines()` utan att ange valfria parametrar.

```python
dataflow = dprep.read_lines(path='./data/text_lines.txt')
dataflow.head(5)
```

||Rad|
|----|-----|
|0|Datum \| \| minsta temperatur \| \| maximalt temperatur|
|1|2015-07-1 \| \| -4.1 \| \| 10.0|
|2|2015-07-2 \| \| -0.8 \| \| 10.8|


När data matas in, kör följande kod för att konvertera objektet dataflöde till ett Pandas-dataframe.

```python
pandas_df = dataflow.to_pandas_dataframe()
```

## <a name="load-csv-data"></a>Läs in CSV-data

När du läser avgränsade filer kan underliggande körningen härleda parsning parametrarna (avgränsare, kodning, om du vill använda rubriker osv.). Kör följande kod för att försöka läsa en CSV-fil genom att ange endast dess plats.

```python
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dataflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0||stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|Hale County|10171002158| |
|2|ALABAMA|1|101710|Hale County|10171002162| |


Om du vill exkludera rader under inläsningen, definiera den `skip_rows` parametern. Den här parametern hoppar över inläsning rader fallande i CSV-filen (med ett ett-baserade index).

```python
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dataflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale County|10171002158|29|
|1|ALABAMA|1|101710|Hale County|10171002162|40 |

Kör följande kod för att visa datatyperna för kolumnen.

```python
dataflow.head(1).dtypes
```
Utdata:

    stnam                     object
    fipst                     object
    leaid                     object
    leanm10                   object
    ncessch                   object
    schnam10                  object
    MAM_MTH00numvalid_1011    object
    dtype: object

Som standard ändrar inte Azure Machine Learning Data Prep SDK-datatypen. Datakällan som du läser från är en textfil, så att SDK läser alla värden som strängar. I det här exemplet ska numeriska kolumner parsas as-nummer. Ange den `inference_arguments` parameter `InferenceArguments.current_culture()` automatiskt härleda och konvertera tabellens kolumntyper under läsa filen.

```
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dataflow.head(1).dtypes
```
Utdata:

    stnam                      object
    fipst                     float64
    leaid                     float64
    leanm10                    object
    ncessch                   float64
    schnam10                   object
    ALL_MTH00numvalid_1011    float64
    dtype: object


Flera av kolumnerna som identifierades på rätt sätt som numeriska och deras typ har angetts till `float64`.

## <a name="use-excel-data"></a>Använda Excel-data

SDK innehåller en `read_excel()` funktionen för att läsa in Excel-filer. Som standard funktionen läser in första blad i arbetsboken. För att definiera ett specifikt ark att läsa in, definiera den `sheet_name` parameter med strängvärdet för bladets namn.

```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dataflow.head(5)
```

||Kolumn1|Kolumn2|Kolumn3|Kolumn4|Column5|Kolumn6|Column7|Column8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|
|1|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|
|2|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|Ingen|
|3|rangordning|Titel|Studio|Världsomfattande|Inrikes / %|Kolumn1|Utländskt nummer / %|Kolumn2|År ^|
|4|1|Avatar|Fox|2788|760.5|0.273|2027.5|0.727|2009 ^|5|

Utdata visar att data i det andra arket har tre tomma rader innan rubrikerna. Den `read_excel()` funktionen innehåller valfria parametrar för att hoppa över rader och använda rubriker. Kör följande kod för att hoppa över de tre första raderna och Använd den fjärde raden som rubrikerna.

```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_column_headers=True, skip_rows=3)
```

||rangordning|Titel|Studio|Världsomfattande|Inrikes / %|Kolumn1|Utländskt nummer / %|Kolumn2|År ^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Avatar|Fox|2788|760.5|0.273|2027.5|0.727|2009 ^|
|1|2|Titanic|Par.|2186.8|658.7|0.301|1528.1|0.699|1997 ^|

## <a name="load-fixed-width-data-files"></a>Läsa in datafiler med fast bredd

Till loadfixed bredd filer, kan du ange en lista med förskjutningar för tecken. Den första kolumnen antas alltid börjar vid noll förskjutningen.

```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dataflow.head(5)
```

||010000|99999|MUSENHETEN NORGE|NO|NO_1|ENRS|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|MUSENHETEN NORGE|NO|NO|ENSO||||
|1|010010|99999|JAN MAYEN|NO|JN|ENJA|+70933|-008667|+00090|


För att undvika identifiering av rubriken och parsa rätt data, skicka `PromoteHeadersMode.NONE` till den `header` parametern.

```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)
```

||Kolumn1|Kolumn2|Kolumn3|Kolumn4|Column5|Kolumn6|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|MUSENHETEN NORGE|NO|NO_1|ENRS|Column7|Column8|Column9|
|1|010003|99999|MUSENHETEN NORGE|NO|NO|ENSO||||


## <a name="load-sql-data"></a>Läs in SQL-data

SDK: N kan också läsa in data från en SQL-källa. För närvarande stöds endast Microsoft SQL Server. Om du vill läsa data från en SQLServer, skapa en [ `MSSQLDataSource` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.mssqldatasource?view=azure-dataprep-py) objekt som innehåller anslutningsparametrarna. Parametern password av `MSSQLDataSource` accepterar en [ `Secret` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#register-secret-value--str--id--str---none-----azureml-dataprep-api-engineapi-typedefinitions-secret) objekt. Du kan skapa hemliga objekt på två sätt:

* Registrera hemligheten och dess värde med motorn för körning.
* Skapa hemligheten med endast en `id` (om värdet för hemligheten har redan registrerats i körningsmiljön) med hjälp av `dprep.create_secret("[SECRET-ID]")`.

```python
secret = dprep.register_secret(value="[SECRET-PASSWORD]", id="[SECRET-ID]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=secret)
```

När du skapar ett datakällobjekt, kan du fortsätta att läsa data från frågeresultatet visas.

```python
dataflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dataflow.head(5)
```

||ProductID|Namn|ProductNumber|Färg|StandardCost|ListPrice|Storlek|Vikt|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|ThumbNailPhoto|ThumbnailPhotoFileName|ROWGUID|Modifieddate kunde|
|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
|0|680|HL-ram – svart, 58|FR-R92B-58|Svart|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00 + 00:00|Ingen|Ingen|b-GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000 + 00:00|
|1|706|HL-ram – röd, 58|FR-R92R-58|Röd|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00 + 00:00|Ingen|Ingen|b-GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000 + 00:00|
|2|707|Sport – 100-Hjälm, röd|HL-U509-R|Röd|13.0863|34.99|Ingen|Ingen|35|33|2005-07-01 00:00:00 + 00:00|Ingen|Ingen|b-GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000 + 00:00|


## <a name="use-azure-data-lake-storage"></a>Använda Azure Data Lake Storage

Det finns två sätt SDK kan hämta nödvändiga OAuth-token för att komma åt Azure Data Lake Storage:

* Hämta åtkomsttoken från en senaste användarinloggning Azure CLI-session
* Använda ett huvudnamn för tjänsten (SP) och ett certifikat som hemlighet

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>En åtkomsttoken från de senaste Azure CLI-sessionen

Kör följande kommando på den lokala datorn.

```azurecli
az login
az account show --query tenantId
dataflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dataflow.head(5) head
```

> [!NOTE]
> Om ditt användarkonto är medlem i fler än en Azure-klient, måste du ange klienten i formuläret AAD-URL-värddatornamn.

### <a name="create-a-service-principal-with-the-azure-cli"></a>Skapa ett huvudnamn för tjänsten med Azure CLI

Använda Azure CLI för att skapa ett huvudnamn för tjänsten och motsvarande certifikat. Viss tjänstens huvudnamn har konfigurerats med den `reader` roll med sitt omfång som har minskats till endast Azure Data Lake Storage-konto 'dpreptestfiles'.

```azurecli
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```

Det här kommandot genererar den `appId` och sökvägen till certifikatfilen (vanligtvis i arbetsmappen). Filen .crt innehåller både offentliga certifikat och den privata nyckeln i PEM-format.

```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

Konfigurera ACL för filsystemet Azure Data Lake Storage med objekt-ID för användaren. I det här exemplet används tjänstens huvudnamn.

```azurecli
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```

Konfigurera `Read` och `Execute` åtkomst för filsystemet Azure Data Lake Storage du konfigurera ACL för mappar och filer individuellt. Detta beror på att den underliggande HDFS ACL-modellen har inte stöd för arv.

```azurecli
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r-x" --path /
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r--" --path /farmers-markets.csv
```

```
certThumbprint = 'C2:08:9D:9E:D1:74:FC:EB:E9:7E:63:96:37:1C:13:88:5E:B9:2C:84'
certificate = ''
with open('./data/adls-dpreptestfiles.crt', 'rt', encoding='utf-8') as crtFile:
    certificate = crtFile.read()

servicePrincipalAppId = "8dd38f34-1fcb-4ff9-accd-7cd60b757174"
```

### <a name="acquire-an-oauth-access-token"></a>Hämta en OAuth-åtkomsttoken

Använd den `adal` paketet (`pip install adal`) att skapa en autentiseringskontext på MSFT-klient och få en OAuth-åtkomsttoken. För ADLS, resursen i tokenbegäran måste vara för 'https://datalake.azure.net', som skiljer sig från de flesta andra Azure-resurser.

```python
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext('https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate('https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dataflow = dprep.read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dataflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Webbplats|Gata|city|Län|
|----|------|-----|----|----|----|----|
|0|1012063|Kaledonien bönder marknaden associering - Danville|https://sites.google.com/site/caledoniafarmers... ||Danville|Kaledonien|
|1|1011871|Stearns vinterinspirerad bönder ' marknaden|http://Stearnshomestead.com |6975 upphöjning väg|Parma|Cuyahoga|
|2|1011878|100 mil marknaden|http://www.pfcmarkets.com |507 Harrison St|Kalamazoo|Kalamazoo|
|3|1009364|106 S. Main gata bönder marknaden|http://thetownofsixmile.wordpress.com/ |106 S. Main gata|Sex mil|||
|4|1010691|10 gata Community bönder marknaden|https://agrimissouri.com/... |10 gata och poppel|Lamar|Barton|

## <a name="next-steps"></a>Nästa steg

* Finns i SDK [översikt](https://aka.ms/data-prep-sdk) designmönster och användningsexempel
* Finns i Azure Machine Learning Data Prep SDK [självstudien](tutorial-data-prep.md) ett exempel på hur du löser ett specifikt scenario