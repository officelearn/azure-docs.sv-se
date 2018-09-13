---
title: Tillåt eller blockera inbjudningar till B2B-användare från specifika organisationer – Azure Active Directory | Microsoft Docs
description: Visar hur en administratör använda Azure portal eller PowerShell för att ange en eller neka listan om du vill tillåta eller blockera B2B-användare från vissa domäner.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/19/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 271371515e881fda4fb81d844bf84689e29b1e6b
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35647234"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Tillåt eller blockera inbjudningar till B2B-användare från specifika organisationer

Du kan använda en lista över tillåtna eller en Neka-lista för att tillåta eller blockera inbjudningar till B2B-användare från specifika organisationer. Till exempel om du vill blockera personliga e-postdomäner, kan du konfigurera en Neka-lista som innehåller domäner som Gmail.com och Outlook.com. Om företaget har ett samarbete med andra företag som Contoso.com och Fabrikam.com Litware.com och du vill begränsa inbjudningar till bara dessa organisationer, du kan lägga till Contoso.com och Fabrikam.com Litware.com till din lista över tillåtna.
  
## <a name="important-considerations"></a>Att tänka på

- Du kan skapa en lista över tillåtna eller en Neka-lista. Du kan inte ange båda typerna av listor. Som standard domänerna som är inte i listan över tillåtna på listan över nekade och vice versa. 
- Du kan skapa endast en princip per organisation. Du kan uppdatera principen för att inkludera flera domäner, eller du kan ta bort principen för att skapa en ny. 
- Den här listan fungerar oberoende av OneDrive för företag och SharePoint Online tillåta/blockera listor. Om du vill begränsa enskilda fildelning i SharePoint Online måste du konfigurera en Tillåt eller neka lista för OneDrive för företag och SharePoint Online. Mer information finns i [begränsade domäner delning i SharePoint Online och OneDrive för företag](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- Den här listan gäller inte för externa användare som har redan utnyttjat inbjudan. Listan kommer att aktiveras när listan har konfigurerats. Om en användarinbjudan är i ett väntande tillstånd, och du har angett en princip som blockerar sin domän, misslyckas anslutningsförsöket att lösa in inbjudan.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Ange listan Tillåt eller neka lista principen i portalen

Som standard den **Tillåt att inbjudningar skickas till alla domänerr (mest inkluderande)** inställningen är aktiverad. I det här fallet kan du bjuda in B2B-användare från alla organisationer.

### <a name="add-a-deny-list"></a>Lägg till en Neka-lista

Det här är det vanligaste scenariot där din organisation vill arbeta med nästan vilket företag, men vill förhindra användare från vissa domäner bjudas in som B2B-användare.

Lägga till en Neka-lista:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Azure Active Directory** > **användare** > **användarinställningar**.
3. Under **externa användare**väljer **hantera inställningar för externt samarbete**.
4. Under **samarbetesbegränsningar**väljer **neka inbjudningar till de angivna domänerna**.
5. Under **MÅLDOMÄNER**, anger du namnet på en av de domäner som du vill blockera. Ange varje domän för flera domäner på en ny rad. Exempel:

   ![Visar alternativet Neka med tillagda domäner](./media/allow-deny-list/DenyListSettings.png)
 
6. När du är klar klickar du på **spara**.

När du har principen, om du försöker bjuda in användare från en blockerad domän kan får du ett meddelande om att användarens domän blockerad är av din inbjudningspolicy.
 
### <a name="add-an-allow-list"></a>Lägg till en lista över tillåtna

Det här är en mer begränsande konfiguration, där du kan ange specifika domäner i listan över tillåtna och begränsa inbjudningar till andra organisationer eller domäner som inte nämns. 

Om du vill använda en lista över tillåtna, se till att du lägga tid på att fullständigt utvärdera vad företaget i stället är. Om du gör den här principen för restriktiva, kan användarna välja att skicka dokument via e-post eller hitta andra inte är IT-sanktionerade sätt att samarbeta.


Lägga till en lista över tillåtna:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Azure Active Directory** > **användare** > **användarinställningar**.
3. Under **externa användare**väljer **hantera inställningar för externt samarbete**.
4. Under **samarbetesbegränsningar**väljer **Tillåt enbart inbjudningar till de angivna domänerna (mest begränsande)**.
5. Under **MÅLDOMÄNER**, anger du namnet på en av de domäner som du vill tillåta. Ange varje domän för flera domäner på en ny rad. Exempel:

   ![Visar alternativet Tillåt med tillagda domäner](./media/allow-deny-list/AllowListSettings.png)
 
6. När du är klar klickar du på **spara**.

När du ställer in principen om du försöker bjuda in användare från en domän som inte finns med på listan över tillåtna, får du ett meddelande om att användarens domän blockerad är av din inbjudningspolicy.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Växlar du från Tillåt för att neka lista och tvärtom 

Om du växlar från en princip till en annan ignorerar detta i befintliga konfiguration. Se till att säkerhetskopiera information om konfiguration innan du utför växeln. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Ange listan Tillåt eller neka lista princip med hjälp av PowerShell

### <a name="prerequisite"></a>Krav

För att välja listan Tillåt eller neka listan med hjälp av PowerShell, måste du installera förhandsversionen av Azure Active Directory-modulen för Windows PowerShell. Mer specifikt installera AzureADPreview Modulversion 2.0.0.98 eller senare.

Kontrollera vilken version av modulen (och om det är installerat):
 
1. Öppna Windows PowerShell som en förhöjd (Kör som administratör). 
2. Kör följande kommando för att se om du har alla versioner av Azure Active Directory-modulen för Windows PowerShell installerat på datorn:

   ````powershell  
   Get-Module -ListAvailable AzureAD*
   ````

Om modulen inte är installerad eller så har du inte en version som krävs, gör du något av följande:

- Om inga resultat returneras, kör du följande kommando för att installera den senaste versionen av modulen AzureADPreview:
  
   ````powershell  
   Install-Module AzureADPreview
   ````
- Om det bara AzureAD-modulen visas i resultaten, kör du följande kommandon för att installera modulen AzureADPreview: 

   ````powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ````
- Om det bara modulen AzureADPreview visas i resultatet, men versionen är mindre än 2.0.0.98, kör du följande kommandon för att uppdatera den: 

   ````powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ````

- Om både till AzureAD AzureADPreview modulerna och visas i resultatet, men versionen av modulen AzureADPreview är mindre än 2.0.0.98, kör du följande kommandon för att uppdatera den: 

   ````powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ````

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Använda AzureADPolicy-cmdletar för att konfigurera principen

Skapa en Tillåt eller neka listan genom att använda den [New AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) cmdlet. I följande exempel visas hur du ställer in en Neka-lista som blockerar ”live.com”-domänen.

````powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
````

Följande visar det här exemplet, men med infogat för definition av principen.

````powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
````

För att välja listan Tillåt eller neka lista princip, använda den [Set-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) cmdlet. Exempel:

````powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
````

Hämta principen med den [Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) cmdlet. Exempel:

````powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
````

Ta bort principen med den [Remove-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview) cmdlet. Exempel:

````powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
````

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure AD B2B finns i [vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- Läs om hur villkorlig åtkomst och B2B-samarbete [villkorlig åtkomst för användare i B2B-samarbetet](conditional-access.md).



