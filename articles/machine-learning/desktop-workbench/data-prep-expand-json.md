---
title: Expandera JSON omvandling med hjälp av Azure Machine Learning Workbench
description: Referensdokument för Expandera JSON-transformering
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: dbda4b7b6d82e8cf1e89dc78ce82efbac08b9933
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35650343"
---
# <a name="expand-json-transformation"></a>Expandera JSON-transformering
Den **Expandera JSON** transformeringen kan du expandera en befintlig kolumn som innehåller giltig JSON-texten i flera kolumner.

## <a name="how-to-perform-this-transformation"></a>Hur du utför den här omvandlingen

Följ dessa steg om du vill utföra den här transformeringen:
1. Välj kolumnen källa som innehåller JSON-texten.
2. Välj **Expandera JSON** från den **omvandlar** menyn. Eller högerklicka på rubriken för kolumnen källa och välj **Expandera JSON** på snabbmenyn. 
3. Klicka på **OK**. 
 
Nya kolumner läggs till bredvid källkolumnen. Dessa kolumner innehåller egenskaper från nästa nivå i hierarkin i JSON-texten. Egenskapsnyckel, används om det finns för att skapa namnet på motsvarande kolumn. Kapslade egenskaper bevaras som JSON-text som användaren upprepade gånger kan visa eller ta bort efter behov.

## <a name="examples"></a>Exempel

Käll-columnn *kunden* utökas i två kolumner *Customer.Name* och *Customer.Phone*.

| Kund                                                | Customer.Name   | Customer.Phone |
|---------------------------------------------------------|-----------------|----------------|
| {”Name”: ”Carrie Dodson”, ”Phone”: ”123-4567-890”}   | Carrie Dodson   | 123-4567-890   |
| {”Name”: ”Leonard Robledo”, ”Phone”: ”456-7890-123”} | Leonard Robledo | 456-7890-123   |

