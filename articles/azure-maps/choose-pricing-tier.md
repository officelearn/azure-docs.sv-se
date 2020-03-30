---
title: Välj rätt prisnivå | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om prisnivåer som erbjuds av Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: a8bf7ff9bacd4fe84ee5b64d0aed5cb271ce06f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335666"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Välj rätt prisnivå i Azure Maps

Azure Maps erbjuder två prisnivåer, S0 och S1. Syftet med den här artikeln är att hjälpa dig att välja rätt prisnivå för dina behov. Om du vill välja rätt prisnivå ställer du dig följande två frågor.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Hur många samtidiga användare planerar jag att stödja? 
Prisnivåerna S0 och S1 hanterar olika mängder datadataflöde. Prisnivån S0 hanterar upp till **50 frågor per sekund.** S1-nivån hanterar **mer än 50 frågor per sekund.**

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Vilka geospatiala funktioner planerar jag att använda?
S0-prisnivån är rätt för dig om de grundläggande geospatiala API:erna uppfyller dina servicekrav. Om du vill ha mer avancerade funktioner för ditt program kan du överväga att välja prisnivån S1. Avancerade funktioner inkluderar: Aerial plus hybrid bilder, få rutt räckvidd och batch geokodning. Granska tabellen **prisnivåfunktioner** för att välja den prisnivå som passar bäst för ditt program.

### <a name="pricing-tier-capabilities"></a>Funktioner på prisnivå

| Funktion                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Återgivning av karta                              | ✓                   | ✓       |
| Satellitbilder                       |                     | ✓        |
| Search                                  | ✓                    | ✓        |
| Batch Sökning                            |                     | ✓        |
| Routa                                   | ✓                    |✓        |
| Batch-routning                            |                    | ✓        |
| Matrisdirigering                          |                     | ✓        |
| Ruttområde (Isochrones)                |                     | ✓        |
| Trafik                                |✓                    |✓        |
| Tidszon                               |✓                    |✓        |
| Geolokalisering (förhandsgranskning)                    |✓                   |✓        |
| Rumsliga operationer                        |                    |✓        |
| Geofencing (geofencing)                                |                    |✓        |
| Azure Maps-data (förhandsversion)                |                     | ✓        |
| Mobilitet (förhandsgranskning)                       |                     | ✓        |
| Väder (förhandsgranskning)                        |✓                    |✓        |

Tänk på dessa ytterligare punkter:
* Vilken typ av företag har du?
* Hur viktigt är ditt program?

### <a name="pricing-tier-targeted-customers"></a>Prisnivå riktade kunder

Se **prisnivån riktade kunder** tabellen för att få en bättre känsla för S0 och S1 prisnivåer. Mer information finns i [Azure Maps priser](https://azure.microsoft.com/pricing/details/azure-maps/). 

| Prisnivå  |     Riktade kunder                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>S0-prisnivån fungerar för program i alla produktionsstadier: från proof-of-concept-utveckling och testning i ett tidigt skede till programproduktion och distribution. Den här nivån är dock utformad för småskalig utveckling eller kunder med låga samtidiga användare, eller båda. <p>|
| S1            |    <p>Prisnivån S1 är för kunder med storskaliga företagsprogram, verksamhetskritiska program eller stora volymer samtidiga användare. Det är också för de kunder som behöver avancerade geospatiala tjänster.</p>|

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du visar och ändrar prisnivåer:

> [!div class="nextstepaction"] 
> [Hantera en prisnivå](how-to-manage-pricing-tier.md)
