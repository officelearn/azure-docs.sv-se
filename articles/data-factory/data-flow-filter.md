---
title: Azure Data Factory mappning Dataomvandling Flow Filter
description: Azure Data Factory mappning Dataomvandling Flow Filter
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: e0b41850c149ff7095333cf77b780dec1f03b882
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66234417"
---
# <a name="azure-data-factory-filter-transformation"></a>Azure data factory filter-transformering

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
