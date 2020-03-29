---
title: Licenskrav för att använda Privilegierad identitetshantering – Azure Active Directory | Microsoft-dokument
description: Beskriver licenskraven för att använda Azure AD Privileged Identity Management (PIM).
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
ms.date: 01/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70696cdb95fffc1e5faa46ca1b5f2180633ed63a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932325"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Licenskrav för att använda privilegierad identitetshantering

Om du vill använda AZURE Active Directory (Azure AD) Privilegierad identitetshantering (PIM) måste en katalog ha en giltig licens. Dessutom måste licenser tilldelas administratörer och relevanta användare. I den här artikeln beskrivs licenskraven för att använda Privilegierad identitetshantering.

## <a name="valid-licenses"></a>Giltiga licenser

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="how-many-licenses-must-you-have"></a>Hur många licenser måste du ha?

Kontrollera att katalogen har minst lika många Azure AD Premium P2-licenser som du har anställda som utför följande uppgifter:

- Användare som tilldelats som berättigade till Azure AD-roller som hanteras med PIM
- Användare som kan godkänna eller avvisa aktiveringsbegäranden i PIM
- Användare som tilldelats en Azure-resursroll med just-in-time- eller direct-tilldelningar (tidsbaserade)  
- Användare som tilldelats en åtkomstgranskning
- Användare som utför åtkomstgranskningar

Azure AD Premium P2-licenser krävs **inte** för följande uppgifter:

- Inga licenser krävs för användare med rollrollerna Global administratör eller Privilegierad rolladministratör som konfigurerar PIM, konfigurerar principer, tar emot aviseringar och konfigurerar åtkomstgranskningar.

Mer information om licenser finns i [Tilldela eller ta bort licenser med Azure Active Directory-portalen](../fundamentals/license-users-groups.md).

## <a name="example-license-scenarios"></a>Exempel på licensscenarier

Här är några exempel på licensscenarier som hjälper dig att avgöra hur många licenser du måste ha.

| Scenario | Beräkning | Antal licenser |
| --- | --- | --- |
| Woodgrove Bank har 10 administratörer för olika avdelningar och 2 globala administratörer som konfigurerar och hanterar PIM. De gör fem administratörer kvalificerade. | Fem licenser för administratörer som är berättigade | 5 |
| Graphic Design Institute har 25 administratörer varav 14 hanteras via PIM. Rollaktivering kräver godkännande och det finns tre olika användare i organisationen som kan godkänna aktiveringar. | 14 licenser för berättigade roller + tre godkännare | 17 |
| Contoso har 50 administratörer varav 42 hanteras via PIM. Rollaktivering kräver godkännande och det finns fem olika användare i organisationen som kan godkänna aktiveringar. Contoso gör också månatliga granskningar av användare som tilldelats administratörsroller och granskare är användarnas chefer som sex inte är i administratörsroller som hanteras av PIM. | 42 licenser för berättigade roller + fem godkännare + sex granskare | 53 |

## <a name="what-happens-when-a-license-expires"></a>Vad händer när en licens går ut?

Om en Azure AD Premium P2-, EMS E5- eller utvärderingslicens upphör att gälla är funktioner för privilegierad identitetshantering inte längre tillgängliga i din katalog:

- Permanenta rolltilldelningar till Azure AD-roller påverkas inte.
- Tjänsten Privilegierad identitetshantering i Azure-portalen, liksom Graph API-cmdlets och PowerShell-gränssnitt för privilegierad identitetshantering, kommer inte längre att vara tillgänglig för användare att aktivera privilegierade roller, hantera privilegierad åtkomst eller utföra åtkomstgranskningar av privilegierade roller.
- Kvalificerade rolltilldelningar av Azure AD-roller tas bort, eftersom användarna inte längre kan aktivera privilegierade roller.
- Alla pågående åtkomstgranskningar av Azure AD-roller avslutas och konfigurationsinställningar för privilegierad identitetshantering tas bort.
- Privilegierad identitetshantering skickar inte längre e-postmeddelanden om rolltilldelningsändringar.

## <a name="next-steps"></a>Nästa steg

- [Distribuera Privileged Identity Management](pim-deployment-plan.md)
- [Börja använda Privileged Identity Management](pim-getting-started.md)
- [Roller som du inte kan hantera i privilegierad identitetshantering](pim-roles.md)
