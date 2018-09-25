---
title: Använda Python extensibility med Azure Machine Learning-Dataförberedelser | Microsoft Docs
description: Det här dokumentet innehåller en översikt och några detaljerade exempel på hur du använder Python-kod för att utöka funktionerna i förberedelse av data
services: machine-learning
author: euangMS
ms.author: euang
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ROBOTS: NOINDEX
ms.openlocfilehash: eceeeb30331031c51e5208e301441d17096b4a00
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972988"
---
# <a name="data-preparations-python-extensions"></a>Data förberedelser Python-tillägg

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Som ett sätt att fylla i funktionen glapp mellan de inbyggda funktionerna för innehåller Azure Machine Learning-Dataförberedelser utökningsbarhet på flera nivåer. I det här dokumentet beskriver vi utökningsbarhet via Python-skriptet. 

## <a name="custom-code-steps"></a>Anpassad kod steg 
Dataförberedelser har följande anpassade steg där användare kan skriva kod:

* Lägg till kolumn
* Avancerat filter
* Transformera dataflöde
* Omvandla Partition

## <a name="code-block-types"></a>Typer av kod block 
Vi stöder två typer av kod block för var och en av de här stegen. Först måste stöder vi ett utan Python-uttryck som körs eftersom. Vi stöder dessutom en Python-modul där vi anropar en särskild funktion med en känd signatur i den kod som du anger.

Du kan till exempel lägga till en ny kolumn som beräknar en logg för en annan kolumn på följande två sätt:

Uttryck 

```python    
    math.log(row["Score"])
```

Modul 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


Lägg till kolumn transformering i Modulläge förväntar sig att hitta en funktion som kallas `newvalue` som accepterar en rad-variabel och returnerar värdet för kolumnen. Den här modulen kan innehålla alla antalet Python-kod med andra funktioner, import osv.

Information om varje tilläggspunkt beskrivs i följande avsnitt. 

## <a name="imports"></a>Import 
Om du använder block Uttryckstyp, du kan fortfarande lägga till **importera** -uttryck för att din kod. Alla måste grupperas på de översta raderna i din kod.

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
 
 
Om du använder block Modultyp, kan du följa alla normala Python-regler för att använda den **importera** instruktionen. 

## <a name="default-imports"></a>Standard-import
Följande importer är alltid ingår och kan användas i din kod. Du behöver inte importera dem på nytt. 

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
Om du vill använda ett paket som inte är installerad som standard måste du först installera den i miljöer som Dataförberedelser använder. Den här installationen måste göras både på den lokala datorn och på alla beräkningsmål som du vill köra.

Om du vill installera dina paket i ett beräkningsmål som du behöver ändra filen conda_dependencies.yml finns i mappen aml_config under roten för ditt projekt.

### <a name="windows"></a>Windows 
Hitta appspecifika installationen av Python och dess katalogen för skript för att hitta platsen på Windows. Standardplatsen är:  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts` 

Kör något av följande kommandon: 

`conda install <libraryname>` 

eller 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
Hitta appspecifika installationen av Python och dess katalogen för skript för att hitta platsen på en Mac-dator. Standardplatsen är: 

`/Users/<user>/Library/Caches/AmlWorkbench/Python/bin` 

Kör något av följande kommandon: 

`./conda install <libraryname>`

eller 

`./pip install <libraryname>`

## <a name="use-custom-modules"></a>Använda anpassade moduler
I transformera dataflöde (skript), skriver du följande kod för Python

```python
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction1
df = ExtensionFunction1(df)
```

I Lägg till kolumn (skript), ange koden blocktyp = modulen och Skriv följande kod för Python

```python 
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction2

def newvalue(row):
    return ExtensionFunction2(row)
```
För körning av olika peka kontexter (lokal, Docker, Spark), absolut sökväg till rätt plats. Du kanske vill använda ”os.getcwd() + relativePath” för att hitta den.


## <a name="column-data"></a>Kolumndata 
Kolumndata kan nås från en rad med hjälp av punktnotation eller nyckel / värde-notation. Kolumnnamn med blanksteg eller specialtecken kan inte nås med hjälp av punktnotation. Den `row` variabeln ska alltid definieras i båda lägena för Python-tillägg (modulen och uttryck). 

Exempel 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="add-column"></a>Lägg till kolumn 
### <a name="purpose"></a>Syfte
Lägg till kolumn tilläggspunkt kan du skriva Python för att beräkna en ny kolumn. Koden du skriver har åtkomst till den fullständiga raden. Det måste returnera ett nytt kolumnvärde för varje rad. 

### <a name="how-to-use"></a>Hur du ska använda detta
Du kan lägga till den här tilläggspunkt med hjälp av Lägg till kolumn (skript)-block. Det är tillgängligt på den översta **transformationer** menyn även som på den **kolumnen** snabbmenyn. 

### <a name="syntax"></a>Syntax
Uttryck

```python
    math.log(row["Score"])
```

Modul 

```python
def newvalue(row):  
     return math.log(row["Score"])
```
 

## <a name="advanced-filter"></a>Avancerat filter
### <a name="purpose"></a>Syfte 
Tilläggspunkt Avancerat Filter kan du skriva ett anpassat filter. Du har åtkomst till hela raden och din kod måste returnera True (ta med raden) eller FALSKT (Uteslut raden). 

### <a name="how-to-use"></a>Hur du ska använda detta
Du kan lägga till tillägget nu genom att använda avancerade Filter (skript)-block. Det är tillgängligt på den översta **transformationer** menyn. 

### <a name="syntax"></a>Syntax

Uttryck

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
Tilläggspunkt transformera dataflöde kan du helt Omforma dataflödet. Du har åtkomst till en Pandas-dataframe som innehåller alla kolumner och rader som du bearbetning. Din kod måste returnera en Pandas-dataframe med nya data. 

>[!NOTE]
>I Python är alla data som ska läsas in i minnet i en Pandas-dataframe om det här tillägget används. 
>
>I Spark, alla data som samlas in till en enskild worker-nod. Om data är mycket stora, kan en worker slut på minne. Använd noggrant.

### <a name="how-to-use"></a>Hur du ska använda detta 
Du kan lägga till den här tilläggspunkt med hjälp av blocket transformera dataflöde (skript). Det är tillgängligt på den översta **transformationer** menyn. 
### <a name="syntax"></a>Syntax 

Uttryck

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
  

## <a name="transform-partition"></a>Omvandla Partition  
### <a name="purpose"></a>Syfte 
Omvandla Partition tilläggspunkt låter dig omvandla en partition av dataflödet. Du har åtkomst till en Pandas-dataframe som innehåller alla kolumner och rader för den partitionen. Din kod måste returnera en Pandas-dataframe med nya data. 

>[!NOTE]
>I Python, kan du få med en enda partition eller flera partitioner, beroende på storleken på dina data. I Spark arbetar med en dataram som innehåller data för en partition på en viss arbetsnod. I båda fallen kan du anta att du har åtkomst till hela datauppsättningen. 


### <a name="how-to-use"></a>Hur du ska använda detta
Du kan lägga till den här tilläggspunkt med hjälp av blocket transformera Partition (skript). Det är tillgängligt på den översta **transformationer** menyn. 

### <a name="syntax"></a>Syntax 

Uttryck 

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
### <a name="error-values"></a>Felvärden  
Begreppet felvärden finns i Dataförberedelser. 

Det är möjligt att stöta på felvärden i anpassad Python-kod. De är instanser av en Python-klass som heter `DataPrepError`. Den här klassen omsluter en Python-undantag och har några egenskaper. Egenskaperna innehåller information om fel som uppstod när det ursprungliga värdet bearbetades, samt det ursprungliga värdet. 


### <a name="datapreperror-class-definition"></a>DataPrepError klassdefinitionen
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
Skapandet av en DataPrepError inom ramen för Data förberedelser Python ser vanligtvis ut så här: 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>Hur du ska använda detta 
Det är möjligt när Python körs på en tilläggspunkt för att skapa DataPrepErrors som returnerar värden med metoden för skapande av tidigare. Det är mycket mer troligt att DataPrepErrors uppstår när data har bearbetats på en tilläggspunkt. Anpassad Python-koden måste nu att hantera en DataPrepError som en giltig datatyp.

#### <a name="syntax"></a>Syntax 
Uttryck 
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
