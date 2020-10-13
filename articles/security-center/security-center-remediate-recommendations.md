---
title: Åtgärda rekommendationer i Azure Security Center | Microsoft Docs
description: Den här artikeln förklarar hur du kan åtgärda rekommendationer i Azure Security Center för att skydda dina resurser och följa säkerhets principer.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 4bad3227e08c0fbe0d280967e45bbef9d477e1b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569143"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Reparationsrekommendationer i Azure Security Center

Rekommendationer ger förslag på hur du bättre skyddar resurserna. Du implementerar en rekommendation genom att följa de åtgärds steg som beskrivs i rekommendationen.

## <a name="remediation-steps"></a>Reparations steg <a name="remediation-steps"></a>

När du har granskat alla rekommendationer, Bestäm vilken av dem som ska åtgärdas först. Vi rekommenderar att du använder en [säker Poäng påverkan](security-center-recommendations.md#monitor-recommendations) för att prioritera vad du ska göra först.

1. Klicka på rekommendationen i listan.

1. Följ anvisningarna i avsnittet **reparations steg** . Varje rekommendation har en egen uppsättning instruktioner. Följande skärm bild visar åtgärds steg för att konfigurera program att bara tillåta trafik över HTTPS.

    ![Rekommendations information](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. När åtgärden har slutförts visas ett meddelande om att reparationen lyckades.

## <a name="quick-fix-remediation"></a>Snabb korrigerings reparation<a name="one-click"></a>

Med snabb korrigering kan du snabbt åtgärda en rekommendation om flera resurser. Den är endast tillgänglig för vissa rekommendationer. Snabb korrigering fören klar reparationen och gör att du snabbt kan öka dina säkra poäng och förbättra din miljös säkerhet.

Implementera snabb korrigerings reparation:

1. I listan över rekommendationer som har **snabb korrigering!** etikett klickar du på rekommendationen.

    [![Välj snabb korrigering!](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. På fliken **felaktiga resurser** väljer du de resurser som du vill implementera rekommendationen på och klickar på **åtgärda**.

    > [!NOTE]
    > Några av de listade resurserna kan vara inaktiverade eftersom du inte har rätt behörighet för att ändra dem.

1. Läs korrigerings informationen och konsekvenserna i bekräftelse rutan.

    ![Snabb korrigering](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > Konsekvenserna visas i den grå rutan i fönstret **Åtgärds resurser** som öppnas när du klickar på **åtgärda**. De listar vilka ändringar som sker när du fortsätter med snabb korrigerings reparationen.

1. Infoga relevanta parametrar vid behov och godkänn reparationen.

    > [!NOTE]
    > Det kan ta flera minuter efter att reparationen har slutförts för att se resurserna på fliken **felfria resurser** . Om du vill visa reparations åtgärderna kontrollerar du [aktivitets loggen](#activity-log).

1. När åtgärden har slutförts visas ett meddelande om att reparationen lyckades.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Snabb korrigering av reparations loggning i aktivitets loggen <a name="activity-log"></a>

Reparations åtgärden använder en mall för distribution av mallar eller REST-PATCH-API-anrop för att tillämpa konfigurationen på resursen. De här åtgärderna loggas i [aktivitets loggen i Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="next-steps"></a>Nästa steg

I det här dokumentet visade du hur du kan åtgärda rekommendationerna i Security Center. Mer information om Security Center finns i följande avsnitt:

* [Ange säkerhets principer i Azure Security Center](tutorial-security-policy.md) – lär dig hur du konfigurerar säkerhets principer för dina Azure-prenumerationer och resurs grupper.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md) – lär dig hur du övervakar Azure-resursernas hälsa.
