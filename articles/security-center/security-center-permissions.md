---
title: Behörigheter i Azure Security Center | Microsoft Docs
description: Den här artikeln förklarar hur Azure Security Center använder rollbaserad åtkomstkontroll för att tilldela behörigheter till användare och identifierar de tillåtna åtgärderna för varje roll.
services: security-center
cloud: na
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/13/2017
ms.author: terrylan
ms.openlocfilehash: b93b57d50ccf5d5dfb092bdb71820da77f345878
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295470"
---
# <a name="permissions-in-azure-security-center"></a>Behörigheter i Azure Security Center

Azure Security Center använder [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/role-assignments-portal.md), som tillhandahåller [inbyggda roller](../role-based-access-control/built-in-roles.md) som kan tilldelas till användare, grupper och tjänster i Azure.

Security Center utvärderar konfigurationen av dina resurser för att identifiera säkerhetsproblem och säkerhetsproblem. I Security Center kan se du bara information relaterad till en resurs när du har tilldelats rollen ägare, deltagare eller läsare för prenumeration eller resursgrupp som en resurs hör till.

Förutom dessa roller finns två specifika roller i Security Center:

* **Säkerhetsläsare**: en användare som tillhör den här rollen har rätt behörighet till Security Center. Användaren kan visa rekommendationer, aviseringar, en säkerhetsprincip och security tillstånd, men göra inte några ändringar.
* **Säkerhetsadministratör**: en användare som tillhör den här rollen har samma rättigheter som Security-läsare och kan också uppdatera säkerhetsprinciper och stänga aviseringar och rekommendationer.

> [!NOTE]
> Säkerhetsroller, Säkerhetsläsare och säkerhetsadministratörer, har åtkomst endast i Security Center. Säkerhetsroller har inte åtkomst till andra delar av Azure, till exempel lagring, webb- och Mobile eller Sakernas Internet.
>
>

## <a name="roles-and-allowed-actions"></a>Roller och tillåtna åtgärder

I följande tabell visar roller och tillåtna åtgärder i Security Center. Ett X anger att åtgärden är tillåten för rollen.

| Roll | Redigera säkerhetsprincip | Tillämpa säkerhetsrekommendationer på en resurs | Avvisa aviseringar och rekommendationer | Visa aviseringar och rekommendationer |
|:--- |:---:|:---:|:---:|:---:|
| Prenumerationens ägare | X | X | X | X |
| Prenumerations-deltagare | X | X | X | X |
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

- [Ange säkerhetsprinciper i Security Center](security-center-policies.md)
- [Hantera säkerhetsrekommendationer i Security Center](security-center-recommendations.md)
- [Övervaka säkerhetshälsa för dina Azure-resurser](security-center-monitoring.md)
- [Hantera och besvara säkerhetsaviseringar i Security Center](security-center-managing-and-responding-alerts.md)
- [Övervaka partners säkerhetslösningar](security-center-partner-solutions.md)
