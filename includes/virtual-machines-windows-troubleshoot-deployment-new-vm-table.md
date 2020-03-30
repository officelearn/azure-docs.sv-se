---
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 14abae6f6f72d724fffb1ccaa12f56fb6976f7a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187343"
---
I följande tabell visas möjliga överförings- och hämtningskombinationer av Windows-generaliserade (gen.) och specialiserade (spec.) OS-avbildningar. De kombinationer som kommer att bearbetas utan några fel indikeras av ett Y, och de som kommer att kasta fel indikeras av ett N. Orsakerna och lösningarna för de olika fel du kommer att stöta på anges under tabellen.

| Operativsystem | Ladda upp spec. | Ladda upp gen. | Fånga spec. | Fånga gen. |
| --- | --- | --- | --- | --- |
| Windows gen. |N<sup>1</sup> |Y |N<sup>3</sup> |Y |
| Windows spec. |Y |N<sup>2</sup> |Y |N<sup>4</sup> |

