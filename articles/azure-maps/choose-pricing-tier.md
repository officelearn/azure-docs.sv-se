---
title: Välj rätt pris nivå | Microsoft Azure Maps
description: I den här artikeln får du lära dig om pris nivåer som erbjuds av Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 8764e9161f952118ca7ae28343dcd16477cf1eee
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155765"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Välj rätt pris nivå i Azure Maps

Azure Maps erbjuder två pris nivåer, S0 och S1. Syftet med den här artikeln är att hjälpa dig att välja rätt pris nivå för dina behov. Du kan välja rätt pris nivå genom att ställa följande två frågor.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Vilka geospatiala funktioner planerar jag att använda?
Pris nivån för S0 är rätt för dig om de grundläggande geospatiala API: erna uppfyller tjänst kraven. Om du vill ha mer avancerade funktioner för ditt program bör du tänka på väljer för pris nivån S1. Exempel på avancerade funktioner: flyg-plus hybrid bilder, hämtning av väg intervall och batch-kod. Tabellen med **pris nivå funktioner** kan hjälpa dig att välja en pris nivå som passar bäst för ditt program.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Hur många samtidiga användare planerar jag att stödja? 
Pris nivåerna S0 och S1 hanterar olika mängder data genom strömning. Pris nivån S0 hanterar upp till **50 frågor per sekund**, medan S1-nivån hanterar **fler än 50 frågor per sekund**.

### <a name="pricing-tier-capabilities"></a>Pris nivå funktioner

| Kapacitet                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Mappa åter givning                              | ✓                   | ✓       |
| Satellit bilder                       |                     | ✓        |
| Sök                                  | ✓                    | ✓        |
| Batch Search                            |                     | ✓        |
| Routa                                   | ✓                    |✓        |
| Batch Routing                            |                    | ✓        |
| Matrix Routing                          |                     | ✓        |
| Route Range (Isochrones)                |                     | ✓        |
| Trafik                                |✓                    |✓        |
| Tidszon                               |✓                    |✓        |
| Geolokalisering (förhands granskning)                    |✓                   |✓        |
| Spatial Operations                        |                    |✓        |
| Geofencing                                |                    |✓        |
| Azure Maps data (förhands granskning)                |                     | ✓        |
| Mobilitet (för hands version)                       |                     | ✓        |
| Väder (för hands version)                        |✓                    |✓        |

Dessa ytterligare data punkter är värda att tänka på:
* Vilken typ av företag har du?
* Hur kritiskt är ditt program?

### <a name="pricing-tier-targeted-customers"></a>Pris nivå riktade kunder

Se **pris nivån riktade kunder** för att få en bättre uppfattning om pris nivåerna S0 och S1. Mer information finns i [Azure Maps prissättning](https://azure.microsoft.com/pricing/details/azure-maps/). 

| Prisnivå  |     Riktad kund                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Pris nivån S0 används för program i alla stadier av produktion: från koncept koncept utveckling och tidigt test till program produktion och distribution. Den här nivån är dock utformad för storskalig utveckling eller kunder med låga samtidiga användare eller både och. <p>|
| S1            |    <p>Pris nivån S1 är för kunder som behöver stöd för storskaliga företags-, verksamhets kritiska program eller stora mängder av samtidiga användare. Det är också för de kunder som behöver avancerade geospatiala tjänster.</p>|

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att visa och ändra pris nivåer:

> [!div class="nextstepaction"] 
> [Hantera en pris nivå](how-to-manage-pricing-tier.md)
