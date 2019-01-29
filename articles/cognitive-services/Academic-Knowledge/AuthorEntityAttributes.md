---
title: Author entity attributes - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs om de attribut som du kan använda med entitet som författare i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 48758ac9ec8c993bbdb490229ae20fcce1fb0a49
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175187"
---
# <a name="author-entity"></a>Redigera entitet
<sub> * Följande attribut är specifika för författare entitet. (Ty = '1') </sub>

Name    |Beskrivning                            |Type       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Enhets-id                              |Int64      |Lika med
AuN     |Författarens normaliserade namn                 |Sträng     |Lika med
DAuN    |Redigera visningsnamn                    |Sträng     |inga
Kopia      |Antalet för författare totala källhänvisningar            |Int32      |inga  
ECC     |Antalet för författare totala uppskattade källhänvisningar  |Int32      |inga
E       |Utökade metadata (se tabellen ”utökade metadata attribut”)  |Sträng     |inga  


## <a name="extended-metadata-attributes"></a>Utökade attribut Metadata ##

Name    | Beskrivning               
--------|---------------------------    
LKA.Afn     | anknytnings visningsnamnet som hör till författaren  
LKA.AfId        | anknytnings entitets-ID som är associerade med författaren
