---
title: Behörigheter i Azure Security Center | Microsoft-dokument
description: I den här artikeln beskrivs hur Azure Security Center använder rollbaserad åtkomstkontroll för att tilldela behörigheter till användare och identifierar tillåtna åtgärder för varje roll.
services: security-center
cloud: na
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 0e60e782fa65cd5868bebe081673f9a158e07799
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921325"
---
# <a name="permissions-in-azure-security-center"></a>Behörigheter i Azure Security Center

I Azure Security Center tillämpas [rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md), vilket innebär att det finns [förinställda roller](../role-based-access-control/built-in-roles.md) som kan ges till användare, grupper och tjänster i Azure.

Security Center utvärderar konfigurationen av dina resurser för att identifiera säkerhetsproblem och säkerhetsproblem. I Security Center visas bara information som är relaterad till en resurs när du tilldelas rollen som Ägare, Deltagare eller Läsare för den prenumeration eller resursgrupp som en resurs tillhör.

Förutom dessa roller finns två specifika roller i Security Center:

* **Säkerhetsläsare:** En användare som tillhör den här rollen har visningsrättigheter till Security Center. Användaren kan visa rekommendationer, aviseringar, en säkerhetsprincip och säkerhetstillstånd, men kan inte göra ändringar.
* **Säkerhetsadministratör**: En användare som tillhör den här rollen har samma rättigheter som säkerhetsläsaren och kan även uppdatera säkerhetsprincipen och avvisa aviseringar och rekommendationer.

> [!NOTE]
> Säkerhetsrollerna, Säkerhetsläsaren och säkerhetsadministratören, har endast åtkomst i Security Center. Säkerhetsrollerna har inte åtkomst till andra tjänstområden i Azure, till exempel Lagring, & Mobile eller Sakernas Internet.
>
>

## <a name="roles-and-allowed-actions"></a>Roller och tillåtna åtgärder

I följande tabell visas roller och tillåtna åtgärder i Security Center.

| Roll | Redigera säkerhetsprincip | Tillämpa säkerhetsrekommendationer för en resurs</br> (inklusive med "Quick Fix!") | Avvisa aviseringar och rekommendationer | Visa aviseringar och rekommendationer |
|:--- |:---:|:---:|:---:|:---:|
| Ägare av prenumeration | ✔ | ✔ | ✔ | ✔ |
| Prenumerationsdeltagare | -- | ✔ | ✔ | ✔ |
| Ägare av resursgrupp | -- | ✔ | -- | ✔ |
| Medarbetare för resursgrupp | -- | ✔ | -- | ✔ |
| Läsare | -- | -- | -- | ✔ |
| Säkerhetsadministratör | ✔ | -- | ✔ | ✔ |
| Säkerhetsläsare | -- | -- | -- | ✔ |

> [!NOTE]
> Vi rekommenderar att du ger användarna den roll som precis ger dem den behörighet de behöver för att kunna utföra sina arbetsuppgifter. Tilldela till exempel rollen Läsare till användare som bara behöver visa information om säkerhetshälsan för en resurs men inte vidta åtgärder, till exempel tillämpa rekommendationer eller redigeringsprinciper.
>
>

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur Security Center använder RBAC för att tilldela behörigheter till användare och identifierade de tillåtna åtgärderna för varje roll. Nu när du känner till de rolltilldelningar som behövs för att övervaka säkerhetstillståndet för din prenumeration, redigera säkerhetsprinciper och tillämpa rekommendationer kan du läsa om hur du:

- [Ange säkerhetsprinciper i Security Center](tutorial-security-policy.md)
- [Hantera säkerhetsrekommendationer i Security Center](security-center-recommendations.md)
- [Övervaka säkerhetshälsan för dina Azure-resurser](security-center-monitoring.md)
- [Hantera och besvara säkerhetsaviseringar i Security Center](security-center-managing-and-responding-alerts.md)
- [Övervaka partnersäkerhetslösningar](security-center-partner-solutions.md)
