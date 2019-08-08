---
title: Användar tillstånd för Azure Multi-Factor Authentication – Azure Active Directory
description: Lär dig mer om användar tillstånd i Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98a339f3fe9d5318b71ef60ac916bc4dcc6112fb
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853743"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Så här kräver du tvåstegsverifiering för en användare

Du kan utföra en av två metoder för att kräva tvåstegsverifiering, som båda kräver att du använder ett globalt administratörs konto. Det första alternativet är att aktivera varje användare för Azure Multi-Factor Authentication (MFA). När användare Aktiver ATS individuellt utför de tvåstegsverifiering varje gång de loggar in (med vissa undantag, till exempel när de loggar in från betrodda IP-adresser eller när funktionen _sparade enheter_ aktive RAS). Det andra alternativet är att konfigurera en princip för villkorlig åtkomst som kräver tvåstegsverifiering under vissa förhållanden.

> [!TIP]
> Den rekommenderade metoden är att aktivera Azure Multi-Factor Authentication med principer för villkorlig åtkomst. Att ändra användar tillstånd rekommenderas inte längre om licenserna inte innehåller villkorlig åtkomst eftersom det krävs att användarna utför MFA varje gång de loggar in.

## <a name="choose-how-to-enable"></a>Välj hur du vill aktivera

**Aktive rad genom att ändra användar tillstånd** – det här är den traditionella metoden för att kräva tvåstegsverifiering och beskrivs i den här artikeln. Det fungerar med både Azure MFA i molnet och Azure MFA-servern. Om du använder den här metoden måste användarna utföra tvåstegsverifiering **varje gång** de loggar in och åsidosätter principer för villkorlig åtkomst.

Aktive rad med princip för villkorlig åtkomst – det här är det mest flexibla sättet att aktivera tvåstegsverifiering för dina användare. Aktivering med principen för villkorlig åtkomst fungerar bara för Azure MFA i molnet och är en Premium funktion i Azure AD. Mer information om den här metoden finns i [distribuera molnbaserad Azure Multi-Factor Authentication](howto-mfa-getstarted.md).

Aktive rad av Azure AD Identity Protection – med den här metoden används principen Azure AD Identity Protection risk för att kräva tvåstegsverifiering endast baserat på inloggnings risk för alla moln program. Den här metoden kräver Azure Active Directory P2-licensiering. Du hittar mer information om den här metoden i [Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> Mer information om licenser och priser finns på [pris sidorna för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) och [Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) .

## <a name="enable-azure-mfa-by-changing-user-state"></a>Aktivera Azure MFA genom att ändra användar tillstånd

Användar konton i Azure Multi-Factor Authentication har följande tre distinkta tillstånd:

| Status | Beskrivning | Icke-webbläsarbaserade appar som påverkas | Webbläsarbaserade appar som påverkas | Modern autentisering påverkas |
|:---:|:---:|:---:|:--:|:--:|
| Inaktiverad |Standard läget för en ny användare som inte har registrerats i Azure MFA. |Nej |Nej |Nej |
| Aktiverad |Användaren har registrerats i Azure MFA, men har inte registrerats. De får ett meddelande om att registrera sig nästa gången de loggar in. |Nej.  De fortsätter att fungera tills registrerings processen har slutförts. | Ja. När sessionen har gått ut krävs Azure MFA-registrering.| Ja. När åtkomsttoken upphör att gälla krävs Azure MFA-registrering. |
| Framtvingat |Användaren har registrerats och slutfört registrerings processen för Azure MFA. |Ja. Appar kräver applösenord. |Ja. Azure MFA krävs vid inloggning. | Ja. Azure MFA krävs vid inloggning. |

En användares tillstånd visar om en administratör har registrerat dem i Azure MFA och om de har slutfört registrerings processen.

Alla användare börjar varainaktiverade. När du registrerar användare i Azure MFA ändras deras status till *aktive rad*. När aktiverade användare loggar in och slutför registrerings processen ändras deras status till tvingande.  

### <a name="view-the-status-for-a-user"></a>Visa status för en användare

Använd följande steg för att komma åt sidan där du kan visa och hantera användar tillstånd:

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Gå till **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj **Multi-Factor Authentication**.
   ![Välj Multi-Factor Authentication](./media/howto-mfa-userstates/selectmfa.png)
4. En ny sida som visar användar tillstånden öppnas.
   ![användar status för Multi-Factor Authentication – skärm bild](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Ändra status för en användare

1. Använd föregående steg för att komma till sidan Azure Multi-Factor Authentication- **användare** .
2. Hitta den användare som du vill aktivera för Azure MFA. Du kan behöva ändra vyn längst upp.
   ![Välj användaren att ändra status för från fliken användare](./media/howto-mfa-userstates/enable1.png)
3. Markera kryss rutan bredvid namnet.
4. Välj **Aktivera** eller **inaktivera**under **snabb steg**till höger.
   ![Aktivera den valda användaren genom att klicka på Aktivera på snabb stegs menyn](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Aktiverade* användare växlas automatiskt till att *tillämpas* när de registrerar sig för Azure MFA. Ändra inte användar tillstånd manuellt till tvingande.

5. Bekräfta ditt val i popup-fönstret som öppnas.

När du har aktiverat användarna ska du meddela dem via e-post. Berätta att de uppmanas att registrera sig nästa gången de loggar in. Om din organisation använder icke-webbläsarbaserade appar som inte stöder modern autentisering måste de också skapa applösenord. Du kan också ta med en länk till [användar handboken för Azure MFA](../user-help/multi-factor-authentication-end-user.md) för att hjälpa dem att komma igång.

### <a name="use-powershell"></a>Använd PowerShell

Ändra användar tillstånd med hjälp av [Azure AD PowerShell](/powershell/azure/overview)genom att ändra `$st.State`. Det finns tre möjliga tillstånd:

* Aktiverad
* Enforced
* Inaktiverad  

Flytta inte användare direkt till framtvingat tillstånd. Om du gör det upphör icke-webbläsarbaserade appar att fungera eftersom användaren inte har genomgått Azure MFA-registrering och fått ett [applösenord](howto-mfa-mfasettings.md#app-passwords).

Installera först modulen med:

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> Glöm inte att ansluta först med **Connect-MSOLService**

Det här exemplet på PowerShell-skript aktiverar MFA för en enskild användare:

   ```PowerShell
   Import-Module MSOnline
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

Att använda PowerShell är ett användbart alternativ när du behöver massredigera användare. Till exempel upprepas följande skript genom en lista över användare och aktiverar MFA på sina konton:

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

Om du vill inaktivera MFA använder du följande skript:

   ```PowerShell
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationMethods @()
   ```

som också kan kortas av:

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Konvertera användare från per användare MFA till villkorlig åtkomst baserat MFA

Följande PowerShell kan hjälpa dig att göra konverteringen till villkorlig åtkomst baserat på Azure Multi-Factor Authentication.

```PowerShell
# Disable MFA for all users, keeping their MFA methods intact
Get-MsolUser -All | Disable-MFA -KeepMethods

# Wrapper to disable MFA with the option to keep the MFA methods (to avoid having to proof-up again later)
function Disable-Mfa {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline=$True)]
        $User,
        [switch] $KeepMethods
    )

    Process {

        Write-Verbose ("Disabling MFA for user '{0}'" -f $User.UserPrincipalName)
        $User | Set-MfaState -State Disabled

        if ($KeepMethods) {
            # Restore the MFA methods which got cleared when disabling MFA
            Set-MsolUser -ObjectId $User.ObjectId `
                         -StrongAuthenticationMethods $User.StrongAuthenticationMethods
        }
    }
}

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

```

## <a name="next-steps"></a>Nästa steg

* Varför uppmanas användaren eller inte att göra MFA? Se avsnittet [Azure AD-inloggningar i rapporterna i Azure Multi-Factor Authentication-dokument](howto-mfa-reporting.md#azure-ad-sign-ins-report).
* Information om hur du konfigurerar ytterligare inställningar som tillförlitliga IP-adresser, anpassade röst meddelanden och bedrägeri aviseringar finns i artikeln [Konfigurera inställningar för Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
* Information om hur du hanterar användar inställningar för Azure Multi-Factor Authentication finns i artikeln [hantera användar inställningar med Azure Multi-Factor Authentication i molnet](howto-mfa-userdevicesettings.md)
