---
title: Läs in data med Azure Machine Learning Data Prep SDK – Python
description: Läs mer om att läsa in data med Azure Machine Learning Data Prep SDK. Du kan läsa in olika typer av indata, ange data filtyper och parametrar eller använda smart läsning av SDK-funktioner för att automatiskt identifiera filtyp.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 000870e3273799930f519ff32d6b072d8c2d1f10
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989693"
---
#<a name="load-data-with-the-azure-machine-learning-data-prep-sdk"></a>Läs in data med Azure Machine Learning Data Prep SDK

Den [Azure Machine Learning Data Prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py) att läsa in olika typer av indata. Du kan ange datatyp för fil och dess parametrar eller använda funktionen för smarta läsning SDK att automatiskt identifiera vilken typ av en fil.

## <a name="read-lines"></a>Läs rader
Ett av de enklaste sätten att läsa in data är att läsa det som textrader.

```
dataflow = dprep.read_lines(path='./data/text_lines.txt')
dataflow.head(5)
```
||Rad|
|----|-----|
|0|Datum \| \| minsta temperatur \| \| maximalt temperatur|
|1|2015-07-1 \| \| -4.1 \| \| 10.0|
|2|2015-07-2 \| \| -0.8 \| \| 10.8|
|3|2015-07-3 \| \| -7.0 \| \| 10,5|
|4|2015-07-4 \| \| -5.5 \| \| 9.3|

När data matas in, kan du hämta en pandas-DataFrame för hela datauppsättningen.

```
df = dataflow.to_pandas_dataframe()
df
```

||Rad|
|----|-----|
|0|Datum\| \| minsta temperatur\| \| maximalt temperatur|
|1|2015-07-1\| \| 4.1\| \| 10.0|
|2|2015-07-2\| \| 0,8\| \| 10.8|
|3|2015-07-3\| \| 7.0\| \| 10,5|
|4|2015-07-4\| \| 5.5\| \| 9.3|

## <a name="read-csv"></a>Läsa CSV
När du läser avgränsade filer, kan du låta den underliggande runtime härleda parsning parametrar (t.ex en avgränsare, kodning, om du vill använda rubriker osv) i stället för att låta dem. Försök att läsa en fil genom att ange endast dess plats i det här exemplet. 

```
# SAS expires June 16th, 2019
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dataflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0||stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|ALABAMA|1|101710|Hale County|10171002158| |
|2|ALABAMA|1|101710|Hale County|10171002162| |
|3|ALABAMA|1|101710|Hale County|10171002156| |
|4|ALABAMA|1|101710|Hale County|10171000588|2|

En av de parametrar som du kan ange är ett antal rader att hoppa över i filer som du läser. Använd följande kod för att filtrera bort raden dubbletter.
```
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dataflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale County|10171002158|29|
|1|ALABAMA|1|101710|Hale County|10171002162|40 |
|2|ALABAMA|1|101710|Hale County|10171002156| 43|
|3|ALABAMA|1|101710|Hale County|10171000588|2|
|4|ALABAMA|1|101710|Hale County|10171000589|23 |

Därefter kan du titta på datatyperna för kolumnerna.
```
dataflow.head(1).dtypes

stnam                     object
fipst                     object
leaid                     object
leanm10                   object
ncessch                   object
schnam10                  object
MAM_MTH00numvalid_1011    object
dtype: object
```
Tyvärr återvände alla våra kolumner som strängar. Det beror på att som standard inte ändrar Azure Machine Learning Data Prep SDK-datatypen. Vi läser från datakällan är en textfil, så SDK läser alla värden som strängar. Det här exemplet, men vill vi parsa numeriska kolumner as-nummer. Om du vill göra detta måste ange du parametern inference_arguments till current_culture.

```
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dataflow.head(1).dtypes

stnam                      object
fipst                     float64
leaid                     float64
leanm10                    object
ncessch                   float64
schnam10                   object
ALL_MTH00numvalid_1011    float64
dtype: object
```
Flera kolumner identifierades korrekt as-nummer och deras typ har angetts till float64. Du kan hämta en pandas-DataFrame för hela datauppsättningen med inmatningen av klar.

```
df = dataflow.to_pandas_dataframe()
df
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1.017100e + 10|49.0|
|1|ALABAMA|Hale County|1.017100e + 10|40.0|
|2|ALABAMA|Hale County|1.017100e + 10|43.0|
|3|ALABAMA|Hale County|1.017100e + 10|2.0|
|4|ALABAMA|Hale County|1.017100e + 10|23.0|

## <a name="read-excel"></a>Läs Excel
Azure Machine Learning Data Prep SDK innehåller en `read_excel` funktionen för att läsa in Excel-filer.
```
dataflow = dprep.read_excel(path='./data/excel.xlsx')
dataflow.head(5)
```
||Kolumn1|Kolumn2|Kolumn3|Kolumn4|Column5|Kolumn6|Column7|Column8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|Hoba|Järn IVB|60000000.0|Hittad|1920.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |-19.58333|17.91667|
|1|Kap York|Järn, IIIAB|58200000.0|Hittad|1818.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |76.13333|-64.93333|
|2|Campo del Cielo|Järn IAB MG|50000000.0|Hittad|1576.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |-27.46667|-60.58333|
|3|Canyon Diablo|Järn IAB MG|30000000.0|Hittad|1891.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |35.05000|-111.03333|
|4|Armanty|Järn, IIIE|28000000.0|Hittad|1898.0|http://www.lpi.usra.edu/meteor/metbull.php?cod... |47.00000|88.00000|

Du har läst in det första bladet för Excel-filen. Du kan göra detta genom att ange namnet på bladet som du vill läsa in. Om du vill läsa in det andra arket i stället kan du ange dess namn som ett argument.
```
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

Som du ser tabellens i det andra arket rubriker och tre tomma rader. Du måste ändra funktionens argument.
```
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_header=True, skip_rows=3)
df = dataflow.to_pandas_dataframe()
df
```

||rangordning|Titel|Studio|Världsomfattande|Inrikes / %|Kolumn1|Utländskt nummer / %|Kolumn2|År ^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Avatar|Fox|2788|760.5|0.273|2027.5|0.727|2009 ^|
|1|2|Titanic|Par.|2186.8|658.7|0.301|1528.1|0.699|1997 ^|
|2|3|Marvel's Avengers|BV|1518.6|623.4|0.41|895.2|0.59|2012|
|3|4|Harry Potter och den Deathly Hallows del 2|WB|1341.5|381|0.284|960.5|0.716|2011|
|4|5|Låsta|BV|1274.2|400.7|0.314|873.5|0.686|2013|

## <a name="read-fixed-width-files"></a>Läsa filer med fast bredd
Du kan ange en lista med förskjutningar för fast bredd-filer. Den första kolumnen antas alltid börjar vid förskjutningen 0.

```
dataflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dataflow.head(5)
```
||010000|99999|MUSENHETEN NORGE|NEJ|NO_1|ENRS|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|MUSENHETEN NORGE|NEJ|NEJ|ENSO||||
|1|010010|99999|JAN MAYEN|NEJ|JN|ENJA|+70933|-008667|+00090|
|2|010013|99999|ROST|NEJ|NEJ|||||
|3|010014|99999|SOERSTOKKEN|NEJ|NEJ|ENSO|+59783|+005350|+00500|
|4|010015|99999|BRINGELAND|NEJ|NEJ|ENBL|+61383|+005867|+03270|


Om det finns inga rubriker i filer, ska du behandla den första raden som data. Genom att skicka `PromoteHeadersMode.NONE` till lösenordsargument rubrik som du kan undvika identifiering av rubriken och få rätt data.

```
dataflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)

df = dataflow.to_pandas_dataframe()
df
```
||Kolumn1|Kolumn2|Kolumn3|Kolumn4|Column5|Kolumn6|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|MUSENHETEN NORGE|NEJ|NO_1|ENRS|Column7|Column8|Column9|
|1|010003|99999|MUSENHETEN NORGE|NEJ|NEJ|ENSO||||
|2|010010|99999|JAN MAYEN|NEJ|JN|ENJA|+70933|-008667|+00090|
|3|010013|99999|ROST|NEJ|NEJ|||||
|4|010014|99999|SOERSTOKKEN|NEJ|NEJ|ENSO|+59783|+005350|+00500|
|5|010015|99999|BRINGELAND|NEJ|NEJ|ENBL|+61383|+005867|+03270|

## <a name="read-sql"></a>Läsa SQL
Azure Machine Learning Data Prep SDK kan också läsa in data från SQL-servrar. För närvarande stöds endast Microsoft SQL Server.
Skapa ett datakällobjekt som innehåller anslutningsinformationen för att läsa data från en SQLServer.

```
secret = dprep.register_secret("[SECRET-USERNAME]", "[SECRET-PASSWORD]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=[DATABASE-PASSWORD])
```
Som du kan se, Lösenordsparametern av `MSSQLDataSource` accepterar hemliga objekt. Du kan få hemliga objekt på två sätt:
-   Registrera hemligheten och dess värde med motorn för körning. 
-   Skapa hemligheten med bara ett ID (användbart om värdet för hemligheten har redan registrerats i körningsmiljön).

När du skapar ett datakällobjekt, kan du fortsätta att läsa data.
```
dataflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dataflow.head(5)
```

||ProductID|Namn|ProductNumber|Färg|StandardCost|ListPrice|Storlek|Vikt|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|ThumbNailPhoto|ThumbnailPhotoFileName|ROWGUID|Modifieddate kunde|
|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
|0|680|HL-ram – svart, 58|FR-R92B-58|Svart|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00 + 00:00|Ingen|Ingen|b-GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000 + 00:00|
|1|706|HL-ram – röd, 58|FR-R92R-58|Röd|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00 + 00:00|Ingen|Ingen|b-GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000 + 00:00|
|2|707|Sport – 100-Hjälm, röd|HL-U509-R|Röd|13.0863|34.99|Ingen|Ingen|35|33|2005-07-01 00:00:00 + 00:00|Ingen|Ingen|b-GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000 + 00:00|
|3|708|Sport – 100-Hjälm, svart|HL-U509|Svart|13.0863|34.99|Ingen|Ingen|35|33|2005-07-01 00:00:00 + 00:00|Ingen|Ingen|b-GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|a25a44fb-c2de-4268-958f-110b8d7621e2|2008-03-11 |10:01:36.827000 + 00:00|
|4|709|Mountainbikestrumpor, M|SÅ-B909-M|Vit|3.3963|9,50|M|Ingen|27|18|2005-07-01 00:00:00 + 00:00|2006-06-30 00:00:00 + 00:00|Ingen|b-GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|18f95f47-1540-4e02-8f1f-cc1bcb6828d0|2008-03-11 |10:01:36.827000 + 00:00|

```
df = dataflow.to_pandas_dataframe()
df.dtypes
```
```
ProductID                                     int64
Name                                         object
ProductNumber                                object
Color                                        object
StandardCost                                float64
ListPrice                                   float64
Size                                         object
Weight                                      float64
ProductCategoryID                             int64
ProductModelID                                int64
SellStartDate             datetime64[ns, UTC+00:00]
SellEndDate                                  object
DiscontinuedDate                             object
ThumbNailPhoto                               object
ThumbnailPhotoFileName                       object
rowguid                                      object
ModifiedDate              datetime64[ns, UTC+00:00]
dtype: object
```

## <a name="read-from-azure-data-lake-storage"></a>Läsa från Azure Data Lake Storage
Det finns två sätt SDK kan hämta nödvändiga OAuth-token för att komma åt Azure Data Lake Storage:
-   Hämta åtkomsttoken från en senaste inloggningssession av användarens inloggning för Azure CLI
-   Använda ett huvudnamn för tjänsten (SP) och ett certifikat som hemlighet

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>En åtkomsttoken från de senaste Azure CLI-sessionen
Kör följande kommando på den lokala datorn:

> [!NOTE] 
> Om ditt användarkonto är medlem i fler än en Azure-klient, måste du ange klienten i formuläret AAD-URL-värddatornamn.


```
az login
az account show --query tenantId
dataflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dataflow.head(5) head
```
### <a name="create-a-service-principal-with-the-azure-cli"></a>Skapa ett huvudnamn för tjänsten med Azure CLI
Du kan använda Azure CLI för att skapa tjänstens huvudnamn och motsvarande certifikat. Viss tjänstens huvudnamn är konfigurerad som läsare, med dess omfattning som minskat till endast Azure Data Lake Storage-konto 'dpreptestfiles'
```
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```
Det här kommandot genererar den `appId` och sökvägen till certifikatfilen (vanligtvis i arbetsmappen). Filen .crt innehåller både offentliga certifikat och den privata nyckeln i PEM-format.

Extrahera tumavtrycket:
```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

##### <a name="configure-an-azure-data-lake-storage-account-for-the-service-principal"></a>Konfigurera ett Azure Data Lake Storage-konto för tjänstens huvudnamn
Konfigurera ACL för filsystemet Azure Data Lake Storage med objekt-ID för användaren eller för det här exemplet, tjänstens huvudnamn:
```
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```
##### <a name="configure-read-and-execute-access-for-the-azure-data-lake-storage-file-system"></a>Konfigurera Läs- och körningsbehörighet för Azure Data Lake Storage file system.
Eftersom den underliggande HDFS ACL-modellen inte stöder arv, måste du konfigurera ACL för mappar och filer individuellt.
```
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
#### <a name="acquire-an-oauth-access-token"></a>Hämta en OAuth-åtkomsttoken
Använd den `adal` paketet (via: `pip install adal`) att skapa en autentiseringskontext på MSFT-klient och få en OAuth-åtkomsttoken. För ADLS, resursen i tokenbegäran måste vara för 'https://datalake.azure.net', som skiljer sig från de flesta andra Azure-resurser.

```
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
|4|1010691|10th Steet Community bönder marknaden|http://agrimissouri.com/mo-grown/grodetail.php... |10 gata och poppel|Lamar|Barton|

