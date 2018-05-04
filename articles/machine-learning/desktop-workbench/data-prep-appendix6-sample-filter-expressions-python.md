---
title: Exempel filteruttryck som är möjligt med Azure Machine Learning förberedelse av data | Microsoft Docs
description: Det här dokumentet innehåller en en uppsättning exempel på filteruttryck som är möjligt med Azure Machine Learning förberedelse av data
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 973c56b8b2821c8e3d63161e6a233243639c74f4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="sample-of-filter-expressions-python"></a>Exempel på filteruttryck (Python) 
Innan du läser den här bilagan läsa [Python utökningsbarhet översikt](data-prep-python-extensibility-overview.md).

## <a name="filter-with-equivalence-test"></a>Filtrera med likvärdiga test
Filtrera i bara de rader där värdet för Col2 (numeriskt) är större än 4. 

```python
    row["Col2"] > 4
```

## <a name="filter-with-multiple-columns"></a>Filtrera med flera kolumner 
Filtrera i bara de rader där Kol1 innehåller värdet **bra** och Col2 innehåller (numeriskt) värdet 1. 
```python
    row["Col1"] == 'Good' and row["Col2"] == 1
```

## <a name="test-filter-against-null"></a>Testa filter mot null
Filtrera i bara de rader där Kol1 har ett null-värde. 
```python
    pd.isnull(row["Col1"])
```
