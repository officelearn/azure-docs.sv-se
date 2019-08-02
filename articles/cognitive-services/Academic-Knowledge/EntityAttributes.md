---
title: Akademiska diagram entitets-attribut – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Lär dig mer om entitetens attribut som du kan använda med akademisk graf i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 596ce35a0d744691edd75d6071d4758691e0031b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705023"
---
# <a name="entity-attributes"></a>Entitetsattribut

Den akademiska grafen består av 7 typer av entiteter. Alla entiteter har ett entitets-ID och en entitetstyp.

## <a name="common-entity-attributes"></a>Gemensamma entiteter-attribut
Namn    |Beskrivning                |type       | Åtgärder
------- | ------------------------- | --------- | ----------------------------
ID      |Enhets-id                  |Int64      |Lika med
Ty      |Entitetstyp                |Enum   |Lika med

## <a name="entity-type-enum"></a>Enum för entitetstyp
Namn                                                            |value
----------------------------------------------------------------|-----
[Artikel](PaperEntityAttributes.md)                               |0
[Författare](AuthorEntityAttributes.md)                             |1
[Tidskrift](JournalEntityAttributes.md)                           |2
[Konferens serier](JournalEntityAttributes.md)                 |3
[Konferens instans](ConferenceInstanceEntityAttributes.md)    |4
[Anknytning](AffiliationEntityAttributes.md)                   |5
[Utbildnings fält](FieldsOfStudyEntityAttributes.md)                      |6

