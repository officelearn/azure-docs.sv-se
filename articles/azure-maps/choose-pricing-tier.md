---
title: Välja rätt prisnivå för Azure Maps | Microsoft Docs
description: Lär dig mer om prisnivåer som erbjuds av Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 01/02/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 028a227eefb319d19c9274082626a007bd9161de
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993968"
---
# <a name="choosing-the-right-pricing-tier-in-azure-maps"></a>Välja rätt prisnivå i Azure Maps

Azure Maps erbjuder två prisnivåer. Syftet med den här artikeln är att hjälpa dig att välja rätt prisnivån för dina behov. För att välja rätt prisnivå, ställa dig två frågor:

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Vilka geospatiala funktioner jag tänker använda?
Om du anser som din systemkrav uppfylls av core geospatiala API: er och S0 prisnivån är rätt för dig. Om du vill ha mer avancerade funktioner för ditt program, till exempel areal + hybridbilder komma vägen intervallet batch-geokodning osv bör du överväga att väljer du prisnivån S1. I tabellen nedan med **priser funktioner** ska ge dig en bättre uppfattning av ditt programs behov och du får också hjälp med att du väljer en prisnivå mest lämpliga för ditt program.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Hur många samtidiga användare planerar för att stödja? 
S0 och S1 prisnivåer kan hantera olika mängder data dataflöde. Överväg att fråga dig själv frågor som hur många samtidiga användare vill att stödja innan du väljer ett Azure Maps prisnivå? S0 prisnivå kan hantera upp till **50 frågor per sekund** och S1-prisnivån kan hantera **mer än 50 frågor per sekund**.

### <a name="pricing-tier-capabilities"></a>Priser för funktioner

| Funktion                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Search                                  |        ✓           |     ✓    |
| Routning                                 |        ✓           |     ✓    |
| Rendering                                  |        ✓           |     ✓    |
| Trafik                                 |        ✓           |     ✓    |
| Tidszoner                              |        ✓           |     ✓    |
| * Bilder + Hybridbilder (förhandsversion)     |        ✓           |     ✓    |
| * Vägen intervallet (förhandsversion)                  |        ✓           |     ✓    |
| * IP 2 plats (förhandsversion)                |        ✓           |     ✓    |
| * Polygoner från sökning (förhandsversion)         |        ✓           |     ✓    |
| * Batch Geokodning (förhandsversion)              |        ✓           |     ✓    |
| * Batch routning (förhandsversion)                |        ✓           |     ✓    |
| * Matris routning (förhandsversion)               |        ✓           |     ✓    |

> [!Note]
> Åtkomst till dessa funktioner från S0 prisnivån upphör att gälla efter 4 februari 2019.

Vissa ytterligare datapunkter värt överväger, vilken typ av enterprise har du eller hur viktiga är programmet skapas?

Se tabellen med **prisnivå riktad kunder** att få en bättre uppfattning av prisnivåer S0 och S1. För mer information om priser för Azure Maps, se [priser för Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>Prisnivå riktad kunder

| Prisnivå  |        Aktuella kunder                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>S0-prisnivån är för kunder som är små eller medelstora företag. Det är rätt prisnivå du om du inte räknar med stora volymer av samtidiga användare och din systemkrav uppfylls av core geospatiala API: er som anges av tabellen ovan. Den här nivån är allmänt tillgängligt och kan användas för program i alla led i produktion från bevis på koncept utveckling och testning till produktion för program och distribution för tidigt.<p>|
| S1            |    <p>Du prisnivån S1 för kunder behöver stöd för storskaliga enterprise, verksamhetskritiska program och stora volymer av samtidiga användare eller kräver avancerade geospatiala tjänster.</p>|

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att visa och ändra prisnivå:

> [!div class="nextstepaction"]
> [Hantera prisnivå](how-to-manage-pricing-tier.md)
