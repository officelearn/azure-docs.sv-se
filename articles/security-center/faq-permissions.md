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
ms.openlocfilehash: 66a260fbb03f770ee98ec29a5f5e15e3d7dd1310
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599397"
---
# <a name="permissions"></a>Behörigheter

## <a name="how-do-permissions-work-in-azure-security-center"></a>Hur fungerar behörigheter i Azure Security Center?

I Azure Security Center tillämpas [rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md), vilket innebär att det finns [förinställda roller](../role-based-access-control/built-in-roles.md) som kan ges till användare, grupper och tjänster i Azure.

Security Center utvärderar konfigurationen av dina resurser för att identifiera säkerhetsproblem och säkerhetsproblem. I Security Center kan se du bara information relaterad till en resurs när du har tilldelats rollen ägare, deltagare eller läsare för prenumeration eller resursgrupp som en resurs hör till.

Se [behörigheter i Azure Security Center](security-center-permissions.md) om du vill veta mer om roller och tillåtna åtgärder i Security Center.



## <a name="who-can-modify-a-security-policy"></a>Vem som kan ändra en säkerhetsprincip för?

Om du vill ändra en säkerhetsprincip måste du vara en administratör eller ägare eller deltagare i den aktuella prenumerationen.

Information om hur du konfigurerar en säkerhets princip finns [i ställa in säkerhets principer i Azure Security Center](tutorial-security-policy.md).