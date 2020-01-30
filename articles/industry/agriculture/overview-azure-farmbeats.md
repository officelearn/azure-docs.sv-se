---
title: Vad är Azure FarmBeats
description: Ger en översikt över Azure-FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: b067b18985905b226287f9dd10ad4b937fab6df1
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2020
ms.locfileid: "76767973"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Översikt över Azure-FarmBeats (för hands version)

Azure FarmBeats är ett affärs-till-affärs erbjudande som är tillgängligt på Azure Marketplace. Det möjliggör agg regering av jordbruks data uppsättningar mellan leverantörer. Med Azure FarmBeats kan du bygga artificiell intelligens (AI) eller Machine Learning-modeller (ML) baserat på säkrade data uppsättningar. Med hjälp av Azure FarmBeats kan jordbrukets företag fokusera på kärn värde – lägger till i stället för den avskilda kraftigt avlyftningen av data teknik.

> [!NOTE]
> Azure FarmBeats är för närvarande en offentlig för hands version. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Azure FarmBeats tillhandahålls utan service nivå avtal. Använd [Azure FarmBeats-forumet](https://aka.ms/FarmBeatsMSDN ) för support.

![Taktslag i projekt grupp](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Med för hands versionen av Azure-FarmBeats kan du:

- Bedöm hälso tillståndet för Server gruppen med vegetations index och vatten index baserat på satellit-bilder.
- Få rekommendationer om hur många jord fuktighets sensorer som ska användas och var de ska placeras.
- Spåra Server grupps villkor genom visualisering av mark data som samlats in av sensorer från olika leverantörer.
- Hämta jord fuktighets kartor baserat på fusion av satellit-och sensor data.
- Få användbara insikter genom att skapa AI/ML-modeller ovanpå aggregerade data uppsättningar.
- Bygg eller utöka din digitala jordbruks lösning genom att tillhandahålla hälso rekommendationer för Server gruppen.

## <a name="datahub"></a>Datahub

Azure FarmBeats-Datahub är ett API-lager som möjliggör agg regerings-, normaliserings-och contextualization för olika jordbruks data uppsättningar i olika leverantörer. Du kan använda Azure-FarmBeats för att få:
- **Sensor data** från två sensor leverantörer [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), [Pessl Instruments](https://metos.at/)
- **Satellit bilder** från Europeiska rums byråens [kontroll 2](https://sentinel.esa.int/web/sentinel/home) satellit uppdrag
- **Drönare bilder** från tre drönare bilder-providers [senseFly](https://www.sensefly.com/) , [SlantRange](https://slantrange.com/) , [DJI](https://dji.com/)

Datahub är utformad som en utöknings bar API-plattform. Vi arbetar med många fler leverantörer för att integrera med Azure FarmBeats, så du har fler valmöjligheter när du skapar din lösning.

## <a name="accelerator"></a>Gas

Azure FarmBeats Accelerator är ett exempel på ett webb program som bygger på Datahub. Accelerator hopp – startar ditt användar gränssnitt och din modell utveckling. Azure FarmBeats-acceleratorn använder Azure FarmBeats-API: er. Den visualiserar inmatade sensor data som diagram och modellens utdata som Maps. Du kan till exempel använda acceleratorn för att skapa en Server grupp snabbt och få en vegetations index karta eller en sensor placerings karta för det Server gruppen enkelt.

## <a name="resources"></a>Resurser

Azure FarmBeats erbjuds utan extra kostnad och du betalar bara för de Azure-resurser som du använder. Du kan använda nedanstående resurser för att få mer information om erbjudandet:

- Håll dig informerad om de senaste Azure FarmBeats-nyheterna genom att gå till vår [Azure FarmBeats-blogg](https://aka.ms/farmbeatsblog).
- Sök efter hjälp genom att publicera en fråga i vårt [Azure FarmBeats support-forum](https://aka.ms/farmbeatssupport).
- Ge feedback genom att publicera eller rösta på en funktions idé i vårt [Azure FarmBeats feedback-forum](https://aka.ms/farmbeatsfeedback).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Installera Azure-FarmBeats](install-azure-farmbeats.md)
