---
title: Expandera JSON omvandlingen med Azure Machine Learning arbetsstationen
description: "Referensdokumentet för Expandera JSON-transformeringen"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 57f461f9a39e8069654985d86bfe3ae23dd0855a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
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

| Kunden                                                | Customer.Name   | Customer.Phone |
|---------------------------------------------------------|-----------------|----------------|
| {”Name”: ”Carrie Dodson”, ”Phone”: ”123-4567-890”}   | Carrie Dodson   | 123-4567-890   |
| {”Name”: ”Leonard Robledo”, ”Phone”: ”123-456-7890”} | Leonard Robledo | 456-7890-123   |

