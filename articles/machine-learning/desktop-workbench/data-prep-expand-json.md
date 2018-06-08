---
title: Expandera JSON omvandlingen med Azure Machine Learning arbetsstationen
description: Referensdokumentet för Expandera JSON-transformeringen
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: fa2a8710f4dc12fab1efe34aa11398b937878692
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831759"
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

