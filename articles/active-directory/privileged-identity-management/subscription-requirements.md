---
title: Licenskrav för att använda PIM - Azure | Microsoft Docs
description: Beskriver licenskraven som använder Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 01/16/2019
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: dd4fa72b3e0b57ab227146eae6e2c7d20d0ce47a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54424225"
---
# <a name="license-requirements-to-use-pim"></a>Licenskrav gäller för att använda PIM

En katalog måste ha en giltig licens för att använda Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Dessutom måste licenser tilldelas gruppen Administratörer och användare. Den här artikeln beskrivs licensieringskraven för att använda PIM.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda PIM måste din katalog ha ett av följande avgiftsbelagda eller utvärderingslicenser:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Mer information finns i [Vad är Azure Active Directory?](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Vilka användare måste ha licenser?

Varje administratör eller användare som interagerar med eller tar emot en förmån från PIM måste ha en licens. Exempel:

- Administratörer med Azure AD-roller som hanteras med hjälp av PIM
- Administratörer med Azure-resursroller hanteras med hjälp av PIM
- Administratörer som har tilldelats rollen privilegierad Rolladministratör
- Användare som är tilldelade som kan hanteras med hjälp av PIM för katalogroller
- Användare kan godkänna/avvisa förfrågningar i PIM
- Användare som är tilldelade till en Azure-resurs-roll med just-in-time eller direkt (tidsbaserade) tilldelningar  
- Användare som är tilldelade till en åtkomstgranskning
- Användare som utför åtkomstgranskningar

Information om hur du tilldelar licenser till din använder finns i [tilldela eller ta bort licenser med hjälp av Azure Active Directory-portalen](../fundamentals/license-users-groups.md).

## <a name="what-happens-when-a-license-expires"></a>Vad händer när en licens har gått ut?

Om en Azure AD Premium P2 eller EMS E5 utvärderingslicensen upphör att gälla PIM-funktioner inte längre att vara tillgängliga i din katalog:

- Permanent rolltilldelningar till Azure AD-roller påverkas.
- PIM-tjänsten i Azure-portalen, som Graph API-cmdletar och PowerShell-gränssnitt i PIM, kommer inte längre vara tillgänglig för användare att aktivera Privilegierade roller, hantera privilegierad åtkomst eller utföra åtkomstgranskningar av Privilegierade roller.
- Berättigade rolltilldelningar för Azure AD-roller tas bort, eftersom användarna kommer inte längre att kunna aktivera Privilegierade roller.
- Alla pågående åtkomstgranskningar av Azure AD-roller kommer att avslutas och konfigurationsinställningar för PIM tas bort.
- PIM kommer inte längre skicka e-postmeddelanden på ändringarna för tilldelningen av rollen.

## <a name="next-steps"></a>Nästa steg

- [Börja använda PIM](pim-getting-started.md)
- [Roller som du inte kan hantera i PIM](pim-roles.md)
