---
title: Azure Data Factory omvandling av data flödes filter
description: Azure Data Factory omvandling av data flödes filter
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 2afe079c346a15ec212664ce022ac5e2926b12d4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387815"
---
# <a name="azure-data-factory-filter-transformation"></a>Transformering av Azure Data Factory-filter



Filtrerings transformeringarna innehåller rad filtrering. Bygg ett uttryck som definierar filter villkoret. Klicka i text rutan för att starta uttrycks verktyget. I uttrycks verktyget skapar du ett filter uttryck som styr vilka rader från den aktuella data strömmen som tillåts passera genom (filter) till nästa omvandling. Tänk på filter omvandlingen som WHERE-satsen i ett SQL-uttryck.

## <a name="filter-on-loan_status-column"></a>Filtrera på loan_status-kolumnen:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Filtrera efter kolumnen år i demonstrations demon för filmer:

```
year > 1980
```

## <a name="next-steps"></a>Nästa steg

Prova en kolumn filtrerings omvandling, [Välj omvandlingen](data-flow-select.md)
