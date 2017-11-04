---
title: "Använda Python utökningsbarhet med Azure Machine Learning Data förberedelser | Microsoft Docs"
description: "Det här dokumentet innehåller en översikt och detaljerad exempel på hur du använder Python-kod för att utöka funktionerna i förberedelse av data"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 53771c407fedc53f27a38ec3fe9b381d6b8c0dad
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2017
---
# <a name="data-preparations-python-extensions"></a>Datatillägg för förberedelser Python
Förberedelser för Azure Machine Learning Data innehåller utökningsbarhet på flera nivåer som ett sätt att fylla i funktionen glapp mellan inbyggda funktioner. I det här dokumentet beskriver vi utökningsbarhet via Python-skriptet. 

## <a name="custom-code-steps"></a>Anpassad kod steg 
Data förberedelser har följande anpassade steg där användare kan skriva kod:

* Filen Reader *
* Skrivaren *
* Lägg till kolumn
* Avancerade Filter
* Transformera dataflöde
* Transformera Partition

* De här stegen stöds inte för närvarande i ett Spark-körning.

## <a name="code-block-types"></a>Typer av kod block 
Vi stöder två typer av kod block för var och en av de här stegen. Vi stöder först ett bare Python-uttryck som körs eftersom. Vi stöder andra, en Python-modulen där vi kallar en särskild funktion med en känd signatur i den kod som du anger.

Du kan till exempel lägga till en ny kolumn som beräknar en logg för en annan kolumn i följande två sätt:

uttryck 

```python    
    math.log(row["Score"])
```

Modul 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


Lägg till kolumn transformeringen i Modulläge förväntar att söka efter en funktion som kallas `newvalue` som accepterar en rad variabel och returnerar värdet för kolumnen. Den här modulen kan innehålla av Python-kod med andra funktioner, importerar och så vidare.

Information om varje tillägg punkt beskrivs i följande avsnitt. 

## <a name="imports"></a>Import 
Om du använder blocktyp uttryck, du kan fortfarande lägga till **importera** instruktioner i din kod. Alla måste grupperas på översta raderna i din kod.

Korrigera 

```python
import math 
import numpy 
math.log(row["Score"])
```
 

Fel  

```python
import math  
math.log(row["Score"])  
import numpy
```
 
 
Om du använder block modultypen, du kan följa alla normala Python-regler för att använda den **importera** instruktionen. 

## <a name="default-imports"></a>Standard-import
Följande importer är alltid inkluderade och kan användas i din kod. Du behöver inte importera dem på nytt. 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="install-new-packages"></a>Installera nya paket
Om du vill använda ett paket som inte är installerad som standard måste du först installera i miljöer som använder Data förberedelser. Den här installationen måste göras både på den lokala datorn och på eventuella beräknings-mål som du vill köra på.

Om du vill installera dina paket i ett beräknings-mål som du behöver ändra filen conda_dependencies.yml finns i mappen aml_config under roten för ditt projekt.

### <a name="windows"></a>Windows 
Hitta app-specifik installation av Python och dess skriptkatalogen för att hitta en plats i Windows. Standardplatsen är:  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts.` 

Kör något av följande kommandon: 

`conda install <libraryname>` 

eller 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
Hitta app-specifik installation av Python och dess skriptkatalogen för att hitta platsen på en Mac. Standardplatsen är: 

`/Users/<user>/Library/Caches/AmlWorkbench>/Python/bin` 

Kör något av följande kommandon: 

`./conda install <libraryname>`

eller 

`./pip install <libraryname>`

## <a name="column-data"></a>Kolumndata 
Kolumndata kan nås från en rad med hjälp av punktnotation eller nyckel / värde-notation. Kolumnnamn som innehåller blanksteg eller specialtecken kan inte nås med hjälp av punktnotation. Den `row` variabeln ska alltid vara definierat i båda lägena av Python-tillägg (modul och uttryck). 

Exempel 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="file-reader"></a>Filen läsare 
### <a name="purpose"></a>Syfte 
Filen Reader tillägget punkten kan du helt kontrollen processen med att läsa en fil i ett dataflöde. Systemet anropar koden och vidarebefordrar i listan över filer som du ska bearbeta. Din kod måste skapas och returnera ett Pandas dataframe. 

>[!NOTE]
>Tillägget nu fungerar inte i Spark. 


### <a name="how-to-use"></a>Hur du ska använda detta 
Du har åtkomst till det här tillägget punkt från den **Öppna datakälla** guiden. Välj **filen** på första sidan och välj sedan din plats. På den **Välj parametrar** sidan den **filtyp** listrutan Välj **anpassad fil (skript)**. 

Koden får en Pandas dataframe med namnet ”df” som innehåller information om filerna du behöver läsa. Om du väljer att öppna en katalog som innehåller flera filer innehåller i dataframe mer än en rad.  

Den här dataframe har följande kolumner:

- Sökväg: Filen som ska läsas.
- PathHint: Anger där filen finns. Värden: Lokal AzureBlobStorage och AzureDataLakeStorage.
- AuthenticationType: Typ av autentisering som används för att komma åt filen. Värden: None, SasToken och OAuthToken.
- AuthenticationValue: Innehåller inget eller token som ska användas.

### <a name="syntax"></a>Syntax 
uttryck 

```python
    paths = df['Path'].tolist()  
    df = pd.read_csv(paths[0])
```


Modul  
```python
PathHint = Local  
def read(df):  
    paths = df['Path'].tolist()  
    filedf = pd.read_csv(paths[0])  
    return filedf  
```
 

## <a name="writer"></a>Skrivare 
### <a name="purpose"></a>Syfte 
Skrivaren tillägget punkten kan du helt kontroll av skrivning av data från ett dataflöde. Systemet anropar koden och vidarebefordrar i en dataframe. Koden kan använda dataframe för att skriva data du vill. 

>[!NOTE]
>Skrivaren tillägget plats fungerar inte i Spark.


### <a name="how-to-use"></a>Hur du ska använda detta 
Du kan lägga till tillägget nu med de skriva (skript) dataströmsblocket. Det är tillgängligt på den översta **transformationer** menyn.

### <a name="syntax"></a>Syntax 
uttryck

```python
    df.to_csv('c:\\temp\\output.csv')
```

Modul

```python
def write(df):  
    df.to_csv('c:\\temp\\output.csv')  
    return df
```
 
 
Den här anpassade write block kan finnas mitt i en lista över steg. Om du använder en modul, måste skriv-funktionen returnera dataframe som indata till det steg som följer. 

## <a name="add-column"></a>Lägg till kolumn 
### <a name="purpose"></a>Syfte
Lägg till kolumn tillägget punkten kan du skriva Python för att beräkna en ny kolumn. Koden du skriver har åtkomst till den fullständiga raden. Det måste returnera ett nytt kolumnvärde för varje rad. 

### <a name="how-to-use"></a>Hur du ska använda detta
Du kan lägga till tillägget nu med hjälp av Lägg till kolumn (skript)-block. Det är tillgängligt på den översta **transformationer** menyn, såväl som på den **kolumnen** snabbmenyn. 

### <a name="syntax"></a>Syntax
uttryck

```python
    math.log(row["Score"])
```

Modul 

```python
def newvalue(row):  
     return math.log(row["Score"])
```
 

## <a name="advanced-filter"></a>Avancerade Filter
### <a name="purpose"></a>Syfte 
Avancerade Filter tillägget punkten kan du skriva ett anpassat filter. Du har åtkomst till hela raden och din kod måste returnera True (inklusive raden) eller FALSKT (exkludera raden). 

### <a name="how-to-use"></a>Hur du ska använda detta
Du kan lägga till tillägget nu genom att använda avancerade Filter (skript)-block. Det är tillgängligt på den översta **transformationer** menyn. 

### <a name="syntax"></a>Syntax

uttryck

```python
    row["Score"] > 95
```

Modul  

```python
def includerow(row):  
    return row["Score"] > 95
```
 

## <a name="transform-dataflow"></a>Transformera dataflöde
### <a name="purpose"></a>Syfte 
Transformera dataflöde tillägget punkten kan du omvandla helt dataflödet. Du har åtkomst till en Pandas dataframe som innehåller alla kolumner och rader som du bearbeta. Din kod måste returnera en Pandas dataframe med de nya data. 

>[!NOTE]
>I Python är alla data som ska läsas in i minnet i en Pandas dataframe om det här filtillägget används. 
>
>Alla data samlas till en enda arbetsnod i Spark. Om data är väldigt stor kan en arbetare kör slut på minne. Använd noggrant.

### <a name="how-to-use"></a>Hur du ska använda detta 
Du kan lägga till tillägget nu med hjälp av den transformera (skript) dataströmsblocket. Det är tillgängligt på den översta **transformationer** menyn. 
### <a name="syntax"></a>Syntax 

uttryck

```python
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Modul 

```python
def transform(df):  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()  
    return df
```
  

## <a name="transform-partition"></a>Transformera Partition  
### <a name="purpose"></a>Syfte 
Transformera Partition tillägget punkten kan du omvandla en partition av dataflödet. Du har åtkomst till en Pandas dataframe som innehåller alla kolumner och rader för den aktuella partitionen. Din kod måste returnera en Pandas dataframe med de nya data. 

>[!NOTE]
>I Python, kan du få en enda partition eller flera partitioner, beroende på storleken på dina data. I Spark arbetar med en dataframe som innehåller data för en partition på en viss arbetsnoden. I båda fallen kan du anta att du har åtkomst till hela datauppsättningen. 


### <a name="how-to-use"></a>Hur du ska använda detta
Du kan lägga till tillägget nu med hjälp av blocket transformera Partition (skript). Det är tillgängligt på den översta **transformationer** menyn. 

### <a name="syntax"></a>Syntax 

uttryck 

```python
    df['partition-id'] = index  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Modul 

```python
def transform(df, index):
    df['partition-id'] = index
    df['index-column'] = range(1, len(df) + 1)
    df = df.reset_index()
    return df
```


## <a name="datapreperror"></a>DataPrepError  
### <a name="error-values"></a>Felvärdena  
Begreppet felvärdena finns i Data förberedelser. 

Det är möjligt att det uppstår felvärden i anpassad Python-kod. De är instanser av en Python-klass som heter `DataPrepError`. Den här klassen packar ett Python-undantag och har några egenskaper. Egenskaperna innehåller information om felet som uppstod när det ursprungliga värdet bearbetades, samt det ursprungliga värdet. 


### <a name="datapreperror-class-definition"></a>DataPrepError klassdefinitionen
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
Skapandet av en DataPrepError inom ramen för Data förberedelser Python vanligtvis ser ut så här: 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>Hur du ska använda detta 
Det är möjligt när Python körs på ett tillägg peka generera DataPrepErrors som returnerar värden med hjälp av metoden tidigare skapas. Det är mycket troligt att DataPrepErrors uppstår när data bearbetas en gång för tillägget. Anpassad Python-kod måste nu att hantera en DataPrepError som en giltig datatyp.

#### <a name="syntax"></a>Syntax 
uttryck 
```python 
    if (isinstance(row["Score"], DataPrepError)): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
```python 
    if (hasattr(row["Score"], "originalValue")): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
Modul 
```python 
def newvalue(row): 
    if (isinstance(row["Score"], DataPrepError)): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
``` 
```python 
def newvalue(row): 
    if (hasattr(row["Score"], "originalValue")): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
```  
