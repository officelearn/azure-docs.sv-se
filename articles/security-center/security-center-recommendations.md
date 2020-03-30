---
title: Säkerhetsrekommendationer i Azure Security Center
description: Det här dokumentet går igenom hur rekommendationer i Azure Security Center hjälper dig att skydda dina Azure-resurser och hålla dig i enlighet med säkerhetsprinciper.
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
ms.date: 07/29/2019
ms.author: memildin
ms.openlocfilehash: 408b0f020be72b8e6b10dd6c97298afda1b91360
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245309"
---
# <a name="security-recommendations-in-azure-security-center"></a>Säkerhetsrekommendationer i Azure Security Center 
I det här avsnittet beskrivs hur du visar och förstår rekommendationerna i Azure Security Center som hjälper dig att skydda dina Azure-resurser.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det här dokumentet är inte en steg-för-steg-guide.
>

## <a name="what-are-security-recommendations"></a>Vad är säkerhetsrekommendationer?

Rekommendationer är åtgärder som du kan vidta för att säkra dina resurser.

Security Center analyserar regelbundet säkerhetstillståndet för dina Azure-resurser för att identifiera potentiella säkerhetsproblem. Det ger dig sedan rekommendationer om hur du tar bort dem.

Varje rekommendation ger dig:

- En kort beskrivning av vad som rekommenderas.
- De åtgärder som ska åtgärdas för att genomföra rekommendationen. <!-- In some cases, Quick Fix remediation is available. -->
- Vilka resurser som behövs för att du ska utföra den rekommenderade åtgärden på dem.
- **Den säkra poängen påverkar**, vilket är det belopp som din Säkra poäng kommer att gå upp om du implementerar den här rekommendationen.

## <a name="monitor-recommendations"></a>Övervaka rekommendationer<a name="monitor-recommendations"></a>

Security Center analyserar säkerhetstillståndet för dina resurser för att identifiera potentiella sårbarheter. Panelen **Rekommendationer** under **Översikt** visar det totala antalet rekommendationer som identifierats av Security Center.

![Översikt över säkerhetscenter](./media/security-center-recommendations/asc-overview.png)

1. Välj **panelen Rekommendationer** under **Översikt**. Listan **Rekommendationer** öppnas.

      ![Visa rekommendationer](./media/security-center-recommendations/view-recommendations.png)

    Du kan filtrera rekommendationer. Om du vill filtrera rekommendationerna väljer du **Filter** på bladet **Rekommendationer.** **Filterbladet** öppnas och du väljer de allvarlighetsgrader och tillståndsvärden som du vill se.

   * **REKOMMENDATIONER**: Rekommendationen.
   * **SÄKER POÄNGPÅVERKAN:** En poäng som genereras av Security Center med hjälp av dina säkerhetsrekommendationer och tillämpa avancerade algoritmer för att avgöra hur viktig varje rekommendation är. Mer information finns i [Beräkning av säker poäng](security-center-secure-score.md#secure-score-calculation).
   * **RESURS**: Visar en lista över de resurser som rekommendationen gäller för.
   * **STATUSSTAplar**: Beskriver allvarlighetsgraden för den aktuella rekommendationen:
       * **Hög (röd)**: Det finns ett säkerhetsproblem med en meningsfull resurs (till exempel ett program, en virtuell dator eller en nätverkssäkerhetsgrupp) och kräver uppmärksamhet.
       * **Medium (Orange)**: Det finns ett säkerhetsproblem och icke-kritiska eller ytterligare steg krävs för att eliminera det eller för att slutföra en process.
       * **Låg (blå)**: Det finns en sårbarhet som bör åtgärdas men som inte kräver omedelbar uppmärksamhet. (Som standard presenteras inte låga rekommendationer, men du kan filtrera efter låga rekommendationer om du vill se dem.) 
       * **Friska (Grön)**:
       * **Inte tillgänglig (grå)**:

1. Klicka på rekommendationen om du vill visa informationen om varje rekommendation.

    ![Information om rekommendation](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Se [klassiska och Resource Manager-distributionsmodeller](../azure-classic-rm.md) för Azure-resurser.
 
## <a name="next-steps"></a>Nästa steg

I det här dokumentet introducerades du i säkerhetsrekommendationer i Security Center. Så här lär du dig hur du åtgärdar rekommendationerna:

* [Åtgärda rekommendationer](security-center-remediate-recommendations.md) – Lär dig hur du konfigurerar säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
