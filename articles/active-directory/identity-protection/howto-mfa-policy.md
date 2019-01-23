---
title: Så här konfigurerar du registreringsprincip för multi-Factor authentication i Azure Active Directory Identity Protection | Microsoft Docs
description: Lär dig hur du konfigurerar registreringsprincip för multifaktorautentisering i Azure AD Identity Protection.
services: active-directory
keywords: Azure active directory identity protection kan cloud app discovery, hantering av program, säkerhet, risk, risknivå, säkerhetsproblem, säkerhetsprincip
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 47c742e486c01d2861889419a56c252e74f1e316
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54469839"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>Hur: Konfigurera registreringsprincip för multi-Factor authentication

Azure AD Identity Protection hjälper dig att hantera lansering av registrering för multifaktorautentisering (MFA) genom att konfigurera en princip. Den här artikeln förklarar vad principen kan användas för ett så här konfigurerar du den.

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>Vad är registreringsprincip för multi-Factor authentication?

Azure Multi-Factor authentication är en metod för att verifiera vem du är som kräver användning av mer än bara ett användarnamn och lösenord. Det ger ett andra säkerhetslager till användarinloggningar och transaktioner.  

Vi rekommenderar att du Kräv Azure Multi-Factor authentication för användarinloggningar eftersom den:

- Du får stark autentisering med en rad enkla verifieringsalternativ

- Spelar en viktig roll i att förbereda din organisation för att skydda och återställa från kontot kompromisser


Mer information finns i [vad är Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md)


## <a name="how-do-i-access-the-mfa-registration-policy"></a>Hur kommer jag åt MFA-registreringsprincip?
   
MFA-registreringsprincip finns i den **konfigurera** avsnittet på den [Azure AD Identity Protection-sidan](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![MFA-principen](./media/howto-mfa-policy/1014.png)




## <a name="policy-settings"></a>Principinställningar

När du konfigurerar principen inloggningsrisk, måste du ange:

- Användare och grupper som principen gäller för:

    ![Användare och grupper](./media/howto-mfa-policy/11.png)

- Vilken typ av åtkomst som du vill ska tillämpas:  

    ![Användare och grupper](./media/howto-mfa-policy/12.png)

- Tillståndet för din princip:

    ![Tillämpa princip](./media/howto-mfa-policy/14.png)


Dialogrutan princip konfiguration får du ett alternativ för att beräkna effekten av din konfiguration.

![Uppskattad påverkan](./media/howto-mfa-policy/15.png)




## <a name="user-experience"></a>Användarupplevelse


En översikt över relaterade användarupplevelsen finns:

* [Flöde för registrering av multifaktorautentisering](flows.md#multi-factor-authentication-registration).  
* [Logga in som inträffar med Azure AD Identity Protection](flows.md).  



## <a name="next-steps"></a>Nästa steg

För att få en översikt över Azure AD Identity Protection kan se den [översikt över Azure AD Identity Protection](overview.md).
