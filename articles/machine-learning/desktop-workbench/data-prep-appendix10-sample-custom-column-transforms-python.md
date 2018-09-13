---
title: Exempel på Python för att få fram nya kolumner i förberedelse av data i Azure Machine Learning | Microsoft Docs
description: Det här dokumentet innehåller Python-kodexempel för att skapa nya kolumner i Azure Machine Learning förberedelse av data.
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
ms.openlocfilehash: d9f8bb20325ff15b6ba67253de5e66d1d1d8e643
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35647825"
---
# <a name="sample-of-custom-column-transforms-python"></a>Exempel på anpassade kolumntransformeringar (Python) 
Namnet på den här transformeringen i menyn är **Lägg till kolumn (skript)**.

Innan du läser den här bilagan läsa [Python extensibility översikt](data-prep-python-extensibility-overview.md).

## <a name="test-equivalence-and-replace-values"></a>Testa likvärdighet och Ersätt värden 
Om värdet i Kol1 är mindre än 4, bör den nya kolumnen har värdet 1. Om värdet i Kol1 är mer än 4, har den nya kolumnen värdet 2. 

```python
    1 if row["Col1"] < 4 else 2
```
## <a name="current-date-and-time"></a>Aktuellt datum och tid 

```python
    datetime.datetime.now()
```
## <a name="typecasting"></a>Typecasting 
```python
    float(row["Col1"]) / float(row["Col2"] - 1)
```
## <a name="evaluate-for-nullness"></a>Utvärdera för nullness 
Om Kol1 innehåller ett null-värde, och markera sedan den nya kolumnen som **felaktig**. Om inte, markera den som **bra.** 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>Ny beräknad kolumn 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>Epoch beräkning 
Antal sekunder sedan Unix-Epoch (förutsatt att Kol1 är redan ett datum): 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```

## <a name="hash-a-column-value-into-a-new-column"></a>Hash-kolumnvärdet för en till en ny kolumn
```python
    import hashlib
    hash(row["MyColumnToHashCol1"])

```




