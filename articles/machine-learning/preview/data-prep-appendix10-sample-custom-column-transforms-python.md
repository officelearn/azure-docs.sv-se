---
title: "Exempel på Python för härledning av nya kolumner i Azure Machine Learning förberedelse av data | Microsoft Docs"
description: "Det här dokumentet innehåller Python-kodexempel för att skapa nya kolumner i Azure Machine Learning förberedelse av data."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: e576d44a854159054d4f7886fe7859ae34875c8f
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="sample-of-custom-column-transforms-python"></a>Exempel på anpassade kolumnen transformeringar (Python) 
Namnet på den här transformeringen i menyn är **Add Column (skript)**.

Innan du läser den här bilagan läsa [Python utökningsbarhet översikt](data-prep-python-extensibility-overview.md).

## <a name="test-equivalence-and-replace-values"></a>Testa likvärdiga och ersätta värden 
Om värdet i Kol1 är mindre än 4, måste den nya kolumnen ha ett värde på 1. Om värdet i Kol1 är mer än 4, har värdet 2 i den nya kolumnen. 

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
## <a name="epoch-computation"></a>Epok beräkning 
Antal sekunder sedan Unix-epok (antagande om Kol1 är redan ett datum): 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```

## <a name="hash-a-column-value-into-a-new-column"></a>Hash-värde i kolumnen till en ny kolumn
```python
    import hashlib
    hash(row["MyColumnToHashCol1"])

```




