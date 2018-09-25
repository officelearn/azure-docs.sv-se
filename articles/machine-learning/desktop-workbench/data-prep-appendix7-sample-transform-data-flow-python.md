---
title: Exempel transformera dataflöde transformationer som är möjligt med Azure Machine Learning databearbetning | Microsoft Docs
description: Det här dokumentet innehåller en uppsättning exempel på transformeringen dataflödestransformeringar möjligt med Azure Machine Learning förberedelse av data
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 82295e412c70065ff8ce3a686aec790614f39f2e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947208"
---
# <a name="sample-of-custom-data-flow-transforms-python"></a>Exempel på anpassade dataflödestransformeringar (Python) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Namnet på transformering i menyn är **transformera dataflöde (skript)**. Innan du läser den här bilagan läsa [Python extensibility översikt](data-prep-python-extensibility-overview.md).

## <a name="transform-frame"></a>Omvandla ram
### <a name="create-a-new-column-dynamically"></a>Skapa en ny kolumn dynamiskt 
Skapar en kolumn dynamiskt (**stad2**) och synkroniserar flera olika versioner av San Francisco till någon från den befintliga kolumnen stad.
```python
    df.loc[(df['city'] == 'San Francisco') | (df['city'] == 'SF') | (df['city'] == 'S.F.') | (df['city'] == 'SAN FRANCISCO'), 'city2'] = 'San Francisco'
```

### <a name="add-new-aggregates"></a>Lägg till nya aggregeringar
Skapar en ny ram med de första och sista aggregeringar som beräknas för resultatkolumnen. Dessa är grupperade efter den **risk_category** kolumn.
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
### <a name="fill-down"></a>Fyll nedåt 

Fyll nedåt kräver två transformeringar. Det förutsätter att data som ser ut som i följande tabell:

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
|              |Houston (USA)    |

1. Skapa en ”Lägg till kolumn (skript)” transformering med följande kod:
```python
    row['State'] if len(row['State']) > 0 else None
```

2. Skapa en transformering i ”transformera dataflöde (skript)” som innehåller följande kod:
```python
    df = df.fillna( method='pad')
```

Data ut så här i följande tabell:

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
|              |Texas         |Houston (USA)    |


### <a name="min-max-normalization"></a>Min – max normalisering
```python
    df["NewCol"] = (df["Col1"]-df["Col1"].mean())/df["Col1"].std()
```