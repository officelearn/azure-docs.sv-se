---
title: Skapa aviseringar med dynamiska tröskelvärden i Azure Monitor
description: Skapa aviseringar med dynamiska tröskelvärden för maskininlärningsbaserade
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: yalavi
ms.reviewer: mbullwin
ms.openlocfilehash: 4024ecddde4b0d020e2c657214a4a258ea0b2ea5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449018"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor-public-preview"></a>Måttaviseringar med dynamiska tröskelvärden i Azure Monitor (förhandsversion)

Metrisk varning med dynamiska tröskelvärden identifiering utnyttjar avancerad maskininlärning (ML) om du vill veta mer mått historiska beteende, identifiera mönster och avvikelser som indikerar möjliga problem. Det finns stöd för både ett enkelt gränssnitt och åtgärder i stor skala genom att tillåta användare att konfigurera Varningsregler via API: et för Azure Resource Manager på ett helt automatiserat sätt.

När en varningsregel har skapats kan aktiveras den endast när det övervakade måtten inte fungerar som förväntat, baserat på dess skräddarsydda tröskelvärden.

Vi vill gärna höra dina synpunkter, hålla det kommer på azurealertsfeedback@microsoft.com.

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>När och varför använder dynamisk Villkorstyp rekommenderas?

1. **Skalbar aviseringar** – dynamiska tröskelvärden för aviseringar regler kan skapa anpassade tröskelvärden för hundratals mått serien i taget. Ger ännu är det lika enkelt att definiera en varningsregel för ett mått. Med hjälp av Användargränssnittet eller Azure Resource Manager API-resultat i färre Varningsregler för att hantera. Den skalbara metoden är särskilt användbart när du hanterar mått dimensioner eller när du tillämpar på flera resurser, t.ex. alla prenumerationsresurser. Vilket innebär att en betydande tid som sparas på hanterings- och skapande av regler för aviseringar. [Mer information om hur du konfigurerar aviseringar för mått med dynamiska tröskelvärden med hjälp av mallar](alerts-metric-create-templates.md).

1. **Smart mått mönsterigenkänning** – med hjälp av vår unika ML-teknik, kan vi automatiskt identifiera måttmönster och anpassa sig till mått ändringar över tid, som ofta kan innehålla säsongsberoende (per timme / dag / vecka). Anpassar sig till de mått beteende med tiden och avisering baserat på avvikelser från användningsmönster Frigör arbetet med vetskapen om att ”höger” tröskelvärdet för varje mått. ML-algoritmen som används i dynamiska tröskelvärden har utformats för att förhindra att bort störande (låg precision) eller tröskelvärden för hela (låg återkallande) som inte har ett förväntat mönster.

1. **Intuitiv Configuration** – dynamiska tröskelvärden att ställa in aviseringar för mått med hjälp av avancerade koncept lindra att behöva ha omfattande kunskap om måttet.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Konfigurera aviseringar regler med dynamiska tröskelvärden?

Aviseringar med dynamiska tröskelvärden kan konfigureras via aviseringar för mått i Azure Monitor. [Mer information om hur du konfigurerar aviseringar för mått](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Hur beräknas tröskelvärdena?

Dynamiskt tröskelvärde kontinuerligt lär sig av data för mått-serien och försöker att modellera den med hjälp av en uppsättning algoritmer och metoder. och försöker att modellera den med hjälp av en uppsättning algoritmer och metoder. Den identifierar mönster i data, till exempel säsongsvärdet (per timme / dag / vecka) och kan hantera bort störande mått (till exempel dator CPU eller minne) samt mått med låg spridning (till exempel tillgänglighet och fel-pris).

Tröskelvärdena som är markerade så att en avvikelse från de här tröskelvärdena visar avvikelser i måttbeteendet.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>Vad kostar ' känslighetsinställningen ' i dynamiska tröskelvärden medelvärdet?

Tröskelvärde för känslighet är ett övergripande begrepp som styr hur lång avvikelse från måttbeteende som krävs för att utlösa en avisering.
Det här alternativet kräver inte kunskap om mått som statiska tröskelvärdet. De tillgängliga alternativen är:

- Hög – är tröskelvärdena tätt och nära mönstret mått serien. Varningsregeln ska utlösas minsta avvikelse, vilket resulterar i fler aviseringar.
- Medel – mindre strikta och bättre avvägd tröskelvärden, färre aviseringar än med hög känslighet (standard).
- Låg – tröskelvärdena blir formulärdata med mer avståndet från mått serien mönster. Varningsregeln utlöser bara på stor avvikelse, vilket resulterar i färre aviseringar.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Vad är 'Operator' inställningsalternativen i dynamiska tröskelvärden?

Dynamiska tröskelvärden för aviseringar regel kan skapa anpassade tröskelvärden baserat på måttbeteende för båda övre och nedre gränser med hjälp av samma varningsregeln.
Du kan välja aviseringen ska utlösas på någon av följande tre villkor:

- Större än det övre tröskelvärdet eller lägre än det nedre tröskelvärdet (standard)
- Större än det högsta tröskelvärdet
- Lägre än det nedre tröskelvärdet.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>Vad gör de avancerade inställningarna i dynamiska tröskelvärden medelvärdet?

**Misslyckas perioder** -dynamiska tröskelvärden kan du konfigurera ”antal överträdelser för att utlösa aviseringen”, ett minsta antal avvikelser som krävs inom ett visst tidsintervall för systemet kan generera en avisering (standardvärdet för tidsperiod är fyra avvikelser i 20 minuter). Användaren kan konfigurera misslyckas punkter och välj vad du vill bli aviserad om genom att ändra misslyckas punkter och tidsperioden. Den här möjligheten minskar onödig avisering som genererats av övergående spikar. Exempel:

Om du vill utlösa en avisering när problemet är kontinuerligt efter 20 minuter, 4 på varandra följande gånger under en viss period gruppering på 5 minuter, använder du följande inställningar:

![Misslyckas perioder inställningar för kontinuerlig problemet 20 minuter 4 på varandra följande gånger under en viss period gruppering på 5 minuter](media/alerts-dynamic-thresholds/0008.png)

Om du vill utlösa en avisering när det har en överträdelse från en dynamiska tröskelvärden på 20 minuter från de senaste 30 minuterna med period på fem minuter, använder du följande inställningar:

![Misslyckas perioder inställningar för problemet på 20 minuter från de senaste 30 minuterna med period gruppering på 5 minuter](media/alerts-dynamic-thresholds/0009.png)

**Ignorera data före** -användare kan även definiera ett startdatum som systemet bör börja räkna tröskelvärden från. Ett vanligt användningsfall kan uppstå när en resurs har en körs i ett läge för testning och höjs nu för att leverera en produktionsarbetsbelastning och därför beteendet för vilka mått som helst under testfasen bör ignoreras.

## <a name="will-slow-behavior-change-in-the-metric-trigger-an-alert"></a>Långsam beteendet ändras i måttutlösaren en avisering?

Förmodligen inte. Dynamiska tröskelvärden är bra för identifiering av betydande avvikelser i stället för att långsamt utvecklas problem.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Hur mycket data används för att förhandsgranska och sedan beräkna tröskelvärden?

De tröskelvärden som visas i diagrammet, innan en varningsregel skapas på måttet beräknas baserat på de senaste 10 dagarna av historiska data när en varningsregel har skapats, dynamiska tröskelvärden kommer skaffa ytterligare historiska data som är tillgänglig och kommer Läs hela tiden baserat på nya data att göra det mer exakta tröskelvärdena.
