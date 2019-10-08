---
title: Hantering av flera innehavare i Azure Security Center | Microsoft Docs
description: " Lär dig hur du aktiverar datainsamling i Azure Security Center. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: f7aa75601639bc0c5652010fb44b01dec0d2622b
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996940"
---
# <a name="cross-tenant-management-in-security-center"></a>Hantering av flera innehavare i Security Center

Med hantering av flera innehavare kan du Visa och hantera säkerhets position för flera klienter i Security Center genom att använda [Azure-delegerad resurs hantering](../lighthouse/concepts/azure-delegated-resource-management.md) . Hantera flera klienter effektivt, från en enda vy, utan att behöva logga in på varje klients katalog.

- Tjänste leverantörer kan hantera position av resurser, för flera kunder, inifrån sin egen klient organisation.

- Säkerhets team för organisationer med flera klienter kan visa och hantera sina säkerhets position från en enda plats.

## <a name="set-up-cross-tenant-management"></a>Konfigurera hantering av flera innehavare

Konfigurera hantering av flera innehavare genom att delegera åtkomst till resurser av hanterade klienter till din egen klient med [Azure delegerad resurs hantering](../lighthouse/concepts/azure-delegated-resource-management.md).

> [!NOTE]
> Azure-delegerad resurs hantering är en av de viktigaste komponenterna i Azure dataLighthouses.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Hur fungerar hantering av flera innehavare i Security Center

Du kan granska och hantera prenumerationer över flera klienter på samma sätt som du hanterar flera prenumerationer i en enda klient.

Klicka på filter ikonen i det övre meny fältet och välj prenumerationer från varje klient katalog som du vill visa.

  ![Filtrera klient organisationer](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

Vyer och åtgärder är i princip samma. Här följer några exempel:

- **Hantera säkerhets principer**: Från en vy hanterar du säkerhets position för många resurser med [principer](tutorial-security-policy.md), vidtar åtgärder med säkerhets rekommendationer och samlar in och hanterar säkerhetsrelaterade data.
- **Förbättra position för säker Poäng och efterlevnad**: Med synlighet mellan flera klienter kan du Visa den övergripande säkerhets position för alla dina klienter och var och hur du bäst kan förbättra position för [säker Poäng](security-center-secure-score.md) och [efterlevnad](security-center-compliance-dashboard.md) för var och en av dem.
- **Åtgärda rekommendationer**: Övervaka och reparera en [rekommendation](security-center-recommendations.md) för många resurser från olika klienter på en gång. Du kan sedan omedelbart ta itu över de säkerhets risker som visar den högsta risken för alla klienter.
- **Hantera aviseringar**: Identifiera [aviseringar](security-center-alerts-overview.md) i de olika klient organisationerna. Vidta åtgärder för resurser som inte är kompatibla med åtgärds bara [steg](security-center-managing-and-responding-alerts.md).

- **Hantera avancerade funktioner för moln försvar och mer**: Hantera de olika hot identifierings-och skydds tjänsterna, till exempel [just-in-Time (JIT) VM-åtkomst](security-center-just-in-time.md), [anpassningsbar nätverks härdning](security-center-adaptive-network-hardening.md), [anpassningsbara program kontroller](security-center-adaptive-application.md)och mycket annat.
 
## <a name="next-steps"></a>Nästa steg
Den här artikeln förklarar hur hantering av flera innehavare fungerar i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Förbättra din säkerhets position med Azure Security Center](security-center-monitoring.md) – lär dig att övervaka hälso tillståndet för dina Azure-resurser.
* [Azure Security Center FAQ](security-center-faq.md) – hitta vanliga frågor om att använda tjänsten.
* [Lär dig om Azure Lighthouse i Enterprise-scenarier](https://docs.microsoft.com/azure/lighthouse/concepts/enterprise) – Upptäck hur Azure Lighthouse kan förenkla hantering av flera innehavare i ett företag som använder flera Azure AD-klienter.