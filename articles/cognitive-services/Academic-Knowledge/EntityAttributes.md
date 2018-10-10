---
title: Entitetsattribut Academic Graph - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs mer om entitetsattribut som du kan använda med Academic Graph i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: a203fdf6562dabb1b9d6e8ab5bb8f46ff6d5dc27
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902798"
---
# <a name="entity-attributes"></a>Entitetsattribut

Academic graph består av 7 typer av entiteten. Alla entiteter har en entitets-ID och en enhetstyp.

## <a name="common-entity-attributes"></a>Vanliga entitetsattribut
Namn    |Beskrivning                |Typ       | Åtgärder
------- | ------------------------- | --------- | ----------------------------
Id      |Enhets-id                  |Int64      |Lika med
Ty      |Entitetstyp                |Enum   |Lika med

## <a name="entity-type-enum"></a>Entiteten typen enum
Namn                                                            |värde
----------------------------------------------------------------|-----
[Artikel](PaperEntityAttributes.md)                               |0
[Författare](AuthorEntityAttributes.md)                             |1
[Tidskrift](JournalEntityAttributes.md)                           |2
[Konferensserie](JournalEntityAttributes.md)                 |3
[Konferensinstans](ConferenceInstanceEntityAttributes.md)    |4
[Anknytning](AffiliationEntityAttributes.md)                   |5
[Studieinriktning](FieldsOfStudyEntityAttributes.md)                      |6

