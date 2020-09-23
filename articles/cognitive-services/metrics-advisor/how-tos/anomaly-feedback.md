---
title: Ge avvikande feedback till Metrics Advisor-tjänsten
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skickar feedback om avvikelser som upptäckts av din Metric Advisor-instans och justera resultaten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 50d422edf1a4b45132d0b86eac9d4947cef5e5bf
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938044"
---
# <a name="adjust-anomaly-detection-using-feedback"></a>Justera avvikelseidentifiering med hjälp av feedback

Om du inte är nöjd med några av de avvikelse identifierings resultat som tillhandahålls av mått övervakaren kan du manuellt lägga till feedback som påverkar modellen som används för dina data. 

Använd knapparna i det övre högra hörnet på sidan för att aktivera feedback antecknings läget.

:::image type="content" source="../media/feedback/annotation-mode.png" alt-text="Antecknings läge för feedback.":::

När feedback-läge har Aktiver ATS kan du ge feedback för en punkt eller flera kontinuerliga punkter.

## <a name="give-feedback-for-one-point"></a>Ge feedback för en punkt 

När feedback antecknings läget är aktiverat klickar du på en punkt för att öppna en **Lägg till feedback** -panel. Du kan ange vilken typ av feedback du vill använda. Den här feedbacken kommer att ingå i identifieringen av framtida punkter.  

* Välj **avvikelse** om du tror att punkten felaktigt har etiketter ATS av mått övervakaren. Du kan ange om en punkt ska vara en avvikelse eller inte. 
* Välj **ChangePoint** om du tror att punkten visar att en trend förändring börjar.
* Välj **period** för att ange säsongs beroende. Mått övervakaren kan automatiskt identifiera intervall för säsongs beroende, eller så kan du ange det manuellt. 

Överväg att lämna en kommentar i text rutan **kommentar** samtidigt, och klicka på **Spara** för att spara din feedback.

:::image type="content" source="../media/feedback/feedback-menu.png" alt-text="Feedback-menyn.":::

## <a name="give-feedback-for-multiple-continuous-points"></a>Ge feedback för flera kontinuerliga punkter

Du kan ge feedback för flera kontinuerliga punkter samtidigt genom att klicka och dra musen på de punkter som du vill kommentera. Du kommer att se samma feedback-meny som ovan. Samma feedback kommer att gälla för alla valda punkter efter att du klickat på **Spara**.

:::image type="content" source="../media/feedback/continuous-points.png" alt-text="Välj flera punkter":::

## <a name="how-to-view-my-feedback"></a>Visa min feedback

Hovra över punkten för att se om en punkts avvikelse identifiering har ändrats. Knapp beskrivningen visas **som påverkad av feedback: sant** om identifieringen ändrades. Om den visar **falskt**genomfördes feedback-beräkningen på punkten, men resultatet av avvikelse identifieringen ändrades inte.

:::image type="content" source="../media/feedback/affected-point.png" alt-text="Punkt som påverkas av feedback":::

## <a name="when-should-i-annotate-an-anomaly-as-normal"></a>När bör jag kommentera en avvikelse som "normal"

Det finns många orsaker till att du kan tänka på att en avvikelse är ett falskt larm. Överväg att använda följande mått Advisor-funktioner om något av följande scenarier gäller:


|Scenario  |Rekommendation |
|---------|---------|
|Avvikelsen orsakas av en känd data käll ändring, till exempel en system ändring.     | Kommentera inte den här avvikelsen om det här scenariot inte förväntas utföras regelbundet.        |
|Avvikelsen orsakas av helgdag.     | Använd [förinställda händelser](configure-metrics.md#preset-events) för att flagga avvikelse identifiering vid specifika tidpunkter.       |
|Det finns ett vanligt mönster för att identifiera avvikelser (till exempel på helger) och de bör inte vara avvikelser.      |Använd feedback-funktionen eller förinställda händelser.        |

## <a name="next-steps"></a>Nästa steg
- [Diagnostisera en incident](diagnose-incident.md).
- [Konfigurera mått och finjustera konfiguration för identifiering](configure-metrics.md)
- [Konfigurera aviseringar och få meddelanden via en hook](../how-tos/alerts.md)