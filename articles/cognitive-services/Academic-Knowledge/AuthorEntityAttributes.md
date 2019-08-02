---
title: Redigera entitets-attribut – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Lär dig de attribut som du kan använda med entiteten Author i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: e63e9d3f8f17a2473caf3d31b83e318ddb132b43
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705091"
---
# <a name="author-entity"></a>Redigera entitet
<sub>* Följande attribut är speciella för Author-entiteten. (Ty = ' 1 ')</sub>

Namn    |Beskrivning                            |type       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
ID      |Enhets-id                              |Int64      |Lika med
AuN     |Redigera normaliserat namn                 |Sträng     |Lika med
DAuN    |Författarens visnings namn                    |Sträng     |inga
Kopia      |Redigera totalt antal käll hänvisningar            |Int32      |inga  
ECC     |Författare totalt antal uppskattat antal hänvisningar  |Int32      |inga
E       |Utökade metadata (se tabellen utökade meta-attribut)  |Sträng     |inga  


## <a name="extended-metadata-attributes"></a>Utökade metadata-attribut ##

Namn    | Beskrivning               
--------|---------------------------    
LKA.Afn     | anknytningens visnings namn som är kopplat till författaren  
LKA. AfId        | anknytningens entitets-ID som är kopplat till författaren
