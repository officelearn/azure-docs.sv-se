---
title: "Exempel transformationer möjligt med Azure Machine Learning förberedelse av Data för transformeringen dataflöde | Microsoft Docs"
description: "Det här dokumentet innehåller en uppsättning exempel på Transformera data flödet transformeringar möjligt med Azure Machine Learning förberedelse av data"
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
ms.date: 09/11/2017
ms.openlocfilehash: 5491548885709c1c1048e45d699ef385a7c49a74
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
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
Autofyll kräver två transformeringar. Vi utgår från data som ser ut som följande:


|Status         |Ort       |
|--------------|-----------|
|Washington    |Redmond    |
|              |Bellevue   |
|              |Issaquah   |
|              |Seattle    |
|Kalifornien    |Los Angeles|
|              |San Diego  |
|              |SAN Jose   |
|Texas         |Dallas     |
|              |SAN Antonio|
|              |Houston    |

Skapa först en transformering av Lägg till kolumn (skript) som innehåller följande kod:
```python
    row['State'] if len(row['State']) > 0 else None
```
Nu skapa transformeringen transformera dataflöde (skript) som innehåller följande kod:
```python
    df = df.fillna( method='pad')
```

Data är nu ser ut ungefär så här:

|Status         |newState         |Ort       |
|--------------|--------------|-----------|
|Washington    |Washington    |Redmond    |
|              |Washington    |Bellevue   |
|              |Washington    |Issaquah   |
|              |Washington    |Seattle    |
|Kalifornien    |Kalifornien    |Los Angeles|
|              |Kalifornien    |San Diego  |
|              |Kalifornien    |SAN Jose   |
|Texas         |Texas         |Dallas     |
|              |Texas         |SAN Antonio|
|              |Texas         |Houston    |

