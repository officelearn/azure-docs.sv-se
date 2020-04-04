---
title: Multifaktorautentisering per användare – Azure Active Directory
description: Aktivera MFA genom att ändra användartillstånd i Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e955a3d10eea0a23154396a44fe6e1fc552a9ccb
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653477"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Så här kräver du tvåstegsverifiering för en användare

Du kan använda en av två metoder för att kräva tvåstegsverifiering, som båda kräver att du använder ett globalt administratörskonto. Det första alternativet är att aktivera varje användare för Azure Multi-Factor Authentication (MFA). När användare aktiveras individuellt utför de tvåstegsverifiering varje gång de loggar in (med vissa undantag, till exempel när de loggar in från betrodda IP-adresser eller när funktionen _för ihågkomna enheter_ är aktiverad). Det andra alternativet är att ställa in en princip för villkorlig åtkomst som kräver tvåstegsverifiering under vissa villkor.

> [!TIP]
> Aktivera Azure Multi-Factor Autentisering med principer för villkorlig åtkomst är den rekommenderade metoden. Det rekommenderas inte längre att ändra användartillstånd om inte dina licenser innehåller villkorlig åtkomst eftersom det kräver att användarna utför MFA varje gång de loggar in.

## <a name="choose-how-to-enable"></a>Välj hur du vill aktivera

**Aktiveras genom att ändra användartillstånd** - Detta är den traditionella metoden för att kräva tvåstegsverifiering och diskuteras i den här artikeln. Det fungerar med både Azure MFA i molnet och Azure MFA Server. Med den här metoden krävs att användare utför tvåstegsverifiering **varje gång** de loggar in och åsidosätter principer för villkorlig åtkomst.

**Aktiverad av principen villkorlig åtkomst** – Det här är det mest flexibla sättet att aktivera tvåstegsverifiering för användarna. Aktivera med principen villkorlig åtkomst fungerar bara för Azure MFA i molnet och är en premiumfunktion i Azure AD. Mer information om den här metoden finns i [Distribuera molnbaserad Azure Multi-Factor Authentication](howto-mfa-getstarted.md).

**Aktiverad av Azure AD Identity Protection** - Den här metoden använder riskprincipen för Azure AD Identity Protection för att kräva tvåstegsverifiering endast baserat på inloggningsrisk för alla molnprogram. Den här metoden kräver Azure Active Directory P2-licensiering. Mer information om den här metoden finns i [Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> Mer information om licenser och priser finns på prissidorna [för Azure AD-](https://azure.microsoft.com/pricing/details/active-directory/
) och [MultiFaktorautentisering.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

## <a name="enable-azure-mfa-by-changing-user-state"></a>Aktivera Azure MFA genom att ändra användartillstånd

Användarkonton i Azure Multi-Factor Authentication har följande tre olika tillstånd:

> [!IMPORTANT]
> Om du aktiverar Azure MFA via en princip för villkorlig åtkomst ändras inte användarens tillstånd. Oroa dig inte användare verkar inaktiverade. Villkorlig åtkomst ändrar inte tillståndet. **Organisationer bör inte aktivera eller tvinga användare om de använder principer för villkorlig åtkomst.**

| Status | Beskrivning | Appar som inte är webbläsare påverkas | Webbläsarappar påverkas | Modern autentisering påverkas |
|:---:| --- |:---:|:--:|:--:|
| Disabled | Standardtillståndet för en ny användare som inte är registrerad i Azure MFA. | Inga | Inga | Inga |
| Enabled | Användaren har registrerats i Azure MFA, men har inte registrerats. De får en uppmaning om att registrera sig nästa gång de loggar in. | Nej.  De fortsätter att fungera tills registreringsprocessen är klar. | Ja. När sessionen har gått ut krävs Azure MFA-registrering.| Ja. När åtkomsttoken har gått ut krävs Azure MFA-registrering. |
| Enforced | Användaren har registrerats och har slutfört registreringsprocessen för Azure MFA. | Ja. Appar kräver applösenord. | Ja. Azure MFA krävs vid inloggning. | Ja. Azure MFA krävs vid inloggning. |

En användares tillstånd återspeglar om en administratör har registrerat dem i Azure MFA och om de slutförde registreringsprocessen.

Alla användare börjar *inaktiveras*. När du registrerar användare i Azure MFA ändras deras tillstånd till *Aktiverad*. När aktiverade användare loggar in och slutför registreringsprocessen ändras deras tillstånd till *Tvingande*.

> [!NOTE]
> Om MFA återaktiverats på ett användarobjekt som redan har registreringsinformation, till exempel telefon eller e-post, måste administratörer ha den användaren registrera MFA via Azure-portalen eller PowerShell. Om användaren inte registrerar om, övergår inte deras MFA-tillstånd från *Aktiverat* till *Verkställt* i MFA-hanteringsgränssnittet.

### <a name="view-the-status-for-a-user"></a>Visa status för en användare

Följ följande steg för att komma åt sidan där du kan visa och hantera användartillstånd:

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör.
2. Sök efter och välj *Azure Active Directory*. Välj **Användare** > **Alla användare**.
3. Välj **Multifaktorautentisering**. Du kan behöva bläddra till höger för att se det här menyalternativet. Välj exempel skärmdump nedan för att se hela Azure portal fönster och menyplats:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Välj Multifaktorautentisering i fönstret Användare i Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
4. En ny sida som visar användartillståndet öppnas.
   ![användarstatus för multifaktorautentisering - skärmdump](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Ändra status för en användare

1. Använd föregående steg för att komma till sidan Azure Multi-Factor Authentication **users.**
2. Hitta den användare som du vill aktivera för Azure MFA. Du kan behöva ändra vyn högst upp.
   ![Välj den användare som ska ändra status för på fliken Användare](./media/howto-mfa-userstates/enable1.png)
3. Markera rutan bredvid namnet.
4. Till höger väljer du **Aktivera** eller **Inaktivera**under **snabba steg**.
   ![Aktivera markerad användare genom att klicka på Aktivera på snabbstegsmenyn](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Aktiverade* användare växlas automatiskt till *Tvingande när* de registrerar sig för Azure MFA. Ändra inte användartillståndet manuellt till *Verkställt*.

5. Bekräfta ditt val i popup-fönstret som öppnas.

När du har aktiverat användare meddelar du dem via e-post. Säg att de blir ombedda att registrera sig nästa gång de loggar in. Om din organisation använder appar som inte är webbläsare och som inte stöder modern autentisering måste de också skapa applösenord. Du kan också inkludera en länk till [slutanvändarhandboken](../user-help/multi-factor-authentication-end-user.md) för Azure MFA för att hjälpa dem att komma igång.

### <a name="use-powershell"></a>Använd PowerShell

Om du vill ändra användartillståndet med `$st.State`Hjälp av Azure AD [PowerShell](/powershell/azure/overview)ändrar du . Det finns tre möjliga tillstånd:

* Enabled
* Enforced
* Disabled  

Flytta inte användare direkt till tillståndet *Tvingande.* Om du gör det slutar appar som inte är webbläsarbaserade att fungera eftersom användaren inte har gått igenom Azure MFA-registrering och fått ett [applösenord](howto-mfa-mfasettings.md#app-passwords).

Installera modulen först med hjälp av:

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> Glöm inte att ansluta först med **Connect-MsolService**

   ```PowerShell
   Connect-MsolService
   ```

Det här exemplet powershell-skriptet aktiverar MFA för en enskild användare:

   ```PowerShell
   Import-Module MSOnline
   Connect-MsolService
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

Att använda PowerShell är ett bra alternativ när du behöver massaktivera användare. Som ett exempel loopar följande skript igenom en lista över användare och aktiverar MFA på sina konton:

   ```PowerShell
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

Om du vill inaktivera MFA använder du det här skriptet:

   ```PowerShell
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationRequirements @()
   ```

som också kan förkortas till:

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Konvertera användare från MFA per användare till villkorsstyrd åtkomstbaserad MFA

Följande PowerShell kan hjälpa dig att göra konverteringen till villkorlig åtkomstbaserad Azure Multi-Factor-autentisering.

Kör det här PowerShell i ett ISE-fönster eller spara som ett . PS1-filen ska köras lokalt.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Vi har nyligen ändrat beteende och PowerShell skript ovan i enlighet därmed. Tidigare sparade skriptet mfa-metoderna, inaktiverade MFA och återställde metoderna. Detta är inte längre nödvändigt nu när standardbeteendet för inaktivera inte rensar metoderna.
>
> Om MFA återaktiverats på ett användarobjekt som redan har registreringsinformation, till exempel telefon eller e-post, måste administratörer ha den användaren registrera MFA via Azure-portalen eller PowerShell. Om användaren inte registrerar om, övergår inte deras MFA-tillstånd från *Aktiverat* till *Verkställt* i MFA-hanteringsgränssnittet.

## <a name="next-steps"></a>Nästa steg

* Varför blev en användare ombedd att utföra MFA? Se avsnittet [Azure AD-inloggningsrapport i dokumentet Rapporter i Azure Multi-Factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).
* Information om hur du konfigurerar ytterligare inställningar som betrodda IPs, anpassade röstmeddelanden och bedrägerivarningar finns i artikeln [Konfigurera Azure Multi-Factor Authentication settings](howto-mfa-mfasettings.md)
* Information om hur du hanterar användarinställningar för Azure Multi-Factor Authentication finns i artikeln [Hantera användarinställningar med Azure Multi-Factor Authentication i molnet](howto-mfa-userdevicesettings.md)
