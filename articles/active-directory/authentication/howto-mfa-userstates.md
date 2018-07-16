---
title: Användarstatus för Microsoft Azure Multi-Factor Authentication
description: Läs mer om användarstatus i Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/26/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 6945966d4a701ea6e2684b7da766c8b6c9f9a283
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049056"
---
# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>Hur du kräver tvåstegsverifiering för en användare eller grupp

Du kan ta två metoder för att kräva tvåstegsverifiering. Det första alternativet är att varje användare för Azure Multi-Factor Authentication (MFA). När användare är aktiverade individuellt, de utför en tvåstegsverifiering varje gång de loggar in (med vissa undantag, till exempel när de loggar in från betrodda IP-adresser eller när den _sparas enheter_ funktionen är aktiverad). Det andra alternativet är att ställa in en princip för villkorlig åtkomst som kräver tvåstegsverifiering under vissa omständigheter.

>[!TIP] 
>Välj någon av följande metoder för att kräva tvåstegsverifiering, inte båda. Aktivera en användare för Azure Multi-Factor Authentication åsidosätter eventuella principer för villkorlig åtkomst.

## <a name="which-option-is-right-for-you"></a>Vilket alternativ som passar dig?

**Aktivera Azure Multi-Factor Authentication genom att ändra användarens tillstånd** är den traditionella metoden för att kräva tvåstegsverifiering. Den fungerar för både Azure MFA i molnet och Azure MFA-servern. Alla användare som du har aktiverat tvåstegsverifiering varje gång de loggar in. Aktivera en användare åsidosätter eventuella principer för villkorlig åtkomst som kan påverka den användaren. 

**Aktivera Azure Multi-Factor Authentication med en princip för villkorlig åtkomst** är ett mer flexibelt sätt för att kräva tvåstegsverifiering. Den fungerar endast för Azure MFA i molnet, men och _villkorlig åtkomst_ är en [betald funktion i Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features). Du kan skapa principer för villkorlig åtkomst som gäller för grupper som enskilda användare. Grupper med hög risk kan ges fler begränsningar än med låg risk grupper eller tvåstegsverifiering kan krävs endast för appar med hög risk och hoppades över för låg risk som. 

Båda alternativen Uppmana användarna att registrera dig för Azure Multi-Factor Authentication första gången de loggar in när kraven har aktiverat. Båda alternativen också fungera med de konfigurerbara [Azure Multi-Factor Authentication-inställningar](howto-mfa-mfasettings.md).

## <a name="enable-azure-mfa-by-changing-user-status"></a>Aktivera Azure MFA genom att ändra användarstatus

Användarkonton i Azure Multi-Factor Authentication har följande tre skilda lägen:

| Status | Beskrivning | Icke-webbläsarappar som påverkas | Webbläsarbaserade appar som påverkas | Modern autentisering som påverkas |
|:---:|:---:|:---:|:--:|:--:|
| Har inaktiverats |Standardläget för en ny användare som inte har registrerats i Azure MFA. |Nej |Nej |Nej |
| Aktiverad |Användaren har registrerats i Azure MFA, men har inte registrerats. De får en uppmaning att registrera dig nästa gång de loggar in. |Nej.  De fortsätter att fungera tills registreringen har slutförts. | Ja. När sessionen har gått ut, krävs Azure MFA-registrering.| Ja. När åtkomsttoken upphört att gälla krävs Azure MFA-registrering. |
| Framtvingat |Användaren har registrerats och har slutfört registreringsprocessen för Azure MFA. |Ja.  Appar kräver applösenord. |Ja. Azure MFA krävs vid inloggning. | Ja. Azure MFA krävs vid inloggning. |

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

   >[!TIP]
   >*Aktiverad* användare automatiskt växlas till *tvingande* när de registrerar sig för Azure MFA. Gör inte manuellt ändra användartillståndet ska *tvingande*. 

5. Bekräfta dina val i popup-fönstret som öppnas. 

När du aktiverar användare får du meddela dem via e-post. Berätta för dem att de uppmanas att registrera nästa gång de loggar in. Om din organisation använder icke-webbläsarbaserade appar som inte stöder modern autentisering, måste de också skapa applösenord används. Du kan även inkludera en länk till den [guiden för Azure MFA-slutanvändare](../user-help/multi-factor-authentication-end-user.md) som hjälper dem att komma igång.

### <a name="use-powershell"></a>Använd PowerShell
Ändra användartillstånd med hjälp av [Azure AD PowerShell](/powershell/azure/overview), ändra `$st.State`. Det finns tre möjliga tillstånd:

* Aktiverad
* Enforced
* Har inaktiverats  

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

## <a name="enable-azure-mfa-with-a-conditional-access-policy"></a>Aktivera Azure MFA med principer för villkorlig åtkomst

_Villkorlig åtkomst_ är en betald funktion i Azure Active Directory, med många konfigurationsalternativ. De här stegen beskriver ett sätt att skapa en princip. Mer information finns i avsnittet om [villkorlig åtkomst i Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Gå till **Azure Active Directory** > **villkorlig åtkomst**.
3. Välj **ny princip**.
4. Under **tilldelningar**väljer **användare och grupper**. Använd den **inkludera** och **undanta** flikar för att ange vilka användare och grupper i principen hanterar.
5. Under **tilldelningar**väljer **Molnappar**. Välja att inkludera **alla molnappar**.
6. Under **åtkomstkontroller**väljer **bevilja**. Välj **kräva multifaktorautentisering**.
7. Aktivera **aktiverar principen** till **på**, och välj sedan **spara**.

De andra alternativen i principen för villkorlig åtkomst ger dig möjligheten att ange exakt när tvåstegs-verifiering krävs. Exempel: du kan göra en princip som den här: när leverantörer försöker komma åt appen inköp från ej betrodda nätverk på enheter som inte är ansluten till domänen, kräva tvåstegsverifiering. 

## <a name="next-steps"></a>Nästa steg

- Få tips om den [bästa praxis för villkorlig åtkomst](../active-directory-conditional-access-best-practices.md).

- Hantera Azure Multi-Factor Authentication-inställningar för [användarna och deras enheter](howto-mfa-userdevicesettings.md).
