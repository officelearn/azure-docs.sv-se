---
title: Hantering över klienter i Azure Security Center | Microsoft-dokument
description: Lär dig hur du ställer in hantering över flera innehavare för att hantera säkerhetspositionen för flera klienter i Security Center med hjälp av Azure-delegerad resurshantering.
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
ms.openlocfilehash: 2aeb2ab4cfb4ed5e8652638aaced320cc7119d3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919489"
---
# <a name="cross-tenant-management-in-security-center"></a>Hantering över klienter i Security Center

Hantering av flera innehavare gör att du kan visa och hantera säkerhetspositionen för flera klienter i Security Center genom att utnyttja [Azure-delegerad resurshantering](../lighthouse/concepts/azure-delegated-resource-management.md) . Hantera flera klienter effektivt, från en enda vy, utan att behöva logga in på varje klientkatalog.

- Tjänsteleverantörer kan hantera säkerhetspositionen för resurser, för flera kunder, inifrån sin egen klientorganisation.

- Säkerhetsteam av organisationer med flera klienter kan visa och hantera sin säkerhetsposition från en enda plats.

## <a name="set-up-cross-tenant-management"></a>Ställ in hantering över flera innehavare

Ställ in hantering över klientgränser genom att delegera åtkomst till resurser för hanterade klienter till din egen klient med hjälp av [Azure-delegerad resurshantering](../lighthouse/concepts/azure-delegated-resource-management.md).

> [!NOTE]
> Azure-delegerad resurshantering är en av de viktigaste komponenterna i Azure Lighthouse.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Hur fungerar hantering över klienter i Security Center

Du kan granska och hantera prenumerationer över flera klienter på samma sätt som du hanterar flera prenumerationer i en enda klient.

Klicka på filterikonen på den övre menyraden och välj prenumerationerna från varje klientkatalog som du vill visa.

  ![Filtrera klienter](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

Åsikterna och åtgärderna är i stort sett desamma. Här följer några exempel:

- **Hantera säkerhetsprinciper**: Hantera säkerhetspositionen för många resurser med [principer](tutorial-security-policy.md)från en vy, vidta åtgärder med säkerhetsrekommendationer och samla in och hantera säkerhetsrelaterade data.
- **Förbättra säker poäng och efterlevnad hållning:** Cross-tenant synlighet kan du visa den övergripande säkerheten hållning för alla dina hyresgäster och var och hur man bäst förbättra [säker poäng](security-center-secure-score.md) och [efterlevnad hållning](security-center-compliance-dashboard.md) för var och en av dem.
- **Åtgärda rekommendationer**: Övervaka och åtgärda en [rekommendation](security-center-recommendations.md) för många resurser från olika klienter samtidigt. Du kan sedan omedelbart ta itu med de sårbarheter som utgör den högsta risken för alla klienter.
- **Hantera aviseringar:** Identifiera [aviseringar](security-center-alerts-overview.md) i de olika klienterna. Vidta åtgärder för resurser som inte följer åtgärdsåtgärder som kan [åtgärdas.](security-center-managing-and-responding-alerts.md)

- **Hantera avancerade molnförsvarsfunktioner med mera:** Hantera de olika hotskyddstjänsterna, till exempel [just-in-time (JIT) VM-åtkomst,](security-center-just-in-time.md) [Adaptiv nätverkshärdning,](security-center-adaptive-network-hardening.md) [anpassningsbara programkontroller](security-center-adaptive-application.md)med mera.
 
## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur hantering av flera innehavare fungerar i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Stärka din säkerhetsposition med Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar hälsotillståndet för dina Azure-resurser.
* [Vanliga frågor och svar om Azure Security Center](faq-general.md) – Hitta vanliga frågor om hur du använder tjänsten.
* [Lär dig mer om Azure Lighthouse i företagsscenarier](https://docs.microsoft.com/azure/lighthouse/concepts/enterprise) – Upptäck hur Azure Lighthouse kan förenkla hantering av flera Azure AD-klienter i Azure Lighthouse.