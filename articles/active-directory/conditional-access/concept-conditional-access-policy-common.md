---
title: Vanliga principer för villkorlig åtkomst – Azure Active Directory
description: Ofta använda principer för villkorlig åtkomst för organisationer
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfb5fb17abd5a433c177d3efc5a4f0a2cec3d4d7
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186129"
---
# <a name="common-conditional-access-policies"></a>Vanliga principer för villkorlig åtkomst

[Säkerhets inställningarna](../fundamentals/concept-fundamentals-security-defaults.md) är bra för vissa men många organisationer behöver större flexibilitet än de erbjuder. Många behöver till exempel möjlighet att utesluta vissa konton, t. ex. återställnings-eller brytar-och administrations konton från principer för villkorlig åtkomst som kräver Multi-Factor Authentication. För dessa organisationer kan de gemensamma principer som hänvisas till i den här artikeln användas.

![Principer för villkorlig åtkomst i Azure Portal](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Konton för nöd åtkomst

Mer information om konton för nöd åtkomst och varför de är viktiga finns i följande artiklar: 

* [Hantera konton för nöd åtkomst i Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Skapa en elastisk strategi för hantering av åtkomst kontroll med Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Vanliga principer som distribueras av organisationer

* [KRÄV MFA för administratörer](howto-conditional-access-policy-admin-mfa.md)\*
* [KRÄV MFA för hantering av Azure](howto-conditional-access-policy-azure-management.md) -\*
* [KRÄV MFA för alla användare](howto-conditional-access-policy-all-users-mfa.md)\*
* [Blockera äldre autentisering](howto-conditional-access-policy-block-legacy.md)\*
* [Riskfylld villkorlig åtkomst (kräver Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Kräv betrodd plats för MFA-registrering](howto-conditional-access-policy-registration.md)
* [Blockera åtkomst efter plats](howto-conditional-access-policy-location.md)
* [Kräv kompatibel enhet](howto-conditional-access-policy-compliant-device.md)

\* de här fyra principerna när de kon figurer ATS tillsammans, skulle efterlikna funktioner som är aktiverade av [säkerhets inställningar](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="next-steps"></a>Nästa steg

- [Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst.](troubleshoot-conditional-access-what-if.md)
- [Använd endast rapport läge för villkorlig åtkomst för att fastställa effekten av nya princip beslut.](concept-conditional-access-report-only.md)
