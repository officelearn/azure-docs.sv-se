---
title: Expandera JSON omvandlingen med Azure Machine Learning arbetsstationen
description: "Referensdokumentet för Expandera JSON-transformeringen"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 21de94d2d0d3cc12aabcb8e9e8b0eec39b0a2710
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="expand-json-transformation"></a>Expandera JSON omvandling
Den **Expandera JSON** transformeringen kan du expandera en befintlig kolumn som innehåller giltig JSON-texten till flera kolumner.

## <a name="how-to-perform-this-transformation"></a>Hur du utför den här omvandlingen

Följ dessa steg om du vill utföra den här transformeringen:
1. Välj kolumnen källa som innehåller JSON-texten.
2. Välj **Expandera JSON** från den **transformerar** menyn. Eller högerklicka på rubriken för kolumnen källa och välj **Expandera JSON** på snabbmenyn. 
3. Klicka på **OK**. 
 
Nya kolumner läggs till bredvid källkolumnen. Dessa kolumner innehåller egenskaper från nästa nivå i hierarkin i JSON-texten. Egenskapnyckeln används om den finns, för att skapa namnet på motsvarande kolumn. Kapslade egenskaper sparas som JSON-texten användaren upprepade gånger kan utöka eller ta bort efter behov.

## <a name="examples"></a>Exempel

Källan columnn *kunden* utökats till två kolumner *Customer.Name* och *Customer.Phone*.

| Kund                                                | Customer.Name   | Customer.Phone |
|---------------------------------------------------------|-----------------|----------------|
| {”Name”: ”Carrie Dodson”, ”Phone”: ”123-4567-890”}   | Carrie Dodson   | 123-4567-890   |
| {”Name”: ”Leonard Robledo”, ”Phone”: ”123-456-7890”} | Leonard Robledo | 456-7890-123   |

