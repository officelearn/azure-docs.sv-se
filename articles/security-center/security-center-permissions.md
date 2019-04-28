---
title: Behörigheter i Azure Security Center | Microsoft Docs
description: Den här artikeln förklarar hur Azure Security Center använder rollbaserad åtkomstkontroll för att tilldela behörigheter till användare och identifierar de tillåtna åtgärderna för varje roll.
services: security-center
cloud: na
documentationcenter: na
author: rkarlin
manager: barbkess
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: aa6c154e14fbf5291e2493f4f27b9d4501ba18f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60905627"
---
# <a name="permissions-in-azure-security-center"></a>Behörigheter i Azure Security Center

I Azure Security Center tillämpas [rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md), vilket innebär att det finns [förinställda roller](../role-based-access-control/built-in-roles.md) som kan ges till användare, grupper och tjänster i Azure.

Security Center utvärderar konfigurationen av dina resurser för att identifiera säkerhetsproblem och säkerhetsproblem. I Security Center kan se du bara information relaterad till en resurs när du har tilldelats rollen ägare, deltagare eller läsare för prenumeration eller resursgrupp som en resurs hör till.

Förutom dessa roller finns två specifika roller i Security Center:

* **Säkerhetsläsare**: En användare som tillhör den här rollen har rätt behörighet till Security Center. Användaren kan visa rekommendationer, aviseringar, en säkerhetsprincip och security tillstånd, men göra inte några ändringar.
* **Säkerhetsadministratör**: En användare som tillhör den här rollen kan har samma rättigheter som Security-läsare och också uppdatera säkerhetsprinciper och stänga aviseringar och rekommendationer.

> [!NOTE]
> Säkerhetsroller, Säkerhetsläsare och säkerhetsadministratörer, har åtkomst endast i Security Center. Säkerhetsroller har inte åtkomst till andra delar av Azure, till exempel lagring, webb- och Mobile eller Sakernas Internet.
>
>

## <a name="roles-and-allowed-actions"></a>Roller och tillåtna åtgärder

I följande tabell visar roller och tillåtna åtgärder i Security Center. Ett X anger att åtgärden är tillåten för rollen.

| Roll | Redigera säkerhetsprincip | Tillämpa säkerhetsrekommendationer på en resurs | Avvisa aviseringar och rekommendationer | Visa aviseringar och rekommendationer |
|:--- |:---:|:---:|:---:|:---:|
| Prenumerationens ägare | X | X | X | X |
| Prenumerationsdeltagare | -- | X | X | X |
| Resursgrupp ägare | -- | X | -- | X |
| Resursgrupp deltagare | -- | X | -- | X |
| Läsare | -- | -- | -- | X |
| Säkerhetsadministratör | X | -- | X | X |
| Säkerhetsläsare | -- | -- | -- | X |

> [!NOTE]
> Vi rekommenderar att du ger användarna den roll som precis ger dem den behörighet de behöver för att kunna utföra sina arbetsuppgifter. Till exempel tilldela rollen Läsare för användare som bara behöver visa information om säkerhetshälsa för en resurs men inte vidta åtgärder, till exempel tillämpa rekommendationer eller ändra principer.
>
>

## <a name="next-steps"></a>Nästa steg
Den här artikeln beskrivs hur Security Center använder RBAC för att tilldela behörigheter till användare och identifierat de tillåtna åtgärderna för varje roll. Nu när du är bekant med rolltilldelningar som behövs för att övervaka säkerhetstillståndet för din prenumeration, redigera IPSec-principer och använder rekommendationerna, Lär dig hur du:

- [Ange säkerhetsprinciper i Security Center](tutorial-security-policy.md)
- [Hantera säkerhetsrekommendationer i Security Center](security-center-recommendations.md)
- [Övervaka säkerhetshälsa för dina Azure-resurser](security-center-monitoring.md)
- [Hantera och besvara säkerhetsaviseringar i Security Center](security-center-managing-and-responding-alerts.md)
- [Övervaka partners säkerhetslösningar](security-center-partner-solutions.md)
