---
title: Azure Security Center FAQ – frågor om behörigheter
description: Detta vanliga frågor och svar om behörigheter i Azure Security Center, en produkt som hjälper dig att förhindra, identifiera och svara på hot.
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
ms.openlocfilehash: 89b3233966ac53d6c384ca6bd5ac30322fbb0b02
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106601"
---
# <a name="permissions"></a>Behörigheter

## <a name="how-do-permissions-work-in-azure-security-center"></a>Hur fungerar behörigheter i Azure Security Center?

I Azure Security Center tillämpas [rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md), vilket innebär att det finns [förinställda roller](../role-based-access-control/built-in-roles.md) som kan ges till användare, grupper och tjänster i Azure.

Security Center bedömer konfigurationen av dina resurser för att identifiera säkerhets problem och sårbarheter. I Security Center ser du bara information som rör en resurs när du tilldelas rollen som ägare, deltagare eller läsare för den prenumeration eller resurs grupp som en resurs tillhör.

Se [behörigheter i Azure Security Center](security-center-permissions.md) om du vill veta mer om roller och tillåtna åtgärder i Security Center.



## <a name="who-can-modify-a-security-policy"></a>Vem kan ändra en säkerhets princip?

Om du vill ändra en säkerhets princip måste du vara säkerhets administratör eller ägare eller deltagare i den prenumerationen.

Information om hur du konfigurerar en säkerhets princip finns [i ställa in säkerhets principer i Azure Security Center](tutorial-security-policy.md).