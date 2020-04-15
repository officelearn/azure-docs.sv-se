---
title: Aktivera multifaktorautentisering per användare – Azure Active Directory
description: Lär dig hur du aktiverar Azure Multi Factor-autentisering per användare genom att ändra användartillståndet
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e8ceaf13324864c7ec3df731c3e710815b0eba9
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309787"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Aktivera Azure Multi Factor-autentisering per användare för att skydda inloggningshändelser

Det finns två sätt att skydda användaråtkomsthändelser genom att kräva multifaktorautentisering i Azure AD. Det första och föredragna alternativet är att ställa in en princip för villkorlig åtkomst som kräver multifaktorautentisering under vissa förhållanden. Det andra alternativet är att aktivera varje användare för Azure Multi-Factor Authentication. När användare aktiveras individuellt utför de multifaktorautentisering varje gång de loggar in (med vissa undantag, till exempel när de loggar in från betrodda IP-adresser eller när funktionen _för ihågkomna enheter_ är aktiverad).

> [!NOTE]
> Aktivera Azure Multi-Factor Autentisering med principer för villkorlig åtkomst är den rekommenderade metoden. Det rekommenderas inte längre att ändra användartillstånd om inte dina licenser innehåller villkorlig åtkomst eftersom det kräver att användarna utför MFA varje gång de loggar in.
>
> Information om hur du kommer igång med villkorlig åtkomst finns [i Självstudiekurs: Säkra inloggningshändelser för användare med Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

## <a name="azure-multi-factor-authentication-user-states"></a>Användartillstånd för Azure Multi Factor Authentication

Användarkonton i Azure Multi-Factor Authentication har följande tre olika tillstånd:

> [!IMPORTANT]
> Om du aktiverar Azure Multi-Factor Authentication via en princip för villkorlig åtkomst ändras inte användarens tillstånd. Bli inte rädd om användarna verkar inaktiverade. Villkorlig åtkomst ändrar inte tillståndet.
>
> **Du bör inte aktivera eller tvinga användare om du använder principer för villkorlig åtkomst.**

| Status | Beskrivning | Appar som inte är webbläsare påverkas | Webbläsarappar påverkas | Modern autentisering påverkas |
|:---:| --- |:---:|:--:|:--:|
| Disabled | Standardtillståndet för en ny användare som inte är registrerad i Azure Multi-Factor Authentication. | Inga | Inga | Inga |
| Enabled | Användaren har registrerats i Azure Multi-Factor Authentication, men har inte registrerats. De får en uppmaning om att registrera sig nästa gång de loggar in. | Nej.  De fortsätter att fungera tills registreringsprocessen är klar. | Ja. När sessionen har gått ut krävs registrering av Azure Multi Factor Authentication.| Ja. När åtkomsttoken har gått ut krävs registrering av Azure Multi Factor Authentication. |
| Enforced | Användaren har registrerats och har slutfört registreringsprocessen för Azure Multi-Factor Authentication. | Ja. Appar kräver applösenord. | Ja. Azure Multi-Factor Autentisering krävs vid inloggning. | Ja. Azure Multi-Factor Autentisering krävs vid inloggning. |

En användares tillstånd återspeglar om en administratör har registrerat dem i Azure Multi-Factor Authentication och om de slutförde registreringsprocessen.

Alla användare börjar *inaktiveras*. När du registrerar användare i Azure Multi-Factor Authentication ändras deras tillstånd till *Aktiverad*. När aktiverade användare loggar in och slutför registreringsprocessen ändras deras tillstånd till *Tvingande*.

> [!NOTE]
> Om MFA återaktiverats på ett användarobjekt som redan har registreringsinformation, till exempel telefon eller e-post, måste administratörer ha den användaren registrera MFA via Azure-portalen eller PowerShell. Om användaren inte registrerar om, övergår inte deras MFA-tillstånd från *Aktiverat* till *Verkställt* i MFA-hanteringsgränssnittet.

## <a name="view-the-status-for-a-user"></a>Visa status för en användare

Följ följande steg för att komma åt Azure-portalsidan där du kan visa och hantera användartillstånd:

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör.
1. Sök efter och välj *Azure Active Directory*och välj sedan **Användare** > **alla användare**.
1. Välj **Multifaktorautentisering**. Du kan behöva bläddra till höger för att se det här menyalternativet. Välj exempel skärmdump nedan för att se hela Azure portal fönster och menyplats:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Välj Multifaktorautentisering i fönstret Användare i Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. En ny sida öppnas som visar användartillståndet, vilket visas i följande exempel.
   ![Skärmbild som visar exempel på användartillståndsinformation för Azure Multi-Factor-autentisering](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Ändra status för en användare

Så här ändrar du tillståndet för Azure Multi-Factor Authentication för en användare:

1. Använd föregående steg för att komma till sidan Azure Multi-Factor Authentication **users.**
1. Hitta den användare som du vill aktivera för Azure Multi-Factor Authentication. Du kan behöva ändra vyn högst upp till **användarna**.
   ![Välj den användare som ska ändra status för på fliken Användare](./media/howto-mfa-userstates/enable1.png)
1. Markera rutan bredvid namnet eller namnet på användaren/användarna för att ändra tillståndet för.
1. Välj **Aktivera** eller **Inaktivera**under **snabba steg**på höger sida . I följande exempel har användaren *John Smith* en check bredvid sitt namn ![och är aktiverad för användning: Aktivera vald användare genom att klicka på Aktivera på snabbstegsmenyn](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Aktiverade* användare växlas automatiskt till *Tvingande när* de registrerar sig för Azure Multi-Factor Authentication. Ändra inte användartillståndet manuellt till *Verkställt*.

1. Bekräfta ditt val i popup-fönstret som öppnas.

När du har aktiverat användare meddelar du dem via e-post. Tala om för användarna att en uppmaning visas för att be dem att registrera sig nästa gång de loggar in. Om din organisation använder appar som inte är webbläsare och som inte stöder modern autentisering måste de också skapa applösenord. Mer information finns i [slutanvändarhandboken för Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user.md) för att hjälpa dem att komma igång.

## <a name="change-state-using-powershell"></a>Ändra tillstånd med PowerShell

Om du vill ändra användartillståndet med hjälp `$st.State` av Azure AD [PowerShell](/powershell/azure/overview)ändrar du parametern för ett användarkonto. Det finns tre möjliga tillstånd för ett användarkonto:

* *Enabled*
* *Enforced*
* *Disabled*  

Flytta inte användare direkt till tillståndet *Tvingande.* Om du gör det slutar appar som inte är webbläsarbaserade att fungera eftersom användaren inte har gått igenom Azure Multi-Factor Authentication-registrering och fått ett [applösenord](howto-mfa-mfasettings.md#app-passwords).

För att komma igång, installera *MSOnline-modulen* med [Install-Module](/powershell/module/powershellget/install-module) enligt följande:

```PowerShell
Install-Module MSOnline
```

Anslut sedan med [Connect-MsolService:](/powershell/module/msonline/connect-msolservice)

```PowerShell
Connect-MsolService
```

I följande exempel powershell-skript aktiverar MFA *bsimon@contoso.com*för en enskild användare med namnet:

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

Att använda PowerShell är ett bra alternativ när du behöver massaktivera användare. Följande skript loopar genom en lista över användare och aktiverar MFA på sina konton. Definiera användarkontona som anges på `$users` den första raden för följande:

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

Om du vill inaktivera MFA får följande exempel en användare med [Get-MsolUser](/powershell/module/msonline/get-msoluser)och tar sedan bort alla *StrongAuthenticationRequirements-set* för den definierade användaren med [Set-MsolUser:](/powershell/module/msonline/set-msoluser)

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

Du kan också direkt inaktivera MFA för en användare som använder [Set-MsolUser](/powershell/module/msonline/set-msoluser) enligt följande:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Konvertera användare från MFA per användare till villkorsstyrd åtkomstbaserad MFA

Följande PowerShell kan hjälpa dig att göra konverteringen till villkorlig åtkomstbaserad Azure Multi-Factor-autentisering.

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
> Vi har nyligen ändrat beteendet och detta PowerShell-skript. Tidigare sparade skriptet mfa-metoderna, inaktiverade MFA och återställde metoderna. Detta är inte längre nödvändigt nu när standardbeteendet för inaktivera inte rensar metoderna.
>
> Om MFA återaktiverats på ett användarobjekt som redan har registreringsinformation, till exempel telefon eller e-post, måste administratörer ha den användaren registrera MFA via Azure-portalen eller PowerShell. Om användaren inte registrerar om, övergår inte deras MFA-tillstånd från *Aktiverat* till *Verkställt* i MFA-hanteringsgränssnittet.

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar Azure Multi-Factor Authentication-inställningar som betrodda IPs, anpassade röstmeddelanden och bedrägeriaviseringar finns i [Konfigurera Azure Multi-Factor Authentication settings](howto-mfa-mfasettings.md). Information om hur du hanterar användarinställningar för Azure Multi-Factor Authentication finns i [Hantera användarinställningar med Azure Multi-Factor Authentication](howto-mfa-userdevicesettings.md).

Information om varför en användare har uppmanats eller inte uppmanats att utföra MFA finns i [Azure Multi-Factor Authentication reports](howto-mfa-reporting.md#azure-ad-sign-ins-report).
