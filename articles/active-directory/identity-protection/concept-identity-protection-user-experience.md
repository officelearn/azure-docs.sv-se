---
title: Användar upplevelser med Azure AD Identity Protection
description: Användar upplevelse för Azure AD Identity Protection
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
ms.openlocfilehash: 7d3315a8d6bca9406461d0082ed85bbefc9459f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88950212"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Användar upplevelser med Azure AD Identity Protection

Med Azure Active Directory Identity Protection kan du:

* Kräv att användare registrerar sig för Azure Multi-Factor Authentication (MFA)
* Automatisera reparationer av riskfyllda inloggningar och komprometterade användare

Alla identitets skydds principer påverkar inloggnings upplevelsen för användarna. Att tillåta användare att registrera sig för och använda verktyg som Azure MFA och lösen ords återställning via självbetjäning kan minska påverkan. Dessa verktyg tillsammans med lämpliga princip val ger användare ett självreparerande alternativ när de behöver det.

## <a name="multi-factor-authentication-registration"></a>Multi-Factor Authentication-registrering

Att aktivera identitets skydds principen som kräver Multi-Factor Authentication-registrering och rikta in dig på alla dina användare, ser till att de har möjlighet att använda Azure MFA för att själv åtgärda i framtiden. Genom att konfigurera den här principen får användarna en 14-dagars period där de kan välja att registrera sig och i slutet tvingas registrera sig. Användarens upplevelse beskrivs nedan. Mer information finns i dokumentationen för slutanvändaren i artikeln [Översikt över tvåstegsverifiering för två faktorer och ditt arbets-eller skol konto](../user-help/multi-factor-authentication-end-user-first-time.md).

### <a name="registration-interrupt"></a>Registrerings avbrott

1. Vid inloggning till ett Azure AD-integrerat program får användaren ett meddelande om kravet på att konfigurera kontot för Multi-Factor Authentication. Den här principen aktive ras också i Windows 10 out of Box-upplevelsen för nya användare med en ny enhet.
   
    ![Mer information krävs](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. Slutför de guidade stegen för att registrera dig för Azure Multi-Factor Authentication och slutför inloggningen.

## <a name="risky-sign-in-remediation"></a>Riskfylld inloggnings reparation

När en administratör har konfigurerat en princip för inloggnings risker, meddelas de berörda användarna när de försöker logga in och utlösa risk nivån för principer. 

### <a name="risky-sign-in-self-remediation"></a>Själv reparation av riskfyllda inloggningar

1. Användaren informeras om att något ovanlig upptäcktes om deras inloggning, till exempel att logga in från en ny plats, enhet eller app.
   
    ![Någon ovanlig prompt](./media/concept-identity-protection-user-experience/120.png)

1. Användaren måste bevisa sin identitet genom att slutföra Azure MFA med en av deras tidigare registrerade metoder. 

### <a name="risky-sign-in-administrator-unblock"></a>Blockering av riskfylld inloggnings administratör

Administratörer kan välja att blockera användare vid inloggning, beroende på deras risk nivå. Slutanvändare måste kontakta sin IT-personal för att bli avblockerade, eller så kan de försöka logga in från en välbekant plats eller enhet. Själv reparation genom att utföra Multi-Factor Authentication är inte ett alternativ i det här fallet.

![Blockerad av inloggnings risk princip](./media/concept-identity-protection-user-experience/200.png)

IT-personalen kan följa anvisningarna i avsnittet [avblockera användare](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) för att tillåta att användare loggar in igen.

## <a name="risky-user-remediation"></a>Riskfyllda användar åtgärder

När en användar risk princip har kon figurer ATS måste användare som uppfyller sannolikheten för risk nivån gå igenom användarnas komprometterande återställnings flöde innan de kan logga in. 

### <a name="risky-user-self-remediation"></a>Själv reparation av riskfyllda användare

1. Användaren informeras om att deras konto säkerhet är utsatt för risk på grund av misstänkt aktivitet eller läcka autentiseringsuppgifter.
   
    ![Åtgärder](./media/concept-identity-protection-user-experience/101.png)

1. Användaren måste bevisa sin identitet genom att slutföra Azure MFA med en av deras tidigare registrerade metoder. 
1. Användaren måste slutligen ändra sitt lösen ord med hjälp av lösen ords återställning via självbetjäning eftersom någon annan kan ha haft åtkomst till sitt konto.

## <a name="risky-sign-in-administrator-unblock"></a>Blockering av riskfylld inloggnings administratör

Administratörer kan välja att blockera användare vid inloggning, beroende på deras risk nivå. Slutanvändare måste kontakta sin IT-personal för att få hjälp med blockeringen. Självbetjäning genom att utföra Multi-Factor Authentication och lösen ords återställning via självbetjäning är inte ett alternativ i det här fallet.

![Blockerad av användar risk princip](./media/concept-identity-protection-user-experience/104.png)

IT-personalen kan följa anvisningarna i avsnittet [avblockera användare](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) för att tillåta att användare loggar in igen.

## <a name="see-also"></a>Se även

- [Åtgärda risker och avblockera användare](howto-identity-protection-remediate-unblock.md)

- [Identitetsskydd för Azure Active Directory](./overview-identity-protection.md)