---
title: "Behörigheter i Azure Security Center | Microsoft Docs"
description: "Den här artikeln beskrivs hur Azure Security Center använder rollbaserad åtkomstkontroll för att tilldela behörigheter till användare och identifierar de tillåtna åtgärderna för varje roll."
services: security-center
cloud: na
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
ms.assetid: 
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: terrylan
ms.openlocfilehash: 0aaa99dda44d2020afd3e841e84020eb4ff87a85
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="permissions-in-azure-security-center"></a>Behörigheter i Azure Security Center

I Azure Security Center tillämpas [rollbaserad åtkomstkontroll](../active-directory/role-based-access-control-configure.md), vilket innebär att det finns [förinställda roller](../active-directory/role-based-access-built-in-roles.md) som kan ges till användare, grupper och tjänster i Azure.

Security Center utvärderar konfigurationen av dina resurser för att identifiera säkerhetsproblem och säkerhetsproblem. I Security Center kan se du bara information relaterad till en resurs när du har tilldelats rollen ägare, deltagare eller läsare för prenumeration eller resursgrupp som en resurs tillhör.

Förutom dessa roller finns två specifika roller i Security Center:

* **Säkerhet Reader**: en användare som tillhör den här rollen har rätt behörighet till Security Center. Användaren kan visa rekommendationer, aviseringar, en säkerhetsprincip och säkerhetsstatus, men det går inte att göra ändringar.
* **Säkerhetsadministratör**: en användare som tillhör den här rollen har samma rättigheter som läsaren säkerhet och kan också uppdatera säkerhetsprincipen och stänga aviseringar och rekommendationer.

> [!NOTE]
> Säkerhetsroller, säkerhet, läsare och säkerhetsadministratör, komma åt endast i Security Center. Säkerhetsroller har inte åtkomst till andra service delar av Azure, till exempel lagring, webb & Mobile eller Sakernas Internet.
>
>

## <a name="roles-and-allowed-actions"></a>Roller och tillåtna åtgärder

Följande tabell visar roller och tillåtna åtgärder i Security Center. Ett X anger att åtgärden tillåts för rollen.

| Roll | Redigera säkerhetsprincip | Tillämpa säkerhetsrekommendationer på en resurs | Avvisa aviseringar och rekommendationer | Visa aviseringar och rekommendationer |
|:--- |:---:|:---:|:---:|:---:|
| Prenumerationsägaren | X | X | X | X |
| Deltagare i prenumeration | X | X | X | X |
| Ägaren av resursgruppen. | -- | X | -- | X |
| Deltagare i resursgrupp | -- | X | -- | X |
| Läsare | -- | -- | -- | X |
| Säkerhetsadministratör | X | -- | X | X |
| Säkerhet läsare | -- | -- | -- | X |

> [!NOTE]
> Vi rekommenderar att du ger användarna den roll som precis ger dem den behörighet de behöver för att kunna utföra sina arbetsuppgifter. Till exempel tilldela rollen Läsare för användare som behöver bara visa information om säkerhetshälsa för en resurs men inte vidta några åtgärder, till exempel tillämpa rekommendationer eller ändra principer.
>
>

## <a name="next-steps"></a>Nästa steg
Den här artikeln förklaras hur RBAC använder i Security Center för att tilldela behörigheter till användare och identifieras de tillåtna åtgärderna för varje roll. Nu när du är bekant med rolltilldelningar som behövs för att övervaka säkerhetsläget för din prenumeration, redigera IPSec-principer och tillämpa rekommendationer, Läs mer om hur du:

- [Ange säkerhetsprinciper i Security Center](security-center-policies.md)
- [Hantera säkerhetsrekommendationer i Security Center](security-center-recommendations.md)
- [Övervaka säkerhetshälsa för Azure-resurser](security-center-monitoring.md)
- [Hantera och besvara säkerhetsaviseringar i Security Center](security-center-managing-and-responding-alerts.md)
- [Övervaka partnerlösningar för säkerhet](security-center-partner-solutions.md)
