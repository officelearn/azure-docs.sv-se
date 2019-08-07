---
title: Åtgärda rekommendationer i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskriver hur du kan åtgärda rekommendationer i Azure Security Center som hjälper dig att skydda dina Azure-resurser och hålla dem kompatibla med säkerhets principer.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2019
ms.author: v-mohabe
ms.openlocfilehash: b947796d4af9def1a274f6e04f356c3173d29fff
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779002"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Åtgärda rekommendationer i Azure Security Center

Rekommendationer ger förslag på hur du bättre skyddar resurserna.  Du implementerar en rekommendation genom att följa de åtgärds steg som beskrivs i rekommendationen. 

## Reparations steg<a name="remediation-steps"></a>

När du har granskat alla rekommendationer, Bestäm vilken av dem som ska åtgärdas först. Vi rekommenderar att du använder en [säker Poäng påverkan](security-center-recommendations.md#monitor-recommendations) för att prioritera vad du ska göra först.

1. Klicka på rekommendationen i listan.
1. Följ anvisningarna i avsnittet **reparations steg** . Varje rekommendation har en egen uppsättning instruktioner. Följande visar åtgärds steg för att konfigurera program att bara tillåta trafik över HTTPS.

    ![Information om rekommendation](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. När åtgärden har slutförts visas ett meddelande om att reparationen lyckades.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet visade du hur du kan åtgärda rekommendationerna i Security Center. Mer information om Security Center finns i följande avsnitt:

* Att [ställa in säkerhets principer i Azure Security Center](tutorial-security-policy.md) – lär dig hur du konfigurerar säkerhets principer för dina Azure-prenumerationer och resurs grupper.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar Azure-resursernas hälsa.
