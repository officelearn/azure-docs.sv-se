---
title: Multi-Factor Authentication per användare – Azure Active Directory
description: Aktivera MFA genom att ändra användar tillstånd i Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46195a0a799f9edabcd8cd5a27e1b79752d03a45
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964063"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Så här kräver du tvåstegsverifiering för en användare

Du kan utföra en av två metoder för att kräva tvåstegsverifiering, som båda kräver att du använder ett globalt administratörs konto. Det första alternativet är att aktivera varje användare för Azure Multi-Factor Authentication (MFA). När användare Aktiver ATS individuellt utför de tvåstegsverifiering varje gång de loggar in (med vissa undantag, till exempel när de loggar in från betrodda IP-adresser eller när funktionen _sparade enheter_ aktive RAS). Det andra alternativet är att konfigurera en princip för villkorlig åtkomst som kräver tvåstegsverifiering under vissa förhållanden.

> [!TIP]
> Den rekommenderade metoden är att aktivera Azure Multi-Factor Authentication att använda principer för villkorlig åtkomst. Att ändra användar tillstånd rekommenderas inte längre om licenserna inte innehåller villkorlig åtkomst eftersom det krävs att användarna utför MFA varje gång de loggar in.

## <a name="choose-how-to-enable"></a>Välj hur du vill aktivera

**Aktive rad genom att ändra användar tillstånd** – det här är den traditionella metoden för att kräva tvåstegsverifiering och beskrivs i den här artikeln. Det fungerar med både Azure MFA i molnet och Azure MFA-servern. Om du använder den här metoden måste användarna utföra tvåstegsverifiering **varje gång** de loggar in och åsidosätter principer för villkorlig åtkomst.

Aktive rad med princip för villkorlig åtkomst – det här är det mest flexibla sättet att aktivera tvåstegsverifiering för dina användare. Aktivering med principen för villkorlig åtkomst fungerar bara för Azure MFA i molnet och är en Premium funktion i Azure AD. Mer information om den här metoden finns i [distribuera molnbaserad Azure-Multi-Factor Authentication](howto-mfa-getstarted.md).

Aktive rad av Azure AD Identity Protection – med den här metoden används principen Azure AD Identity Protection risk för att kräva tvåstegsverifiering endast baserat på inloggnings risk för alla moln program. Den här metoden kräver Azure Active Directory P2-licensiering. Du hittar mer information om den här metoden i [Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> Mer information om licenser och priser finns på sidan med pris information för [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) och [Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) .

## <a name="enable-azure-mfa-by-changing-user-state"></a>Aktivera Azure MFA genom att ändra användar tillstånd

Användar konton i Azure Multi-Factor Authentication har följande tre distinkta tillstånd:

> [!IMPORTANT]
> Att aktivera Azure MFA via en princip för villkorlig åtkomst ändrar inte användarens tillstånd. Inga alarmed-användare visas inaktiverade. Villkorlig åtkomst ändrar inte tillstånd. **Organisationer bör inte aktivera eller framtvinga användare om de använder principer för villkorlig åtkomst.**

| Status | Beskrivning | Icke-webbläsarbaserade appar som påverkas | Webbläsarbaserade appar som påverkas | Modern autentisering påverkas |
|:---:| --- |:---:|:--:|:--:|
| Disabled | Standard läget för en ny användare som inte har registrerats i Azure MFA. | Nej | Nej | Nej |
| Enabled | Användaren har registrerats i Azure MFA, men har inte registrerats. De får ett meddelande om att registrera sig nästa gången de loggar in. | Nej.  De fortsätter att fungera tills registrerings processen har slutförts. | Ja. När sessionen har gått ut krävs Azure MFA-registrering.| Ja. När åtkomsttoken upphör att gälla krävs Azure MFA-registrering. |
| Enforced | Användaren har registrerats och slutfört registrerings processen för Azure MFA. | Ja. Appar kräver applösenord. | Ja. Azure MFA krävs vid inloggning. | Ja. Azure MFA krävs vid inloggning. |

En användares tillstånd visar om en administratör har registrerat dem i Azure MFA och om de har slutfört registrerings processen.

Alla användare börjar vara *inaktiverade*. När du registrerar användare i Azure MFA ändras deras status till *aktive rad*. När aktiverade användare loggar in och slutför registrerings processen ändras deras status till *tvingande*.

> [!NOTE]
> Om MFA återaktiveras på ett användar objekt som redan har registrerings information, till exempel telefon eller e-post, måste administratörerna ha den användaren omregistrera MFA via Azure Portal eller PowerShell. Om användaren inte omregistreras, övergår inte MFA-statusen över från *aktive rad* till *tvingande* i MFA management UI.

### <a name="view-the-status-for-a-user"></a>Visa status för en användare

Använd följande steg för att komma åt sidan där du kan visa och hantera användar tillstånd:

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Sök efter och välj *Azure Active Directory*. Välj **Användare** > **Alla användare**.
3. Välj **Multi-Factor Authentication**.
   ![Välj Multi-Factor Authentication](./media/howto-mfa-userstates/selectmfa.png)
4. En ny sida som visar användar tillstånden öppnas.
   ![användar status för Multi-Factor Authentication-skärm bild](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Ändra status för en användare

1. Använd föregående steg för att komma till sidan Azure Multi-Factor Authentication- **användare** .
2. Hitta den användare som du vill aktivera för Azure MFA. Du kan behöva ändra vyn längst upp.
   ![Välj användaren att ändra status för från fliken användare](./media/howto-mfa-userstates/enable1.png)
3. Markera kryss rutan bredvid namnet.
4. Välj **Aktivera** eller **inaktivera**under **snabb steg**till höger.
   ![aktivera markerad användare genom att klicka på Aktivera på snabb menyn](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Aktiverade* användare växlas automatiskt till att *tillämpas* när de registrerar sig för Azure MFA. Ändra inte användar tillstånd manuellt till *tvingande*.

5. Bekräfta ditt val i popup-fönstret som öppnas.

När du har aktiverat användarna ska du meddela dem via e-post. Berätta att de uppmanas att registrera sig nästa gången de loggar in. Om din organisation använder icke-webbläsarbaserade appar som inte stöder modern autentisering måste de också skapa applösenord. Du kan också ta med en länk till [användar handboken för Azure MFA](../user-help/multi-factor-authentication-end-user.md) för att hjälpa dem att komma igång.

### <a name="use-powershell"></a>Använd PowerShell

Ändra `$st.State`om du vill ändra användar tillstånd med hjälp av [Azure AD PowerShell](/powershell/azure/overview). Det finns tre möjliga tillstånd:

* Enabled
* Enforced
* Disabled  

Flytta inte användare direkt till *framtvingat* tillstånd. Om du gör det upphör icke-webbläsarbaserade appar att fungera eftersom användaren inte har genomgått Azure MFA-registrering och fått ett [applösenord](howto-mfa-mfasettings.md#app-passwords).

Installera först modulen med:

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> Glöm inte att ansluta först med **Connect-MSOLService**

   ```PowerShell
   Connect-MsolService
   ```

Det här exemplet på PowerShell-skript aktiverar MFA för en enskild användare:

   ```PowerShell
   Import-Module MSOnline
   Connect-MsolService
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

Följande PowerShell kan hjälpa dig att göra konverteringen till baserade Azure-Multi-Factor Authentication med villkorlig åtkomst.

Kör PowerShell i ett ISE-fönster eller Spara som. PS1-fil som ska köras lokalt.

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
> Vi ändrade nyligen beteendet och PowerShell-skriptet ovan enligt detta. Tidigare har skriptet sparat av MFA-metoderna, inaktiverat MFA och återställt metoderna. Detta behövs inte längre nu när standard beteendet för inaktivera inte tar bort metoderna.
>
> Om MFA återaktiveras på ett användar objekt som redan har registrerings information, till exempel telefon eller e-post, måste administratörerna ha den användaren omregistrera MFA via Azure Portal eller PowerShell. Om användaren inte omregistreras, övergår inte MFA-statusen över från *aktive rad* till *tvingande* i MFA management UI.

## <a name="next-steps"></a>Nästa steg

* Varför uppmanas användaren eller inte att göra MFA? Se avsnittet [Azure AD-inloggningar i rapporterna i azure Multi-Factor Authentication-dokumentet](howto-mfa-reporting.md#azure-ad-sign-ins-report).
* Information om hur du konfigurerar ytterligare inställningar som tillförlitliga IP-adresser, anpassade röst meddelanden och bedrägeri aviseringar finns i artikeln [Konfigurera Azure Multi-Factor Authentication inställningar](howto-mfa-mfasettings.md)
* Information om hur du hanterar användar inställningar för Azure Multi-Factor Authentication finns i artikeln [hantera användar inställningar med azure Multi-Factor Authentication i molnet](howto-mfa-userdevicesettings.md)
