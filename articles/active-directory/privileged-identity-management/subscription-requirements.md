---
title: Licens krav för att använda Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Beskriver licens kraven för att använda Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6e336ec40ba2b9f6d3018e6a4f5b2ac721077aa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895120"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Licens krav för att använda Privileged Identity Management

Om du vill använda Azure Active Directory (Azure AD) Privileged Identity Management (PIM) måste en katalog ha en giltig licens. Dessutom måste licenser tilldelas till administratörer och relevanta användare. I den här artikeln beskrivs licens kraven för att använda Privileged Identity Management.

## <a name="prerequisites"></a>Krav

Om du vill använda Privileged Identity Management måste katalogen ha någon av följande betalda eller utvärderings licenser:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

Mer information finns i [Vad är Azure Active Directory?](../fundamentals/active-directory-whatis.md).

## <a name="which-users-must-have-licenses"></a>Vilka användare måste ha licenser?

Varje administratör eller användare som interagerar med eller får en förmån från Privileged Identity Management måste ha en licens. Till exempel:

- Administratörer med Azure AD-roller som hanteras med PIM
- Administratörer med Azure-resurs roller som hanteras med PIM
- Administratörer som har tilldelats rollen privilegie rad roll administratör
- Användare som är behöriga för Azure AD-roller som hanteras med PIM
- Användare som kan godkänna/avvisa begär anden i PIM
- Användare som tilldelats en Azure-resurs roll med just-in-Time-eller Direct (tidsbaserad) tilldelningar  
- Användare som tilldelats åtkomst granskning
- Användare som utför åtkomst granskningar

Information om hur du tilldelar licenser till dina användnings områden finns i [tilldela eller ta bort licenser med hjälp av Azure Active Directory portalen](../fundamentals/license-users-groups.md).

## <a name="what-happens-when-a-license-expires"></a>Vad händer när en licens upphör att gälla?

Om en Azure AD Premium P2, EMS E5 eller utvärderings licens upphör att gälla, kommer Privileged Identity Management funktioner inte längre att vara tillgängliga i din katalog:

- Permanenta roll tilldelningar till Azure AD-roller kommer inte att påverkas.
- Privileged Identity Management tjänst i Azure Portal, samt Graph API-cmdletar och PowerShell-gränssnitt för Privileged Identity Management, kommer inte längre att vara tillgängliga för användare att aktivera privilegierade roller, hantera privilegie rad åtkomst eller utföra åtkomst granskningar av privilegierade roller.
- Kvalificerade roll tilldelningar för Azure AD-roller tas bort eftersom användarna inte längre kan aktivera privilegierade roller.
- Pågående åtkomst granskningar av Azure AD-roller kommer att avslutas och Privileged Identity Management konfigurations inställningar tas bort.
- Privileged Identity Management kommer inte längre att skicka e-postmeddelanden om roll tilldelnings ändringar.

## <a name="next-steps"></a>Nästa steg

- [Distribuera Privileged Identity Management](pim-deployment-plan.md)
- [Börja använda Privileged Identity Management](pim-getting-started.md)
- [Roller som du inte kan hantera i Privileged Identity Management](pim-roles.md)
