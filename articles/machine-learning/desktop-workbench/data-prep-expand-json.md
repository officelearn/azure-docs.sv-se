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
ROBOTS: NOINDEX
ms.openlocfilehash: 0a5cbca114b220686d656f93edb00a199e3cbeeb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989829"
---
# <a name="expand-json-transformation"></a>Expandera JSON-transformering

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Den **Expandera JSON** transformeringen kan du expandera en befintlig kolumn som innehåller giltig JSON-texten i flera kolumner.

## <a name="how-to-perform-this-transformation"></a>Hur du utför den här omvandlingen

Följ dessa steg om du vill utföra den här transformeringen:
1. Välj kolumnen källa som innehåller JSON-texten.
2. Välj **Expandera JSON** från den **omvandlar** menyn. Eller högerklicka på rubriken för kolumnen källa och välj **Expandera JSON** på snabbmenyn. 
3. Klicka på **OK**. 
 
Nya kolumner läggs till bredvid källkolumnen. Dessa kolumner innehåller egenskaper från nästa nivå i hierarkin i JSON-texten. Egenskapsnyckel, används om det finns för att skapa namnet på motsvarande kolumn. Kapslade egenskaper bevaras som JSON-text som användaren upprepade gånger kan visa eller ta bort efter behov.

## <a name="examples"></a>Exempel

Källkolumnen *kunden* utökas i två kolumner *Customer.Name* och *Customer.Phone*.

| Kund                                                | Customer.Name   | Customer.Phone |
|---------------------------------------------------------|-----------------|----------------|
| {”Name”: ”Carrie Dodson”, ”Phone”: ”123-4567-890”}   | Carrie Dodson   | 123-4567-890   |
| {”Name”: ”Leonard Robledo”, ”Phone”: ”456-7890-123”} | Leonard Robledo | 456-7890-123   |

