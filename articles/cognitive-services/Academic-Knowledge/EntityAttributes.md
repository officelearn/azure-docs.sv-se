---
title: Academic Graph entity attributes - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs mer om entitetsattribut som du kan använda med Academic Graph i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 485775660ecfdf2291365ab98c9188295ea2cbde
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61340187"
---
# <a name="entity-attributes"></a>Entitetsattribut

Academic graph består av 7 typer av entiteten. Alla entiteter har en entitets-ID och en entitetstyp.

## <a name="common-entity-attributes"></a>Vanliga entitetsattribut
Namn    |Beskrivning                |Typ       | Åtgärder
------- | ------------------------- | --------- | ----------------------------
Id      |Enhets-id                  |Int64      |Lika med
Ty      |Entitetstyp                |Enum   |Lika med

## <a name="entity-type-enum"></a>Entiteten typen enum
Namn                                                            |value
----------------------------------------------------------------|-----
[Artikel](PaperEntityAttributes.md)                               |0
[Författare](AuthorEntityAttributes.md)                             |1
[Tidskrift](JournalEntityAttributes.md)                           |2
[Konferensserie](JournalEntityAttributes.md)                 |3
[Konferensinstans](ConferenceInstanceEntityAttributes.md)    |4
[Anknytning](AffiliationEntityAttributes.md)                   |5
[Studieinriktning](FieldsOfStudyEntityAttributes.md)                      |6

