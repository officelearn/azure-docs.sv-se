---
title: Azure Data Factory mappning Dataomvandling Flow Filter
description: Azure Data Factory mappning Dataomvandling Flow Filter
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b7e7b123560aae3a2d3086c8536969297d31f7ba
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271998"
---
# <a name="azure-data-factory-mapping-data-flow-filter-transformation"></a>Azure Data Factory mappning Dataomvandling Flow Filter

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Filter-transformeringar ger filtrering av raden. Skapa ett uttryck som definierar filtervillkoret. Klicka i textrutan för att starta Uttrycksverktyget. Skapa ett filteruttryck för att begränsa vilka rader från aktuell dataströmmen kan passera (filter) till nästa transformeringen i Uttrycksverktyget.

Dvs. filtrera på loan_status kolumnen:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Filtrera efter varje kolumn i filmer demo:

```
year > 1980
```
