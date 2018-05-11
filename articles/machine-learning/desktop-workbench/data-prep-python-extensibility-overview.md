---
title: Använda Python utökningsbarhet med Azure Machine Learning Data förberedelser | Microsoft Docs
description: Det här dokumentet innehåller en översikt och detaljerad exempel på hur du använder Python-kod för att utöka funktionerna i förberedelse av data
services: machine-learning
author: euangMS
ms.author: euang
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ms.openlocfilehash: 6363d39b2dfbd36ccebff6780e35caf58ca84dda
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="data-preparations-python-extensions"></a>Datatillägg för förberedelser Python
Förberedelser för Azure Machine Learning Data innehåller utökningsbarhet på flera nivåer som ett sätt att fylla i funktionen glapp mellan inbyggda funktioner. I det här dokumentet beskriver vi utökningsbarhet via Python-skriptet. 

## <a name="custom-code-steps"></a>Anpassad kod steg 
Data förberedelser har följande anpassade steg där användare kan skriva kod:

* Lägg till kolumn
* Avancerat filter
* Transformera dataflöde
* Transformera Partition

## <a name="code-block-types"></a>Typer av kod block 
Vi stöder två typer av kod block för var och en av de här stegen. Vi stöder först ett bare Python-uttryck som körs eftersom. Vi stöder andra, en Python-modulen där vi kallar en särskild funktion med en känd signatur i den kod som du anger.

Du kan till exempel lägga till en ny kolumn som beräknar en logg för en annan kolumn i följande två sätt:

Uttryck 

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

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts` 

Kör något av följande kommandon: 

`conda install <libraryname>` 

eller 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
Hitta app-specifik installation av Python och dess skriptkatalogen för att hitta platsen på en Mac. Standardplatsen är: 

`/Users/<user>/Library/Caches/AmlWorkbench/Python/bin` 

Kör något av följande kommandon: 

`./conda install <libraryname>`

eller 

`./pip install <libraryname>`

## <a name="use-custom-modules"></a>Använda anpassade moduler
I transformera dataflöde (skript), skriver du följande Python-kod

```python
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction1
df = ExtensionFunction1(df)
```

I Lägg till kolumn (skript), och ange koden Block = modulen och skriva följande kod för Python

```python 
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction2

def newvalue(row):
    return ExtensionFunction2(row)
```
För att köra en olika peka kontexter (lokal, Docker, Spark), absolut sökväg på rätt plats. Du kanske vill använda ”os.getcwd() + relativePath” för att hitta den.


## <a name="column-data"></a>Kolumndata 
Kolumndata kan nås från en rad med hjälp av punktnotation eller nyckel / värde-notation. Kolumnnamn som innehåller blanksteg eller specialtecken kan inte nås med hjälp av punktnotation. Den `row` variabeln ska alltid vara definierat i båda lägena av Python-tillägg (modul och uttryck). 

Exempel 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="add-column"></a>Lägg till kolumn 
### <a name="purpose"></a>Syfte
Lägg till kolumn tillägget punkten kan du skriva Python för att beräkna en ny kolumn. Koden du skriver har åtkomst till den fullständiga raden. Det måste returnera ett nytt kolumnvärde för varje rad. 

### <a name="how-to-use"></a>Hur du ska använda detta
Du kan lägga till tillägget nu med hjälp av Lägg till kolumn (skript)-block. Det är tillgängligt på den översta **transformationer** menyn, såväl som på den **kolumnen** snabbmenyn. 

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
Avancerade Filter tillägget punkten kan du skriva ett anpassat filter. Du har åtkomst till hela raden och din kod måste returnera True (inklusive raden) eller FALSKT (exkludera raden). 

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
Transformera dataflöde tillägget punkten kan du omvandla helt dataflödet. Du har åtkomst till en Pandas dataframe som innehåller alla kolumner och rader som du bearbeta. Din kod måste returnera en Pandas dataframe med de nya data. 

>[!NOTE]
>I Python är alla data som ska läsas in i minnet i en Pandas dataframe om det här filtillägget används. 
>
>Alla data samlas till en enda arbetsnod i Spark. Om data är väldigt stor kan en arbetare kör slut på minne. Använd noggrant.

### <a name="how-to-use"></a>Hur du ska använda detta 
Du kan lägga till tillägget nu med hjälp av den transformera (skript) dataströmsblocket. Det är tillgängligt på den översta **transformationer** menyn. 
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
  

## <a name="transform-partition"></a>Transformera Partition  
### <a name="purpose"></a>Syfte 
Transformera Partition tillägget punkten kan du omvandla en partition av dataflödet. Du har åtkomst till en Pandas dataframe som innehåller alla kolumner och rader för den aktuella partitionen. Din kod måste returnera en Pandas dataframe med de nya data. 

>[!NOTE]
>I Python, kan du få en enda partition eller flera partitioner, beroende på storleken på dina data. I Spark arbetar med en dataframe som innehåller data för en partition på en viss arbetsnoden. I båda fallen kan du anta att du har åtkomst till hela datauppsättningen. 


### <a name="how-to-use"></a>Hur du ska använda detta
Du kan lägga till tillägget nu med hjälp av blocket transformera Partition (skript). Det är tillgängligt på den översta **transformationer** menyn. 

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
