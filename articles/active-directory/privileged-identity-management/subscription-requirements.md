---
title: Licensiera kraven för att använda PIM - Azure Active Directory | Microsoft Docs
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
ms.subservice: pim
ms.date: 01/16/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40e16209a185623b6e15650f70141edd6394e337
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60437464"
---
# <a name="license-requirements-to-use-pim"></a>Licenskrav gäller för att använda PIM

En katalog måste ha en giltig licens för att använda Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Dessutom måste licenser tilldelas gruppen Administratörer och användare. Den här artikeln beskrivs licensieringskraven för att använda PIM.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill använda PIM måste din katalog ha ett av följande avgiftsbelagda eller utvärderingslicenser:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Mer information finns i [Vad är Azure Active Directory?](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Vilka användare måste ha licenser?

Varje administratör eller användare som interagerar med eller tar emot en förmån från PIM måste ha en licens. Exempel:

- Administratörer med Azure AD-roller som hanteras med hjälp av PIM
- Administratörer med Azure-resursroller hanteras med hjälp av PIM
- Administratörer som har tilldelats rollen privilegierad Rolladministratör
- Användare som tilldelats som berättigad till Azure AD-roller som hanteras med hjälp av PIM
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

- [Distribuera PIM](pim-deployment-plan.md)
- [Börja använda PIM](pim-getting-started.md)
- [Roller som du inte kan hantera i PIM](pim-roles.md)
