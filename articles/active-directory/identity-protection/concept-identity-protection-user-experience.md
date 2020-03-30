---
title: Användarupplevelser med Azure AD Identity Protection
description: Användarupplevelse av Azure AD Identity Protection
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
ms.openlocfilehash: cc10fb4f9894a355c9eed024ae9f87747214999b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72887005"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Användarupplevelser med Azure AD Identity Protection

Med Azure Active Directory Identity Protection kan du:

* Kräv att användare registrerar sig för Azure Multi-Factor Authentication (MFA)
* Automatisera reparation av riskfyllda inloggningar och komprometterade användare

Alla principer för identitetsskydd påverkar inloggningsupplevelsen för användare. Att tillåta användare att registrera sig för och använda verktyg som Azure MFA och självbetjäning lösenordsåterställning kan minska effekten. Dessa verktyg tillsammans med lämpliga politiska val ger användarna ett självkorrigerande alternativ när de behöver det.

## <a name="multi-factor-authentication-registration"></a>Registrering av multifaktorautentisering

Aktivera identitetsskyddsprincipen som kräver registrering av multifaktorautentisering och som riktar sig till alla dina användare, kommer att se till att de har möjlighet att använda Azure MFA för att själv åtgärda i framtiden. Genom att konfigurera den här principen får användarna en 14-dagarsperiod där de kan välja att registrera sig och i slutet tvingas registrera sig. Upplevelsen för användare beskrivs nedan. Mer information finns i slutanvändardokumentationen i artikeln [Översikt för tvåfaktorsverifiering och ditt arbets- eller skolkonto](../user-help/user-help-two-step-verification-overview.md).

### <a name="registration-interrupt"></a>Registrering avbryter

1. Vid inloggning till alla Azure AD-integrerade program får användaren ett meddelande om kravet på att konfigurera kontot för multifaktorautentisering. Den här principen utlöses också i Windows 10 Out of Box Experience för nya användare med en ny enhet.
   
    ![Mer information krävs](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. Slutför de guidade stegen för att registrera dig för Azure Multi-Factor Authentication och slutför din inloggning.

## <a name="risky-sign-in-remediation"></a>Riskfylld insanering

När en administratör har konfigurerat en princip för inloggningsrisker meddelas de berörda användarna när de försöker logga in och utlösa principrisknivån. 

### <a name="risky-sign-in-self-remediation"></a>Riskfylld inloggning självreparation

1. Användaren informeras om att något ovanligt har upptäckts om deras inloggning, till exempel logga in från en ny plats, enhet eller app.
   
    ![Något ovanligt snabbt](./media/concept-identity-protection-user-experience/120.png)

1. Användaren måste bevisa sin identitet genom att fylla i Azure MFA med en av sina tidigare registrerade metoder. 

### <a name="risky-sign-in-administrator-unblock"></a>Riskafyllt avblockeringsadministratör avblockerar

Administratörer kan välja att blockera användare vid inloggning beroende på deras risknivå. För att få avblockerad måste slutanvändarna kontakta sin IT-personal eller försöka logga in från en bekant plats eller enhet. Självåtgärd genom att utföra multifaktorautentisering är inte ett alternativ i det här fallet.

![Blockerad av inloggningsriskpolicy](./media/concept-identity-protection-user-experience/200.png)

IT-personal kan följa instruktionerna i avsnittet [Avblockera användare](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) så att användare kan logga in igen.

## <a name="risky-user-remediation"></a>Riskfyllda åtgärder för användare

När en användarriskprincip har konfigurerats måste användare som uppfyller sannolikheten för kompromettering av användarrisken gå igenom återställningsflödet för användare innan de kan logga in. 

### <a name="risky-user-self-remediation"></a>Riskarisk självreparation för användare

1. Användaren informeras om att deras kontosäkerhet är i fara på grund av misstänkt aktivitet eller läckta autentiseringsuppgifter.
   
    ![Åtgärder](./media/concept-identity-protection-user-experience/101.png)

1. Användaren måste bevisa sin identitet genom att fylla i Azure MFA med en av sina tidigare registrerade metoder. 
1. Slutligen är användaren tvungen att ändra sitt lösenord med självbetjäning lösenordsåterställning eftersom någon annan kan ha haft tillgång till sitt konto.

## <a name="risky-sign-in-administrator-unblock"></a>Riskafyllt avblockeringsadministratör avblockerar

Administratörer kan välja att blockera användare vid inloggning beroende på deras risknivå. För att få avblockerad måste slutanvändarna kontakta sin IT-personal. Självåtgärd genom att utföra multifaktorautentisering och återställning av lösenord för självbetjäning är inte ett alternativ i det här fallet.

![Blockerad av användarriskprincip](./media/concept-identity-protection-user-experience/104.png)

IT-personal kan följa instruktionerna i avsnittet [Avblockera användare](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) så att användare kan logga in igen.

## <a name="see-also"></a>Se även

- [Åtgärda risker och avblockera användare](howto-identity-protection-remediate-unblock.md)

- [Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md) 
