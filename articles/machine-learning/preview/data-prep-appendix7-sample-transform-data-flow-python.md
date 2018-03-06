---
title: "Exempel transformationer möjligt med Azure Machine Learning förberedelse av Data för transformeringen dataflöde | Microsoft Docs"
description: "Det här dokumentet innehåller en uppsättning exempel på Transformera data flödet transformeringar möjligt med Azure Machine Learning förberedelse av data"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: aa213a3b1a8949f0fca5e4bbb7ec5a6a775ae6ec
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="sample-of-custom-data-flow-transforms-python"></a>Exempel på anpassade data flödet transformeringar (Python) 
Transformering i menyn heter **transformera dataflöde (skript)**. Innan du läser den här bilagan läsa [Python utökningsbarhet översikt](data-prep-python-extensibility-overview.md).

## <a name="transform-frame"></a>Transformera ram
### <a name="create-a-new-column-dynamically"></a>Skapa en ny kolumn dynamiskt 
Skapar en kolumn dynamiskt (**city2**) och synkroniserar flera olika versioner av San Francisco till en från kolumnen befintliga ort.
```python
    df.loc[(df['city'] == 'San Francisco') | (df['city'] == 'SF') | (df['city'] == 'S.F.') | (df['city'] == 'SAN FRANCISCO'), 'city2'] = 'San Francisco'
```

### <a name="add-new-aggregates"></a>Lägga till nya mängder
Skapar en ny ram med första och sista-mängder som beräknats för resultatkolumnen. Dessa är grupperade efter den **risk_category** kolumn.
```python
    df = df.groupby(['risk_category'])['Score'].agg(['first','last'])
```
### <a name="winsorize-a-column"></a>Winsorize en kolumn 
Formulerar om data för att uppfylla en formel för att minska avvikare i en kolumn.
```python
    import scipy.stats as stats
    df['Last Order'] = stats.mstats.winsorize(df['Last Order'].values, limits=0.4)
```

## <a name="transform-data-flow"></a>Transformera dataflöde
### <a name="fill-down"></a>Autofyll 

Autofyll kräver två transformeringar. Vi utgår från data som ser ut som i följande tabell:

|Status         |Ort       |
|--------------|-----------|
|Washington    |Redmond    |
|              |Bellevue   |
|              |Issaquah   |
|              |Seattle    |
|Kalifornien    |Los Angeles|
|              |San Diego  |
|              |San José   |
|Texas         |Dallas     |
|              |SAN Antonio|
|              |Houston    |

1. Skapa en ”Lägg till kolumn (skript)” transformering med följande kod:
```python
    row['State'] if len(row['State']) > 0 else None
```

2. Skapa en ”transformeringen dataflöde (skript)” transformering som innehåller följande kod:
```python
    df = df.fillna( method='pad')
```

Data är nu ser ut som i följande tabell:

|Status         |newState         |Ort       |
|--------------|--------------|-----------|
|Washington    |Washington    |Redmond    |
|              |Washington    |Bellevue   |
|              |Washington    |Issaquah   |
|              |Washington    |Seattle    |
|Kalifornien    |Kalifornien    |Los Angeles|
|              |Kalifornien    |San Diego  |
|              |Kalifornien    |San José   |
|Texas         |Texas         |Dallas     |
|              |Texas         |SAN Antonio|
|              |Texas         |Houston    |


### <a name="min-max-normalization"></a>Min max normalisering
```python
    df["NewCol"] = (df["Col1"]-df["Col1"].mean())/df["Col1"].std()
```