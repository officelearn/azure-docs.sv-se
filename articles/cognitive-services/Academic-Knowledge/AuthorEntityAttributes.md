---
title: Redigera entitetsattribut – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs om de attribut som du kan använda med entitet som författare i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 62e6da6d558a0494fb83115b1e307081099399d4
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900697"
---
# <a name="author-entity"></a>Redigera entitet
<sub> * Följande attribut är specifika för författare entitet. (Ty = '1') </sub>

Namn    |Beskrivning                            |Typ       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Enhets-id                              |Int64      |Lika med
AuN     |Författarens normaliserade namn                 |Sträng     |Lika med
DAuN    |Redigera visningsnamn                    |Sträng     |inga
Kopia      |Antalet för författare totala källhänvisningar            |Int32      |inga  
ECC     |Antalet för författare totala uppskattade källhänvisningar  |Int32      |inga
E       |Utökade metadata (se tabellen ”utökade metadata attribut”)  |Sträng     |inga  


## <a name="extended-metadata-attributes"></a>Utökade attribut Metadata ##

Namn    | Beskrivning               
--------|---------------------------    
LKA. Afn     | anknytnings visningsnamnet som hör till författaren  
LKA. AfId        | anknytnings entitets-ID som är associerade med författaren