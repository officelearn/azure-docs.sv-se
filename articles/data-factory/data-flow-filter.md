---
title: Azure Data Factory mappning Dataomvandling Flow Filter
description: Azure Data Factory mappning Dataomvandling Flow Filter
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 1930ca7406b6ef1b9fd20e1651bc031150dd952e
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729551"
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
