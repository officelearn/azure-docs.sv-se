---
title: Aktivera Multi-Factor Authentication per användare – Azure Active Directory
description: Lär dig hur du aktiverar Azure-Multi-Factor Authentication per användare genom att ändra användar tillstånd
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bd688a9e488c1206b0c8531698b061f650e2afe
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87417938"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Aktivera Azure MFA per användare för att skydda inloggningshändelser

Det finns två sätt att skydda användar inloggnings händelser genom att kräva Multi-Factor Authentication i Azure AD. Det första och bästa alternativet är att konfigurera en princip för villkorlig åtkomst som kräver multifaktorautentisering under vissa förhållanden. Det andra alternativet är att aktivera varje användare för Azure Multi-Factor Authentication. När användare Aktiver ATS individuellt utför de Multi-Factor Authentication varje gång de loggar in (med vissa undantag, till exempel när de loggar in från betrodda IP-adresser eller när funktionen _sparade enheter_ aktive RAS).

> [!NOTE]
> Den rekommenderade metoden är att aktivera Azure Multi-Factor Authentication att använda principer för villkorlig åtkomst. Att ändra användar tillstånd rekommenderas inte längre om dina licenser inte innehåller villkorlig åtkomst eftersom det krävs att användarna utför MFA varje gång de loggar in. Information om hur du kommer igång med villkorlig åtkomst finns i [Självstudier: skydda användar inloggnings händelser med Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> För kostnads fria Azure AD-klienter utan villkorlig åtkomst kan du [använda säkerhets inställningar för att skydda användare](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="azure-multi-factor-authentication-user-states"></a>Användar tillstånd för Azure-Multi-Factor Authentication

Användar konton i Azure Multi-Factor Authentication har följande tre distinkta tillstånd:

> [!IMPORTANT]
> Att aktivera Azure Multi-Factor Authentication via en princip för villkorlig åtkomst ändrar inte användarens tillstånd. Är inte alarmed om användarna ser inaktiverade. Villkorlig åtkomst ändrar inte tillstånd.
>
> **Om du använder principer för villkorlig åtkomst bör du inte aktivera eller framtvinga användare.**

| Status | Beskrivning | Icke-webbläsarbaserade appar som påverkas | Webbläsarbaserade appar som påverkas | Modern autentisering påverkas |
|:---:| --- |:---:|:--:|:--:|
| Inaktiverad | Standard läget för en ny användare som inte har registrerats i Azure Multi-Factor Authentication. | Nej | Nej | Nej |
| Enabled | Användaren har registrerats i Azure Multi-Factor Authentication men inte registrerat autentiseringsmetoder. De får ett meddelande om att registrera sig nästa gången de loggar in. | Nej.  De fortsätter att fungera tills registrerings processen har slutförts. | Ja. När sessionen har gått ut krävs Azure Multi-Factor Authentication registrering.| Ja. När åtkomsttoken har upphört att gälla krävs Azure Multi-Factor Authentication registrering. |
| Enforced | Användaren har registrerats och slutfört registrerings processen för Azure Multi-Factor Authentication. | Ja. Appar kräver applösenord. | Ja. Azure Multi-Factor Authentication krävs vid inloggning. | Ja. Azure Multi-Factor Authentication krävs vid inloggning. |

En användares tillstånd visar om en administratör har registrerat dem i Azure Multi-Factor Authentication och om de har slutfört registrerings processen.

Alla användare börjar vara *inaktiverade*. När du registrerar användare i Azure Multi-Factor Authentication ändras deras status till *aktive rad*. När aktiverade användare loggar in och slutför registrerings processen ändras deras status till *tvingande*.

> [!NOTE]
> Om MFA återaktiveras på ett användar objekt som redan har registrerings information, till exempel telefon eller e-post, måste administratörerna ha den användaren omregistrera MFA via Azure Portal eller PowerShell. Om användaren inte omregistreras, övergår inte MFA-statusen över från *aktive rad* till *tvingande* i MFA management UI.

## <a name="view-the-status-for-a-user"></a>Visa status för en användare

Använd följande steg för att komma åt Azure Portal sidan där du kan visa och hantera användar tillstånd:

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
1. Sök efter och välj *Azure Active Directory*och välj sedan **användare**  >  **alla användare**.
1. Välj **Multi-Factor Authentication**. Du kan behöva bläddra till höger för att se det här meny alternativet. Välj skärm bilden nedan om du vill se hela Azure Portals fönster och meny plats: [ ![ Välj Multi-Factor Authentication från fönstret användare i Azure AD.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. En ny sida öppnas som visar användar statusen, som du ser i följande exempel.
   ![Skärm bild som visar information om exempel på användar tillstånd för Azure Multi-Factor Authentication](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Ändra status för en användare

Utför följande steg för att ändra Azure Multi-Factor Authentication-tillstånd för en användare:

1. Använd föregående steg för att komma till sidan Azure Multi-Factor Authentication- **användare** .
1. Hitta den användare som du vill aktivera för Azure Multi-Factor Authentication. Du kan behöva ändra vyn överst till **användare**.
   ![Välj användaren att ändra status för från fliken användare](./media/howto-mfa-userstates/enable1.png)
1. Markera kryss rutan bredvid namnen på de användare som du vill ändra status för.
1. Välj **Aktivera** eller **inaktivera**på höger sida under **snabb steg**. I följande exempel har användaren *John Smith* en kontroll bredvid sitt namn och aktive ras för användning: ![ Aktivera vald användare genom att klicka på Aktivera på snabb menyn](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Aktiverade* användare växlas automatiskt till att *tillämpas* när de registrerar sig för Azure Multi-Factor Authentication. Ändra inte användar tillstånd manuellt till *framtvingad*.

1. Bekräfta ditt val i popup-fönstret som öppnas.

När du har aktiverat användarna ska du meddela dem via e-post. Berätta för användarna att en uppmaning visas för att be dem att registrera sig nästa gången de loggar in. Om din organisation använder icke-webbläsarbaserade appar som inte stöder modern autentisering måste de också skapa applösenord. Mer information finns i [användar handboken för Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user.md) för att hjälpa dem att komma igång.

## <a name="change-state-using-powershell"></a>Ändra tillstånd med PowerShell

Om du vill ändra användar tillstånd med hjälp av [Azure AD PowerShell](/powershell/azure/)ändrar du `$st.State` parametern för ett användar konto. Det finns tre möjliga tillstånd för ett användar konto:

* *Aktiverad*
* *Enforced*
* *Inaktiverad*  

Flytta inte användare direkt till *framtvingat* tillstånd. Om du gör det upphör icke-webbläsarbaserade appar att fungera eftersom användaren inte har gått igenom Azure Multi-Factor Authentication registrering och fått ett [applösenord](howto-mfa-app-passwords.md).

Kom igång genom att installera *MSOnline* -modulen med hjälp av [install-module](/powershell/module/powershellget/install-module) enligt följande:

```PowerShell
Install-Module MSOnline
```

Anslut sedan med [Connect-MSOLService](/powershell/module/msonline/connect-msolservice):

```PowerShell
Connect-MsolService
```

Följande exempel på PowerShell-skript aktiverar MFA för en enskild användare med namnet *bsimon@contoso.com* :

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

Att använda PowerShell är ett användbart alternativ när du behöver massredigera användare. Följande skript upprepas genom en lista över användare och aktiverar MFA på sina konton. Definiera de användar konton som har angetts på den första raden `$users` enligt följande:

   ```PowerShell
   # Define your list of users to update state in bulk
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

Om du vill inaktivera MFA hämtar följande exempel en användare med [Get-MsolUser](/powershell/module/msonline/get-msoluser)och tar sedan bort alla *StrongAuthenticationRequirements* som definierats för den definierade användaren med hjälp av [set-MsolUser](/powershell/module/msonline/set-msoluser):

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

Du kan också direkt inaktivera MFA för en användare som använder [set-MsolUser](/powershell/module/msonline/set-msoluser) på följande sätt:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Konvertera användare från per användare MFA till villkorlig åtkomst baserat MFA

Följande PowerShell kan hjälpa dig att göra konverteringen till baserade Azure-Multi-Factor Authentication med villkorlig åtkomst.

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
> Om MFA återaktiveras på ett användar objekt som redan har registrerings information, till exempel telefon eller e-post, måste administratörerna ha den användaren omregistrera MFA via Azure Portal eller PowerShell. Om användaren inte omregistreras, övergår inte MFA-statusen över från *aktive rad* till *tvingande* i MFA management UI.

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar inställningar för Azure-Multi-Factor Authentication finns i [Konfigurera inställningar för azure Multi-Factor Authentication](howto-mfa-mfasettings.md).

Information om hur du hanterar användar inställningar för Azure Multi-Factor Authentication finns i [hantera användar inställningar med azure Multi-Factor Authentication](howto-mfa-userdevicesettings.md).

Mer information om varför en användare uppmanas att göra MFA finns i [Azure Multi-Factor Authentication-rapporter](howto-mfa-reporting.md).
