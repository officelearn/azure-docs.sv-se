---
title: Behörigheter i Azure Security Center | Microsoft Docs
description: I den här artikeln förklaras hur Azure Security Center använder rollbaserad åtkomst kontroll för att tilldela behörigheter till användare och för att identifiera tillåtna åtgärder för varje roll.
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921325"
---
# <a name="permissions-in-azure-security-center"></a>Behörigheter i Azure Security Center

I Azure Security Center tillämpas [rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md), vilket innebär att det finns [förinställda roller](../role-based-access-control/built-in-roles.md) som kan ges till användare, grupper och tjänster i Azure.

Security Center utvärderar konfigurationen av dina resurser för att identifiera säkerhetsproblem och säkerhetsproblem. I Security Center kan se du bara information relaterad till en resurs när du har tilldelats rollen ägare, deltagare eller läsare för prenumeration eller resursgrupp som en resurs hör till.

Förutom dessa roller finns två specifika roller i Security Center:

* **Säkerhets läsare**: en användare som tillhör den här rollen har visnings rättigheter att Security Center. Användaren kan visa rekommendationer, aviseringar, säkerhets principer och säkerhets tillstånd, men kan inte göra ändringar.
* **Säkerhets administratör**: en användare som tillhör den här rollen har samma rättigheter som säkerhets läsaren och kan också uppdatera säkerhets principen och ignorera aviseringar och rekommendationer.

> [!NOTE]
> Säkerhets rollerna, säkerhets läsaren och säkerhets administratören har endast åtkomst i Security Center. Säkerhets rollerna har inte åtkomst till andra tjänst områden i Azure, till exempel lagring, webb & mobil eller Sakernas Internet.
>
>

## <a name="roles-and-allowed-actions"></a>Roller och tillåtna åtgärder

I följande tabell visas roller och tillåtna åtgärder i Security Center.

| Roll | Redigera säkerhets princip | Tillämpa säkerhets rekommendationer för en resurs</br> (inklusive snabb korrigering!) | Ignorera aviseringar och rekommendationer | Visa aviseringar och rekommendationer |
|:--- |:---:|:---:|:---:|:---:|
| Prenumerations ägare | ✔ | ✔ | ✔ | ✔ |
| Prenumerations deltagare | -- | ✔ | ✔ | ✔ |
| Resurs grupp ägare | -- | ✔ | -- | ✔ |
| Resurs grupps deltagare | -- | ✔ | -- | ✔ |
| Läsare | -- | -- | -- | ✔ |
| Säkerhetsadministratör | ✔ | -- | ✔ | ✔ |
| Säkerhets läsare | -- | -- | -- | ✔ |

> [!NOTE]
> Vi rekommenderar att du ger användarna den roll som precis ger dem den behörighet de behöver för att kunna utföra sina arbetsuppgifter. Du kan till exempel tilldela rollen läsare till användare som bara behöver visa information om säkerhets hälso tillståndet för en resurs men inte vidta åtgärder, till exempel att tillämpa rekommendationer eller redigera principer.
>
>

## <a name="next-steps"></a>Nästa steg
I den här artikeln förklaras hur Security Center använder RBAC för att tilldela behörigheter till användare och identifierat tillåtna åtgärder för varje roll. Nu när du är bekant med roll tilldelningarna som krävs för att övervaka säkerhets status för din prenumeration, redigera säkerhets principer och använda rekommendationer, lär du dig att:

- [Ange säkerhets principer i Security Center](tutorial-security-policy.md)
- [Hantera säkerhets rekommendationer i Security Center](security-center-recommendations.md)
- [Övervaka Azure-resursernas säkerhets hälsa](security-center-monitoring.md)
- [Hantera och besvara säkerhetsaviseringar i Security Center](security-center-managing-and-responding-alerts.md)
- [Övervaka säkerhets lösningar för partner](security-center-partner-solutions.md)
