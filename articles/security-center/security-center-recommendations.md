---
title: Säkerhetsrekommendationer i Azure Security Center
description: Det här dokumentet vägleder dig genom hur rekommendationer i Azure Security Center hjälper dig att skydda dina Azure-resurser och hålla dem kompatibla med säkerhets principer.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: f3fb31dc7db4808db83f33abdf14179265d147f9
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425191"
---
# <a name="security-recommendations-in-azure-security-center"></a>Säkerhetsrekommendationer i Azure Security Center 
I det här avsnittet beskrivs hur du visar och förstår rekommendationerna i Azure Security Center som hjälper dig att skydda dina Azure-resurser.


## <a name="what-are-security-recommendations"></a>Vad är säkerhets rekommendationer?

Rekommendationer är åtgärder som du kan vidta för att skydda dina resurser.

Security Center analyserar regelbundet säkerhets status för dina Azure-resurser för att identifiera potentiella säkerhets risker. Därefter får du rekommendationer om hur du åtgärdar problemen.

Varje rekommendation ger dig följande:

- En kort beskrivning av problemet
- De åtgärder som vidtas för att genomföra rekommendationen
- De berörda resurserna

## <a name="monitor-recommendations"></a>Övervaka rekommendationer <a name="monitor-recommendations"></a>

Security Center analyserar dina resursers säkerhets tillstånd för att identifiera potentiella sårbarheter. 

1. Från Security Center menyn öppnar du sidan **rekommendationer** för att se de rekommendationer som gäller för din miljö. Rekommendationerna är grupperade i säkerhets kontroller.

      ![Rekommendationer grupperade efter säkerhets kontroll](./media/security-center-recommendations/view-recommendations.png)

1. Expandera en kontroll och välj en rekommendation för att Visa rekommendations informations sidan.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Sidan rekommendations information." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    Sidan innehåller:

    1. **Tillämpa** och **neka** knappar på rekommendationer som stöds (se [förhindra felaktig konfiguration med tvinga/neka-rekommendationer](prevent-misconfigurations.md))
    1. **Allvarlighets grad**
    1. **Aktualitets intervall**  (i förekommande fall) 
    1. **Beskrivning** – en kort beskrivning av problemet
    1. **Reparations steg** – en beskrivning av de manuella steg som krävs för att åtgärda säkerhets problemet på de berörda resurserna. För rekommendationer med snabb korrigering kan du välja **Visa reparations logik** innan du använder den föreslagna korrigeringen för dina resurser. 
    1. **Resurser som påverkas** – dina resurser grupperas i flikar:
        - **Felfria resurser** – relevanta resurser som antingen inte påverkas eller som du redan har åtgärdat problemet på.
        - Resurser som inte är **felfria** – resurser som fortfarande påverkas av det identifierade problemet.
        - **Ej tillämpliga resurser** – resurser för vilka rekommendationen inte kan ge ett definitivt svar. Fliken ej tillämpligt innehåller även orsaker för varje resurs. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Sidan rekommendations information.":::
    1. Åtgärds knappar för att reparera rekommendationen eller utlösa en Logic app.

## <a name="preview-recommendations"></a>För hands versions rekommendationer

Rekommendationer som har flaggats som **förhands granskning** ingår inte i beräkningarna av dina säkra poäng.

De bör fortfarande åtgärdas när så är möjligt, så att när förhands gransknings perioden är slut bidrar de till dina poäng.

Ett exempel på en förhands gransknings rekommendation:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Sidan rekommendations information.":::
 
## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lanserat säkerhets rekommendationer i Security Center. För relaterad information:

- [Åtgärda rekommendationer](security-center-remediate-recommendations.md) – lär dig hur du konfigurerar säkerhets principer för dina Azure-prenumerationer och resurs grupper.
- [Förhindra felaktig konfiguration med tvinga/neka-rekommendationer](prevent-misconfigurations.md).
- [Automatisera svar på rekommendationer](workflow-automation.md)
- [Undanta en resurs från en rekommendation](exempt-resource.md)
- [Säkerhetsrekommendationer – en referensguide](recommendations-reference.md)