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
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878943"
---
# <a name="author-entity"></a>Redigera entitet
<sub> * Följande attribut är specifika för författare entitet. (Ty = '1') </sub>

Namn    |Beskrivning                            |Type       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Enhets-id                              |Int64      |Lika med
AuN     |Författarens normaliserade namn                 |String     |Lika med
DAuN    |Redigera visningsnamn                    |String     |inga
Kopia      |Antalet för författare totala källhänvisningar            |Int32      |inga  
ECC     |Antalet för författare totala uppskattade källhänvisningar  |Int32      |inga
E       |Utökade metadata (se tabellen ”utökade metadata attribut”)  |String     |inga  


## <a name="extended-metadata-attributes"></a>Utökade attribut Metadata ##

Namn    | Beskrivning               
--------|---------------------------    
LKA.Afn     | anknytnings visningsnamnet som hör till författaren  
LKA.AfId        | anknytnings entitets-ID som är associerade med författaren
