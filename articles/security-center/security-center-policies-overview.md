---
title: Azure Security Center inställningar | Microsoft Docs
description: Konfigurera Azure Security Center inställningar.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/03/2018
ms.author: memildin
ms.openlocfilehash: 4a7254d4ac67ee7d1bf203baf5741638dbc8f3dd
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201621"
---
# <a name="security-center-settings"></a>Security Center-inställningar
Den här artikeln innehåller en översikt över inställningarna i Security Center.

Följande inställningar kan nås under säkerhets princip:

- **Data insamling**: Bestämmer inställningar för agent etablering och [data insamling](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) .
- **Säkerhets princip**: Bestämmer vilka kontroller som Security Center övervakar och rekommenderar. Du kan redigera [säkerhets principen](tutorial-security-policy.md) i Security Center. Du kan också använda [Azure policy](tutorial-security-policy.md) för att skapa nya definitioner, definiera ytterligare principer och tilldela principer över hanterings grupper. 
- **E-postmeddelanden**: Identifierar säkerhets kontakter och inställningar för [e-postaviseringar](security-center-provide-security-contact-details.md) .
- **Prisnivå**: Definierar alternativ för kostnads fri eller standard [priser](security-center-pricing.md). Vilken nivå du väljer avgör vilka Security Center-funktioner som blir tillgängliga för resurser i området. Du kan ange en nivå för prenumerationer och arbets ytor.

> [!NOTE]
> Du kan ange alla dessa per prenumeration. För arbets ytor kan du bara ange data insamling och pris nivå.
>


## <a name="who-can-edit-security-policies"></a>Vem kan redigera säkerhets principer?
Security Center använder rollbaserad Access Control (RBAC), som innehåller inbyggda roller som kan tilldelas användare, grupper och tjänster i Azure. När användarna öppnar Security Center ser de bara information som är relaterad till de resurser som de har åtkomst till. Det innebär att användarna tilldelas rollen som *ägare*, *deltagare*eller *läsare* till den prenumeration som resursen hör till. Förutom dessa roller finns två specifika roller i Security Center:

- **Säkerhets läsare**: Ha behörighet att Security Center, som innehåller rekommendationer, aviseringar, principer och hälsa, men som inte kan göra ändringar.
- **Säkerhets administratör**: Ha samma visnings rättigheter som *säkerhets läsaren*, och de kan också uppdatera säkerhets principen och stänga rekommendationer och aviseringar.


## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig om säkerhets principer i Azure Security Center. Mer information om Azure Security Center finns i följande artiklar:

* [Ställer in säkerhets principer i Azure Security Center](tutorial-security-policy.md): Lär dig hur du konfigurerar säkerhets principer för dina Azure-prenumerationer.
* [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md): Lär dig hur Security Center rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md): Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Hantera och svara på säkerhets aviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Lär dig att hantera och åtgärda säkerhetsaviseringar.
* [Övervaka partner lösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig att övervaka hälsotillståndet för dina partnerlösningar.
* [Azure Security Center data säkerhet](security-center-data-security.md): Lär dig hur Security Center hanterar och skyddar data.
* [Azure Security Center vanliga frågor och svar](security-center-faq.md): Få svar på vanliga frågor om att använda tjänsten.
* [Azure-säkerhets blogg](https://blogs.msdn.com/b/azuresecurity/): Få de senaste nyheterna och informationen om Azure-säkerhet.
