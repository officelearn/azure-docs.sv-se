---
title: Översikt över Azure FarmBeats
description: Ger en översikt över Azure-FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 12882860b8a9beadd4e56cbb151d670fac4da43b
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73798699"
---
# <a name="overview-of-azure-farmbeats"></a>Översikt över Azure FarmBeats

Azure FarmBeats är en samling Azure-tjänster och-funktioner som har utformats för att hjälpa dig att snabbt bygga intelligenta data drivna lösningar i jordbruket. Azure FarmBeats är ett Azure Marketplace-erbjudande som gör att du kan förvärva, samla in och bearbeta data från jordbruket från olika källor, till exempel sensorer, drönare, kameror, satellit, utan att investera i djup data teknik resurser.

> [!NOTE]
> Azure FarmBeats är för närvarande en offentlig för hands version. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Azure FarmBeats tillhandahålls utan service nivå avtal. Använd [Azure FarmBeats-forumet](https://aka.ms/FarmBeatsMSDN ) för support.

Med Azure FarmBeats kommer du att kunna hämta data från olika källor som sensorer, satellit, drönare, allt inom ramen för en Server grupp (ett geografiskt område av intresse).

Du kommer att kunna:

- Sammanställ jordbruks data uppsättningar från olika leverantörer
- Bygg snabbt AI/ML-modeller (artificiell intelligens/maskin språk) genom att fusing olika data uppsättningar

Med Azure FarmBeats får du ett robust och enkelt sätt att göra följande:

- Skapa en karta för Server gruppen med hjälp av en multijson-fil.
- Bedöm hälso tillståndet för Server gruppen med vegetations index och vatten index baserat på satellit-bilder.
- Få rekommendationer om hur många sensorer som ska användas och var de ska placeras.
- Spåra Server grupps tillstånd genom visualisering av mark data som samlats in av sensorer från olika sensor leverantörer.
- Hämta jord fuktighets kartor baserat på fusion av satellit-och sensor data.
- Få användbara insikter genom att skapa AI/ML-modeller ovanpå aggregerade data uppsättningar.
- Bygg eller utöka din digitala jordbruks lösning genom att tillhandahålla hälso rekommendationer för Server gruppen.

Azure FarmBeat-komponenter beskrivs i följande avsnitt i den här artikeln.

## <a name="data-hub"></a>Data hubb

Ett API-lager som möjliggör agg regerings-, normaliserings-och contextualization av olika jordbruks data uppsättningar mellan leverantörer. Från och med den här för hands versionen kan du använda två sensor leverantörer [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), en satellit bilder [-Provider Sentinel-2](https://sentinel.esa.int/web/sentinel/home)och två drönare bilder-providers [senseFly](https://www.sensefly.com/) , [SlantRange](https://slantrange.com/). Datahubben är utformad som en API-plattform och vi arbetar med många fler leverantörer för att integrera med Azure FarmBeats, så du har mer valmöjligheter när du skapar din lösning.

## <a name="accelerator"></a>Gas

En exempel lösning som bygger på data hubben och som JumpStarts ditt användar gränssnitt och modell utveckling. Det här webb programmet utnyttjar API: er för att demonstrera visualisering av inmatade sensor data som diagram och visualisering av modellens utdata som Maps. Du kan till exempel använda acceleratorn för att snabbt skapa en Server grupp och snabbt få en karta över index karta eller en sensor placerings karta för Server gruppen.

## <a name="resources"></a>Resurser

Besök FarmBeats- [bloggen](https://aka.ms/AzureFarmBeats) och [forumen](https://aka.ms/FarmBeatsMSDN).

## <a name="next-steps"></a>Nästa steg

Om du vill komma igång med Azure FarmBeats går du till [Azure Marketplace](https://aka.ms/FarmBeatsMarketplace) och distribuerar.
