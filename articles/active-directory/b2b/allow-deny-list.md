---
title: Tillåt eller blockera inbjudningar till specifika organisationer - Azure AD
description: Visar hur en administratör kan använda Azure-portalen eller PowerShell för att ange en åtkomst- eller neka-lista för att tillåta eller blockera B2B-användare från vissa domäner.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/15/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b5100c4406cfd4a8395dfa177dc3cd5e911decb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273431"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Tillåta eller blockera inbjudningar till B2B-användare från specifika organisationer

Du kan använda en tillåt-lista eller en neka lista för att tillåta eller blockera inbjudningar till B2B-användare från specifika organisationer. Om du till exempel vill blockera personliga e-postadressdomäner kan du skapa en lista med nekande som innehåller domäner som Gmail.com och Outlook.com. Om ditt företag har ett partnerskap med andra företag som Contoso.com, Fabrikam.com och Litware.com, och du vill begränsa inbjudningar till endast dessa organisationer, kan du lägga till Contoso.com, Fabrikam.com och Litware.com i listan över tillåtna.
  
## <a name="important-considerations"></a>Att tänka på

- Du kan skapa antingen en tillåt-lista eller en neka lista. Du kan inte ställa in båda typerna av listor. Som standard finns de domäner som inte finns i listan över tillåtna i listan neka och vice versa. 
- Du kan bara skapa en princip per organisation. Du kan uppdatera principen så att den innehåller fler domäner eller ta bort principen för att skapa en ny. 
- Antalet domäner som du kan lägga till i en tillåt-lista eller neka lista begränsas endast av principens storlek. Den maximala storleken på hela principen är 25 KB (25 000 tecken), vilket inkluderar listan tillåt eller neka och andra parametrar som konfigurerats för andra funktioner.
- Den här listan fungerar oberoende av onedrive för företag och SharePoint Online tillåt/blockera listor. Om du vill begränsa enskilda fildelning i SharePoint Online måste du konfigurera en tillåt- eller neka-lista för OneDrive för företag och SharePoint Online. Mer information finns [i Delning av begränsade domäner i SharePoint Online och OneDrive för företag](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- Listan gäller inte externa användare som redan har löst in inbjudan. Listan kommer att verkställas när listan har konfigurerats. Om en användartjudan är i ett väntande tillstånd och du anger en princip som blockerar deras domän, misslyckas användarens försök att lösa in inbjudan.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Ange principen för tillåt eller neka lista i portalen

Som standard är inställningen **Tillåt inbjudningar som ska skickas till valfri domän (mest inkluderande)** aktiverad. I det här fallet kan du bjuda in B2B-användare från valfri organisation.

### <a name="add-a-deny-list"></a>Lägga till en neka-lista

Detta är det mest typiska scenariot, där din organisation vill arbeta med nästan alla organisationer, men vill förhindra att användare från specifika domäner ska bjudas in som B2B-användare.

Så här lägger du till en neka-lista:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj**Användarinställningar för** **Azure Active Directory** > **-användare** > .
3. Under **Externa användare**väljer du Hantera inställningar för **externt samarbete**.
4. Under **Samarbetsbegränsningar**väljer du **Neka inbjudningar till de angivna domänerna**.
5. Under **MÅLDOMÄNER**anger du namnet på en av de domäner som du vill blockera. För flera domäner anger du varje domän på en ny rad. Ett exempel:

   ![Visar alternativet neka med tillagda domäner](./media/allow-deny-list/DenyListSettings.png)
 
6. När du är klar klickar du på **Spara**.

När du har angett principen får du ett meddelande om att användarens domän för närvarande blockeras av din inbjudningsprincip om du försöker bjuda in en användare från en blockerad domän.
 
### <a name="add-an-allow-list"></a>Lägga till en tillåtlista

Det här är en mer restriktiv konfiguration där du kan ange specifika domäner i listan över tillåtna och begränsa inbjudningar till andra organisationer eller domäner som inte nämns. 

Om du vill använda en tillåt-lista kontrollerar du att du lägger tid på att utvärdera dina affärsbehov fullt ut. Om du gör den här principen alltför restriktiv kan användarna välja att skicka dokument via e-post eller hitta andra icke-IT-sanktionerade sätt att samarbeta.


Så här lägger du till en tillåt-lista:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj**Användarinställningar för** **Azure Active Directory** > **-användare** > .
3. Under **Externa användare**väljer du Hantera inställningar för **externt samarbete**.
4. Under **Samarbetsbegränsningar**väljer du **Tillåt inbjudningar endast till de angivna domänerna (de mest restriktiva).**
5. Under **MÅLDOMÄNER**anger du namnet på en av de domäner som du vill tillåta. För flera domäner anger du varje domän på en ny rad. Ett exempel:

   ![Visar alternativet Tillåt med tillagda domäner](./media/allow-deny-list/AllowListSettings.png)
 
6. När du är klar klickar du på **Spara**.

När du har angett principen får du ett meddelande om att användarens domän för närvarande blockeras av din inbjudningsprincip om du försöker bjuda in en användare från en domän som inte finns med i listan över tillåtna personer.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Växla från tillåt att neka lista och vice versa 

Om du växlar från en princip till en annan ignoreras den befintliga principkonfigurationen. Se till att säkerhetskopiera information om din konfiguration innan du utför växeln. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Ange principen för tillåt eller neka lista med PowerShell

### <a name="prerequisite"></a>Krav

> [!Note]
> AzureADPreview Module är inte en helt stödd modul som den är i förhandsversion. 

Om du vill ange listan tillåt eller neka med PowerShell måste du installera förhandsversionen av Azure Active Directory Module för Windows PowerShell. Installera AzureADPreview-modul version 2.0.0.98 eller senare.

Så här kontrollerar du versionen av modulen (och ser om den är installerad):
 
1. Öppna Windows PowerShell som en förhöjd användare (Kör som administratör). 
2. Kör följande kommando för att se om du har några versioner av Azure Active Directory Module för Windows PowerShell installerade på datorn:

   ```powershell  
   Get-Module -ListAvailable AzureAD*
   ```

Om modulen inte är installerad, eller om du inte har en nödvändig version, gör något av följande:

- Om inga resultat returneras kör du följande kommando för att installera den senaste versionen av AzureADPreview-modulen:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Om endast AzureAD-modulen visas i resultaten kör du följande kommandon för att installera AzureADPreview-modulen: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Om bara AzureADPreview-modulen visas i resultatet, men versionen är mindre än 2.0.0.98, kör du följande kommandon för att uppdatera den: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ```

- Om både AzureAD- och AzureADPreview-modulerna visas i resultaten, men versionen av AzureADPreview-modulen är mindre än 2.0.0.98, kör du följande kommandon för att uppdatera den: 

   ```powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ```

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Använda AzureADPolicy-cmdlets för att konfigurera principen

Om du vill skapa en lista över tillåta eller neka använder du cmdleten [New-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) I följande exempel visas hur du anger en neka-lista som blockerar domänen "live.com".

```powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Följande visar samma exempel, men med principdefinitionen infogad.

```powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Om du vill ange principen för tillåt eller neka list använder du cmdleten [Set-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) Ett exempel:

```powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
```

Hämta principen genom att använda cmdleten [Get-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) Ett exempel:

```powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
```

Om du vill ta bort principen använder du cmdleten [Remove-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview) Ett exempel:

```powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
```

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure AD B2B finns i [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- Information om villkorlig åtkomst och B2B-samarbete finns i [Villkorlig åtkomst för B2B-samarbetsanvändare](conditional-access.md).



