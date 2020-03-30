---
title: Vanliga frågor och svar om Azure Security Center – frågor om behörigheter
description: Den här vanliga frågor och svar på frågor om behörigheter i Azure Security Center, en produkt som hjälper dig att förebygga, identifiera och svara på hot.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 66a260fbb03f770ee98ec29a5f5e15e3d7dd1310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599397"
---
# <a name="permissions"></a>Behörigheter

## <a name="how-do-permissions-work-in-azure-security-center"></a>Hur fungerar behörigheter i Azure Security Center?

I Azure Security Center tillämpas [rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md), vilket innebär att det finns [förinställda roller](../role-based-access-control/built-in-roles.md) som kan ges till användare, grupper och tjänster i Azure.

Security Center utvärderar konfigurationen av dina resurser för att identifiera säkerhetsproblem och säkerhetsproblem. I Security Center visas bara information som är relaterad till en resurs när du tilldelas rollen som Ägare, Deltagare eller Läsare för den prenumeration eller resursgrupp som en resurs tillhör.

Se [Behörigheter i Azure Security Center](security-center-permissions.md) om du vill veta mer om roller och tillåtna åtgärder i Security Center.



## <a name="who-can-modify-a-security-policy"></a>Vem kan ändra en säkerhetsprincip?

Om du vill ändra en säkerhetsprincip måste du vara säkerhetsadministratör eller ägare eller deltagare i prenumerationen.

Mer information om hur du konfigurerar en säkerhetsprinciper finns [i Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md).