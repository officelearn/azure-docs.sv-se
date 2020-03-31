---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d93de4ed758afb5e951bb5e19f4f7adb290e461c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187346"
---
I följande tabell visas möjliga uppladdnings- och hämtningskombinationer av Linux-generaliserade och specialiserade OS-avbildningar. De kombinationer som kommer att bearbetas utan några fel indikeras av ett Y, och de som kommer att kasta fel indikeras av ett N. Orsakerna och lösningarna för de olika fel du kommer att stöta på anges under tabellen.

| Operativsystem | Ladda upp spec. | Ladda upp gen. | Fånga spec. | Fånga gen. |
| --- | --- | --- | --- | --- |
| Linux gen. |N<sup>1</sup> |Y |N<sup>3</sup> |Y |
| Linux spec. |Y |N<sup>2</sup> |Y |N<sup>4</sup> |

