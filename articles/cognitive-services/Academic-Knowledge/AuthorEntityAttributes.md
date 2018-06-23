---
title: Redigera entitetsattribut i Academic Knowledge API | Microsoft Docs
description: 'Läs om de attribut som du kan använda med författare entiteten i Academic Knowledge API: et i kognitiva Services.'
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: afcc3257c49522a4631c4aae0e0c2b88373f9af1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351354"
---
# <a name="author-entity"></a>Författare entitet
<sub> * Följande attribut är specifika för författare entitet. (Ty = '1') </sub>

Namn    |Beskrivning                            |Typ       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Enhets-id                              |Int64      |Lika med
AuN     |Författarens normaliserat namn                 |Sträng     |Lika med
DAuN    |Visningsnamn för författare                    |Sträng     |inga
Kopia      |Antalet för författare totala källhänvisningar            |Int32      |inga  
ECC     |Antalet för författare totala uppskattade källhänvisningar  |Int32      |inga
E       |Utökad metadata (se tabell ”utökade Meta attribut”)  |Sträng     |inga  


## <a name="extended-metadata-attributes"></a>Utökade Metadataattribut ##

Namn    | Beskrivning               
--------|---------------------------    
LKA. Afn     | anknytningens visningsnamnet som hör till författaren  
LKA. AfId        | anknytningens enhets-ID som är kopplade till författaren