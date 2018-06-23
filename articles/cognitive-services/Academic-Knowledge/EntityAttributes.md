---
title: Academic diagrammet entitetsattribut för Academic Knowledge API | Microsoft Docs
description: Mer information om enhetens attribut som du kan använda med Academic diagrammet i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: f771969c3c6f05e5d2c99b1fbf593ae039ccb12c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351348"
---
# <a name="entity-attributes"></a>Entitetsattribut

Academic diagrammet består av 7 typer av entiteten. Alla enheter har en enhets-ID och en entitetstyp.

## <a name="common-entity-attributes"></a>Vanliga entitetsattribut
Namn    |Beskrivning                |Typ       | Åtgärder
------- | ------------------------- | --------- | ----------------------------
Id      |Enhets-id                  |Int64      |Lika med
Ty      |Entitetstyp                |Enum   |Lika med

## <a name="entity-type-enum"></a>Entiteten typen uppräkning
Namn                                                            |värde
----------------------------------------------------------------|-----
[Artikel](PaperEntityAttributes.md)                               |0
[Författare](AuthorEntityAttributes.md)                             |1
[Tidskrift](JournalEntityAttributes.md)                           |2
[Konferens serien](JournalEntityAttributes.md)                 |3
[Konferens instans](ConferenceInstanceEntityAttributes.md)    |4
[Anknytning](AffiliationEntityAttributes.md)                   |5
[Utbildning](FieldsOfStudyEntityAttributes.md)                      |6

