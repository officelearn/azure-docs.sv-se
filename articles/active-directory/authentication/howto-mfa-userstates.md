---
title: Användarstatus för Microsoft Azure Multi-Factor Authentication
description: Läs mer om användarstatus i Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: b9f0571c88b6ec4aa9e3851d5bf618e5104b0652
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716254"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Hur du kräver tvåstegsverifiering för en användare

Du kan ta två metoder för att kräva tvåstegsverifiering. Det första alternativet är att varje användare för Azure Multi-Factor Authentication (MFA). När användare är aktiverade individuellt, de utför en tvåstegsverifiering varje gång de loggar in (med vissa undantag, till exempel när de loggar in från betrodda IP-adresser eller när den _sparas enheter_ funktionen är aktiverad). Det andra alternativet är att ställa in en princip för villkorlig åtkomst som kräver tvåstegsverifiering under vissa omständigheter.

> [!TIP]
> Välj någon av följande metoder för att kräva tvåstegsverifiering, inte båda. Aktivera en användare för Azure Multi-Factor Authentication åsidosätter eventuella principer för villkorlig åtkomst.

## <a name="choose-how-to-enable"></a>Välj hur du vill aktivera

**Aktiverat genom att ändra användarens tillstånd** – detta är den traditionella metoden för att kräva tvåstegsverifiering och beskrivs i den här artikeln. Det fungerar med både Azure MFA i molnet och Azure MFA-servern. Med den här metoden kräver att användare utför en tvåstegsverifiering **varje gång** de logga in och åsidosätter principer för villkorlig åtkomst.

Aktiverad av principen för villkorlig åtkomst – är detta det mest flexibla sättet att aktivera tvåstegsverifiering för dina användare. Aktivera med hjälp av endast princip för villkorlig åtkomst fungerar för Azure MFA i molnet och är en premiumfunktion i Azure AD. Mer information om den här metoden finns i [distribuera molnbaserade Azure Multi-Factor Authentication](howto-mfa-getstarted.md).

Aktiveras med Azure AD Identity Protection - används den här metoden Azure AD Identity Protection risken för att kräva tvåstegsverifiering endast utifrån inloggningsrisk för alla molnprogram. Den här metoden kräver Azure Active Directory P2 licensiering. Mer information om den här metoden finns i [Azure Active Directory Identity Protection](../identity-protection/overview.md#risky-sign-ins)

> [!Note]
> Mer information om licenser och priser finns på den [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) och [Multifaktorautentisering](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) prissidor.

## <a name="enable-azure-mfa-by-changing-user-status"></a>Aktivera Azure MFA genom att ändra användarstatus

Användarkonton i Azure Multi-Factor Authentication har följande tre skilda lägen:

| Status | Beskrivning | Icke-webbläsarappar som påverkas | Webbläsarbaserade appar som påverkas | Modern autentisering som påverkas |
|:---:|:---:|:---:|:--:|:--:|
| Disabled |Standardläget för en ny användare som inte har registrerats i Azure MFA. |Nej |Nej |Nej |
| Enabled |Användaren har registrerats i Azure MFA, men har inte registrerats. De får en uppmaning att registrera dig nästa gång de loggar in. |Nej.  De fortsätter att fungera tills registreringen har slutförts. | Ja. När sessionen har gått ut, krävs Azure MFA-registrering.| Ja. När åtkomsttoken upphört att gälla krävs Azure MFA-registrering. |
| Enforced |Användaren har registrerats och har slutfört registreringsprocessen för Azure MFA. |Ja. Appar kräver applösenord. |Ja. Azure MFA krävs vid inloggning. | Ja. Azure MFA krävs vid inloggning. |

Tillstånd för en användares återspeglar om en administratör har registrerats dem i Azure MFA och om de slutfört registreringsprocessen.

Alla användare börjar *inaktiverad*. När du registrerar användare i Azure MFA kan deras tillstånd ändras till *aktiverad*. När du aktiverade användare logga in och slutföra registreringen, deras status ändras till *tvingande*.  

### <a name="view-the-status-for-a-user"></a>Visa status för en användare

Använd följande steg för att komma åt sidan där du kan visa och hantera användartillstånd:

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Gå till **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj **Multifaktorautentisering**.
   ![Välj Multifaktorautentisering](./media/howto-mfa-userstates/selectmfa.png)
4. En ny sida som visar användarens tillstånd öppnas.
   ![Användarstatus för multi-Factor authentication – skärmbild](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Ändra status för en användare

1. Använd föregående steg för att komma till Azure Multi-Factor Authentication **användare** sidan.
2. Sök efter den användare som du vill aktivera för Azure MFA. Du kan behöva ändra vyn överst.
   ![Hitta användare – skärmbild](./media/howto-mfa-userstates/enable1.png)
3. Markera rutan bredvid användarens namn.
4. Till höger under **snabbsteg**, Välj **aktivera** eller **inaktivera**.
   ![Aktivera valda användare – skärmbild](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Aktiverad* användare automatiskt växlas till *tvingande* när de registrerar sig för Azure MFA. Gör inte manuellt ändra användartillståndet ska *tvingande*.

5. Bekräfta dina val i popup-fönstret som öppnas.

När du aktiverar användare får du meddela dem via e-post. Berätta för dem att de uppmanas att registrera nästa gång de loggar in. Om din organisation använder icke-webbläsarbaserade appar som inte stöder modern autentisering, måste de också skapa applösenord används. Du kan även inkludera en länk till den [guiden för Azure MFA-slutanvändare](../user-help/multi-factor-authentication-end-user.md) som hjälper dem att komma igång.

### <a name="use-powershell"></a>Använd PowerShell

Ändra användartillstånd med hjälp av [Azure AD PowerShell](/powershell/azure/overview), ändra `$st.State`. Det finns tre möjliga tillstånd:

* Enabled
* Enforced
* Disabled  

Flytta inte användare direkt till den *tvingande* tillstånd. Om du gör, icke-webbläsarbaserade appar slutar att fungera eftersom användaren inte har gått igenom Azure MFA-registrering och fick ett [applösenord](howto-mfa-mfasettings.md#app-passwords).

Med hjälp av PowerShell är ett bra alternativ när du vill aktivera många användare samtidigt. Skapa ett PowerShell.skript som igenom en lista över användare och gör det möjligt för dem:

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Följande skript är ett exempel:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera ytterligare inställningar som tillförlitliga IP-adresser, anpassade röstmeddelanden och bedrägerivarningar finns i artikeln [konfigurera Azure Multi-Factor Authentication-inställningar](howto-mfa-mfasettings.md)

Information om hur du hanterar användarinställningar för Azure Multi-Factor Authentication finns i artikeln [hantera användarinställningar med Azure Multi-Factor Authentication i molnet](howto-mfa-userdevicesettings.md)
