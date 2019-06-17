---
title: Author entity attributes - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs om de attribut som du kan använda med entitet som författare i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: d4b33c06ab023023aadf403cf0ef0b08c2bafc5f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60609746"
---
# <a name="author-entity"></a>Redigera entitet
<sub> * Följande attribut är specifika för författare entitet. (Ty = '1') </sub>

Namn    |Beskrivning                            |Typ       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Entitets-ID                              |Int64      |Lika med
AuN     |Författarens normaliserade namn                 |String     |Lika med
DAuN    |Redigera visningsnamn                    |String     |Ingen
CC      |Antalet för författare totala källhänvisningar            |Int32      |Ingen  
ECC     |Antalet för författare totala uppskattade källhänvisningar  |Int32      |Ingen
E       |Utökade metadata (se tabellen ”utökade metadata attribut”)  |String     |Ingen  


## <a name="extended-metadata-attributes"></a>Utökade attribut Metadata ##

Namn    | Beskrivning               
--------|---------------------------    
LKA.Afn     | anknytnings visningsnamnet som hör till författaren  
LKA.AfId        | anknytnings entitets-ID som är associerade med författaren
