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
ms.openlocfilehash: accc2803895f3892075cdd9877ca98344ab88bd1
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884825"
---
# <a name="entity-attributes"></a>Entitetsattribut

Academic graph består av 7 typer av entiteten. Alla entiteter har en entitets-ID och en enhetstyp.

## <a name="common-entity-attributes"></a>Vanliga entitetsattribut
Namn    |Beskrivning                |Type       | Åtgärder
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

