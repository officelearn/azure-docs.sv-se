---
title: Kom igång Azure MFA i molnet
description: Microsoft Azure Multi-Factor Authentication Kom igång med villkorlig åtkomst
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: d3c033efb034cbce2e439ba22097cafc029d8b63
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39620492"
---
# <a name="deploy-cloud-based-azure-multi-factor-authentication"></a>Distribuera molnbaserade Azure Multi-Factor Authentication

Det är enkelt att komma igång med Azure Multi-Factor Authentication (MFA Azure).

Innan du börjar bör du kontrollera att du har följande krav:

* Ett globalt administratörskonto i Azure AD-klienten. Om du behöver hjälp med att slutföra det här steget finns i vår artikel [Kom igång med Azure AD](../get-started-azure-ad.md)
* Rätt licenser till användare. Om du behöver mer information finns i avsnittet [så här hämtar du Azure Multi-Factor Authentication](concept-mfa-licensing.md)

## <a name="choose-how-to-enable"></a>Välj hur du vill aktivera

**Aktiverad av villkorliga åtkomstprinciper** -den här metoden beskrivs i den här artikeln. Det är det mest flexibla sättet att aktivera tvåstegsverifiering för dina användare. Aktivera med hjälp av endast princip för villkorlig åtkomst fungerar för Azure MFA i molnet och är en premiumfunktion i Azure AD.

Aktiveras med Azure AD Identity Protection - används den här metoden Azure AD Identity Protection risken för att kräva tvåstegsverifiering endast utifrån inloggningsrisk för alla molnprogram. Den här metoden kräver Azure Active Directory P2 licensiering. Mer information om den här metoden finns i [Azure Active Directory Identity Protection](../active-directory-identityprotection.md#risky-sign-ins)

Aktiverat genom att ändra användarens tillstånd - detta är den traditionella metoden för att kräva tvåstegsverifiering. Det fungerar med både Azure MFA i molnet och Azure MFA-servern. Med den här metoden kräver att användare utför en tvåstegsverifiering **varje gång** de logga in och åsidosätter principer för villkorlig åtkomst. Mer information om den här metoden finns i [kräva tvåstegsverifiering för en användare](howto-mfa-userstates.md)

> [!Note]
> Mer information om licenser och priser finns på den [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) och [Multifaktorautentisering](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) prissidor.

## <a name="choose-authentication-methods"></a>Välj autentiseringsmetoder

Aktivera minst en autentiseringsmetod för användarna utifrån organisationens krav. Vi hitta att Microsoft Authenticator-appen när aktiverad för användare ger den bästa användarupplevelsen. Om du vill förstå vilka metoder är tillgängliga och hur du konfigurerar dem finns i artikeln [vad är autentiseringsmetoder](concept-authentication-methods.md).

## <a name="get-users-to-enroll"></a>Få användarna att registrera

När du aktiverar principen för villkorlig åtkomst kan tvingas användarna att registrera nästa gång de använder en app som skyddas med principen. Om du aktiverar en princip som kräver MFA för alla användare på alla molnappar kan den här åtgärden orsaka problem för användarna och supportavdelningen. Rekommendationen är att be användare att registrera autentiseringsmetoder i förväg med registreringsportalen på [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup). Många organisationer hitta att skapa affischer, tabell-kort och e-postmeddelanden som hjälper dig att införa.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Aktivera Multi-Factor Authentication med villkorlig åtkomst

Logga in på den [Azure-portalen](https://portal.azure.com) med hjälp av ett globalt administratörskonto.

### <a name="choose-verification-options"></a>Välj verifieringsalternativ

Innan du aktiverar Azure Multi-Factor Authentication, måste din organisation bestämma vilka alternativ för verifiering som de gör. I den här övningen aktivera du anrop till telefon och text meddelandet till telefon, eftersom de allmänna alternativ för att de flesta ska kunna använda. Mer information om autentiseringsmetoder och deras användning finns i artikeln [vad är autentiseringsmetoder?](concept-authentication-methods.md)

1. Bläddra till **Azure Active Directory**, **användare**, **Multifaktorautentisering**
   ![åtkomst till Multi-Factor Authentication portalen från bladet för Azure AD-användare i Azure-portalen](media/howto-mfa-getstarted/users-mfa.png) 
2. I den nya fliken som öppnas bläddrar du till **tjänstinställningar**
3. Under **verifieringsalternativ**, markera följande kryssrutorna för metoder som är tillgängliga för användare
   * Samtal till telefon
   * Textmeddelande till telefon

   ![Konfigurera verifieringsmetoder i inställningar-fliken Multi-Factor Authentication-tjänsten](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

4. Klicka på **Spara**
5. Stäng den **tjänstinställningar** fliken

### <a name="create-conditional-access-policy"></a>Skapa princip för villkorlig åtkomst

1. Logga in på den [Azure-portalen](https://portal.azure.com) med hjälp av ett globalt administratörskonto.
1. Bläddra till **Azure Active Directory**, **Conditional access** (Villkorsstyrd åtkomst)
1. Välj **Ny princip**
1. Ange ett beskrivande namn för principen
1. Under **användare och grupper**
   * På den **inkludera** fliken den **alla användare** alternativknapp
   * REKOMMENDERAT: På den **undanta** fliken, markerar du kryssrutan för **användare och grupper** och välj en grupp som ska användas för undantag när användare inte har åtkomst till sina autentiseringsmetoder.
   * Klicka på **Klar**
1. Under **Molnappar**väljer den **alla molnappar** alternativknapp
   * Du kan också: På den **undanta** -fliken, Välj molnappar som din organisation inte kräver MFA för.
   * Klicka på **Klar**
1. Under **villkor** avsnittet
   * : Om du har aktiverat Azure Identity Protection, du kan välja att utvärdera inloggningsrisk som en del av principen.
   * Du kan också: Om du har konfigurerat betrodda platser eller namngivna platser, kan du ange om du vill inkludera eller exkludera dessa platser från principen.
1. Under **Bevilja** ser du till att alternativknappen **Bevilja åtkomst** har markerats
    * Markera rutan för **Kräv multifaktorautentisering**
    * Klicka på **Välj**
1. Hoppa över avsnittet **Session**
1. Ställ in växeln **Aktivera princip** till **På**
1. Klicka på **Skapa**

![Skapa en princip för villkorlig åtkomst för att aktivera MFA för användare av Azure i pilotgrupp](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

### <a name="test-azure-multi-factor-authentication"></a>Testa Azure Multi-Factor Authentication

Testa för att bekräfta att din princip för villkorlig åtkomst fungerar, logga in till en resurs som inte får kräva MFA och sedan till Azure-portalen som kräver MFA.

1. Öppna ett nytt webbläsarfönster i InPrivate- eller <inkognitoläge och gå till [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com).
   * Logga in med den testanvändare som skapades i avsnittet om förutsättningar i den här artikeln. Observera att den inte ska uppmana dig att slutföra MFA.
   * Stäng webbläsarfönstret
2. Öppna ett nytt webbläsarfönster i InPrivate- eller <inkognitoläge och gå till [https://portal.azure.com](https://portal.azure.com).
   * Logga in med den testanvändare som skapades i avsnittet om förutsättningar i den här artikeln. Observera att det nu ska krävas att du registrerar dig för och använder multifaktorautentisering.
   * Stäng webbläsarfönstret

## <a name="next-steps"></a>Nästa steg

Grattis, du har konfigurerat Azure Multi-Factor Authentication i molnet.

Om du vill konfigurera ytterligare inställningar som tillförlitliga IP-adresser, anpassade röstmeddelanden och bedrägerivarningar finns i artikeln [konfigurera Azure Multi-Factor Authentication-inställningar](howto-mfa-mfasettings.md)

Information om hur du hanterar användarinställningar för Azure Multi-Factor Authentication finns i artikeln [hantera användarinställningar med Azure Multi-Factor Authentication i molnet](howto-mfa-userdevicesettings.md)

[Aktivera konvergerade registrering för Azure Multi-Factor Authentication och Azure AD lösenordsåterställning via självbetjäning](concept-registration-mfa-sspr-converged.md)
