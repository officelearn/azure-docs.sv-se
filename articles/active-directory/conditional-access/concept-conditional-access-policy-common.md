---
title: Vanliga principer för villkorlig åtkomst – Azure Active Directory
description: Vanliga principer för villkorlig åtkomst för organisationer
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20b699f0672b49dd2f947e0cf00d0ffcef7961e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295310"
---
# <a name="common-conditional-access-policies"></a>Vanliga principer för villkorlig åtkomst

[Säkerhet standardvärden](../fundamentals/concept-fundamentals-security-defaults.md) är bra för vissa men många organisationer behöver mer flexibilitet än de erbjuder. Många behöver till exempel möjlighet att utesluta specifika konton som deras nödåtkomst eller riskdelningsadministration från principer för villkorlig åtkomst som kräver multifaktorautentisering. För dessa organisationer kan de gemensamma principer som refereras i den här artikeln vara till nytta.

![Principer för villkorlig åtkomst i Azure-portalen](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Konton för nödåtkomst

Mer information om konton för nödåtkomst och varför de är viktiga finns i följande artiklar: 

* [Hantera konton för nödåtkomst i Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Skapa en strategi för hantering av flexibel åtkomstkontroll med Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Typiska principer som distribueras av organisationer

* [Blockera äldre autentisering](howto-conditional-access-policy-block-legacy.md)\*
* [Kräv MFA för administratörer](howto-conditional-access-policy-admin-mfa.md)\*
* [Kräv MFA för Azure-hantering](howto-conditional-access-policy-azure-management.md)\*
* [Kräv MFA för alla användare](howto-conditional-access-policy-all-users-mfa.md)\*

\*Dessa fyra principer när de konfigureras tillsammans, skulle efterlikna funktioner som aktiveras av [säkerhet standard .](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="additional-policies"></a>Ytterligare policyer

* [Riskbaserade villkorsstyrd åtkomst (kräver Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Kräv betrodd plats för MFA-registrering](howto-conditional-access-policy-registration.md)
* [Blockera åtkomst efter plats](howto-conditional-access-policy-location.md)
* [Kräv kompatibel enhet](howto-conditional-access-policy-compliant-device.md)
* [Blockera åtkomst utom specifika appar](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>Nästa steg

- [Simulera inloggningsbeteende med verktyget Villkorlig åtkomst vad händer om.](troubleshoot-conditional-access-what-if.md)

- [Använd endast rapportläge för villkorlig åtkomst för att fastställa effekten av nya principbeslut.](concept-conditional-access-report-only.md)
