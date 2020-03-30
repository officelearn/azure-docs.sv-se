---
title: Azure AD-principer för identitetsskydd
description: Identifiera de tre principer som är aktiverade med identitetsskydd
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a44e32efa3e38cf9c4df7dc00e3189c129db418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72887421"
---
# <a name="identity-protection-policies"></a>Principer om identitetsskydd

Azure Active Directory Identity Protection innehåller tre standardprinciper som administratörer kan välja att aktivera. Dessa principer omfattar begränsad anpassning men gäller för de flesta organisationer. Alla principer gör det möjligt att utesluta användare, till exempel din [nödsituationsåtkomst eller administratörskonton för brytglas.](../users-groups-roles/directory-emergency-access.md)

![Principer om identitetsskydd](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Azure MFA-registreringsprincip

Identity Protection kan hjälpa organisationer att distribuera Azure Multi-Factor Authentication (MFA) med hjälp av en princip för villkorlig åtkomst som kräver registrering vid inloggning. Att aktivera den här principen är ett bra sätt att se till att nya användare i organisationen har registrerat sig för MFA första dagen. Multifaktorautentisering är en av självkorrigerande metoder för riskhändelser inom Identity Protection. Självkorrigerande gör det möjligt för användarna att vidta åtgärder på egen hand för att minska hjälpdesk-samtalsvolymen.

Mer information om Azure Multi-Factor Authentication finns i artikeln, [How it works: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Riskpolicy för inloggning

Identity Protection analyserar signaler från varje inloggning, både i realtid och offline, och beräknar en riskpoäng baserat på sannolikheten för att inloggningen inte utfördes av användaren. Administratörer kan fatta ett beslut baserat på den här riskpoängsignalen för att tvinga fram organisationskrav. Administratörer kan välja att blockera åtkomst, tillåta åtkomst eller tillåta åtkomst men kräver multifaktorautentisering.

Om risk upptäcks kan användare utföra multifaktorautentisering för att själv åtgärda och stänga den riskfyllda inloggningshändelsen för att förhindra onödigt brus för administratörer.

> [!NOTE] 
> Användare måste ha registrerat sig tidigare för Azure Multi-Factor Authentication innan de utlöser principen för inloggningsrisk.

### <a name="custom-conditional-access-policy"></a>Anpassad princip för villkorlig åtkomst

Administratörer kan också välja att skapa en anpassad princip för villkorlig åtkomst, inklusive inloggningsrisk som ett tilldelningsvillkor. Mer information om villkorlig åtkomst finns i artikeln, [Vad är villkorlig åtkomst?](../conditional-access/overview.md)

![Princip för anpassad princip för in-inloggning för villkorlig åtkomst](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Policy för användarrisk

Identity Protection kan beräkna vad den anser är normalt för en användares beteende och använda det för att basera beslut för sin risk. Användarrisk är en beräkning av sannolikheten för att en identitet har komprometterats. Administratörer kan fatta ett beslut baserat på den här riskpoängsignalen för att tvinga fram organisationskrav. Administratörer kan välja att blockera åtkomst, tillåta åtkomst eller tillåta åtkomst men kräver en lösenordsändring med [azure ad-självbetjäningslösenordsåterställning](../authentication/howto-sspr-deployment.md).

Om risk upptäcks kan användare utföra återställning av lösenord för självbetjäning för att själv åtgärda och stänga användarriskhändelsen för att förhindra onödigt brus för administratörer.

> [!NOTE] 
> Användare måste tidigare ha registrerat sig för återställning av lösenord för självbetjäning innan användarriskprincipen utlöses.

## <a name="next-steps"></a>Nästa steg

- [Aktivera azure ad-återställning av lösenord med självbetjäning](../authentication/howto-sspr-deployment.md)

- [Aktivera Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

- [Aktivera registreringsprincipen för multifaktorautentisering i Azure](howto-identity-protection-configure-mfa-policy.md)

- [Aktivera principer för inloggning och användarrisk](howto-identity-protection-configure-risk-policies.md)
