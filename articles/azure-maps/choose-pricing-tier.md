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
ms.openlocfilehash: 56f9226f1bee630895725eb0b3806e294e9a5b51
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56218150"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Välja rätt prisnivå i Azure Maps

Azure Maps erbjuder två prisnivåer. Syftet med den här artikeln är att hjälpa dig att välja rätt prisnivån för dina behov. För att välja rätt prisnivå kan du fråga dig själv följande två.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Vilka geospatiala funktioner jag tänker använda?
S0 prisnivån är rätt för dig om core geospatiala API: er som uppfyller dina tjänstekrav på. Om du vill ha mer avancerade funktioner för ditt program bör du du väljer du prisnivån S1. Exempel-funktioner är areal plus hybridbilder komma vägen intervall och batch-geokodning. Den **priser funktioner** tabellen nedan ger dig en bättre uppfattning om programmets behov. Du kan också välja en prisnivå som är mest lämpliga för ditt program.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Hur många samtidiga användare planerar för att stödja? 
S0 och S1 prisnivåer hantera olika mängder data dataflöde. Innan du väljer ett Azure Maps prisnivå kan du fråga dig själv några frågor. Ett exempel är ”hur många samtidiga användare vill jag stöder”? S0 prisnivån hanterar upp till **50 frågor per sekund**. S1 prisnivå nivå handtagen **mer än 50 frågor per sekund**.

### <a name="pricing-tier-capabilities"></a>Priser för funktioner

| Funktion                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Search                                  |        ✓           |     ✓    |
| Routning                                 |        ✓           |     ✓    |
| Rendering                                  |        ✓           |     ✓    |
| Trafik                                 |        ✓           |     ✓    |
| Tidszoner                              |        ✓           |     ✓    |
| Bilder plus hybridbilder    |            |     ✓    |
| Väg intervall                    |                   |     ✓    |
| IP-2-plats (förhandsversion)                |        ✓           |     ✓    |
| Polygoner från sökning          |                   |     ✓    |
| Batch-geokodning (förhandsversion)              |                   |     ✓    |
| Batch-routning (förhandsversion)                |                   |     ✓    |
| Matrisen routning (förhandsversion)               |                   |     ✓    |


Dessa ytterligare datapunkter är värda att överväga:
* Vilken typ av enterprise har du?
* Hur viktiga är programmet skapas?

Se den **prisnivån riktad kunder** tabell för att få en bättre uppfattning S0 och S1 prisnivåer. Mer information finns i [priser för Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>Prisnivå riktad kunder

| Prisnivå  |     Aktuella kunder                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>S0-prisnivån är för kunder som är små eller medelstora företag. Det är rätt prisnivå du om du inte tror stora volymer av samtidiga användare. Det är också rätt om core geospatiala API: er som visas i tabellen ovan uppfyller dina tjänstekrav. Den här nivån är allmänt tillgänglig. Den fungerar för program i alla led i produktion: från proof of concept-utveckling och testning till produktion för program och distribution för tidigt.<p>|
| S1            |    <p>Du prisnivån S1 är avsedd för kunder behöver stöd för storskaliga enterprise, verksamhetskritiska program eller stora volymer av samtidiga användare. Det är också för de kunder som behöver avancerad geospatiala tjänster.</p>|

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du visar och ändrar prisnivåer:

> [!div class="nextstepaction"] 
> [Hantera en prisnivå](how-to-manage-pricing-tier.md)
