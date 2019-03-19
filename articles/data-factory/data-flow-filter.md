---
title: Azure Data Factory mappning Dataomvandling Flow Filter
description: Azure Data Factory mappning Dataomvandling Flow Filter
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: d1751c47ad4507260d9f8d6ea44fcb32ed0e7338
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57864736"
---
# <a name="azure-data-factoryfilter-transformation"></a>Azure FactoryFilter Dataomvandling

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Filter-transformeringar ger filtrering av raden. Skapa ett uttryck som definierar filtervillkoret. Klicka i textrutan för att starta Uttrycksverktyget. Skapa ett filteruttryck för att begränsa vilka rader från aktuell dataströmmen kan passera (filter) till nästa transformeringen i Uttrycksverktyget. Tänk på Filter-transformeringen som WHERE-satsen för en SQL-instruktion.

## <a name="filter-on-loanstatus-column"></a>Filtrera på loan_status kolumnen:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Filtrera efter varje kolumn i filmer demo:

```
year > 1980
```

## <a name="next-steps"></a>Nästa steg

Prova en kolumn som filtrering omvandling, den [Välj omvandling](data-flow-select.md)
