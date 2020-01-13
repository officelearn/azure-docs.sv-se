---
title: Välj rätt pris nivå | Microsoft Azure Maps
description: I den här artikeln får du lära dig om pris nivåer som erbjuds av Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/02/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5ff774f9848db948058075a98504e6c13db5467a
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911755"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Välj rätt pris nivå i Azure Maps

Azure Maps finns i två prisnivåer. Syftet med den här artikeln är att hjälpa dig att välja rätt pris nivå för dina behov. Du kan välja rätt pris nivå genom att ställa följande två frågor.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Vilka geospatiala funktioner planerar jag att använda?
Pris nivån för S0 är rätt för dig om de grundläggande geospatiala API: erna uppfyller tjänst kraven. Om du vill ha mer avancerade funktioner för ditt program bör du tänka på väljer för pris nivån S1. Exempel på funktioner är Area plus hybrid bilder, hämtning av väg intervall och batchbokföring av batchar. Tabellen med **pris nivå funktioner** som följer ger dig en bättre uppfattning om programmets behov. Du kan också välja en pris nivå som passar bäst för ditt program.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Hur många samtidiga användare planerar jag att stödja? 
Pris nivåerna S0 och S1 hanterar olika mängder data genom strömning. Innan du väljer en Azure Maps pris nivå kan du ställa frågor. Ett exempel är "hur många samtidiga användare vill jag ha stöd för?" Pris nivån S0 hanterar upp till **50 frågor per sekund**. Pris nivån S1 hanterar **fler än 50 frågor per sekund**.

### <a name="pricing-tier-capabilities"></a>Pris nivå funktioner

| Kapacitet                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Sök (FWD/rev-enkodning, punkter av intresse)  |        ✓           |     ✓    |
| Batch-hårdkodad (för hands version)              |                   |     ✓    |
| Polygoner från sökning          |                   |     ✓    |
| Routning                                 |        ✓           |     ✓    |
| Flödes intervall                    |                   |     ✓    |
| Batchbearbetning (för hands version)                |                   |     ✓    |
| Mat ris routning (för hands version)               |                   |     ✓    |
| Rendering                                  |        ✓           |     ✓    |
| Bilder plus hybrid bilder    |            |     ✓    |
| Trafik                                 |        ✓           |     ✓    |
| Tidszoner                              |        ✓           |     ✓    |
| Geolokalisering (förhands granskning)                |        ✓           |     ✓    |
| Data (förhands granskning)               |                   |     ✓    |
| Avstånd (för hands version)               |                   |     ✓    |
| Polystaket (för hands version)               |                   |     ✓    |



Dessa ytterligare data punkter är värda att tänka på:
* Vilken typ av företag har du?
* Hur kritiskt är programmet byggt?

Se **pris nivån riktade kunder** för att få en bättre uppfattning om pris nivåerna S0 och S1. Mer information finns i [Azure Maps prissättning](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>Pris nivå riktade kunder

| Prisnivå  |     Riktad kund                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>Pris nivån S0 är för kunder som är antingen små eller medel stora företag. Det är rätt pris nivå för dig om du inte förväntar dig stora mängder av samtidiga användare. Det är också rätt om de geospatiala geospatiala API: erna som visas i tabellen ovan uppfyller tjänst kraven. Den här nivån är allmänt tillgänglig. Det fungerar för program i alla produktions faser: från koncept koncept utveckling och tidigt test till program produktion och distribution.<p>|
| S1            |    <p>Pris nivån S1 är för kunder som behöver stöd för storskaliga företags-, verksamhets kritiska program eller stora mängder av samtidiga användare. Det är också för de kunder som behöver avancerade geospatiala tjänster.</p>|

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att visa och ändra pris nivåer:

> [!div class="nextstepaction"] 
> [Hantera en pris nivå](how-to-manage-pricing-tier.md)
