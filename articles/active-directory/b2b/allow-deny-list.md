---
title: Tillåt eller blockera inbjudningar till B2B användare från specifika organisationer - Azure Active Directory | Microsoft Docs
description: Visar hur en administratör använder Azure-portalen eller PowerShell för att ange ett åtkomst eller neka-listan om du vill tillåta eller blockera B2B användare från vissa domäner.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/19/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 3b4b57dd2299c6278fe823f59a4f2c7d8721f712
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260128"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Tillåt eller blockera inbjudningar till B2B-användare från specifika organisationer

Du kan använda en lista över tillåtna eller en Neka-lista till Tillåt eller blockera inbjudningar till B2B användare från specifika organisationer. Till exempel om du vill blockera domäner för personliga e-postadress kan du konfigurera en Neka-lista som innehåller domäner som Gmail.com och Outlook.com. Om företaget har ett samarbete med andra företag som Contoso.com, Fabrikam.com och Litware.com och du vill begränsa inbjudningar till bara dessa organisationer, du kan lägga till Contoso.com, Fabrikam.com och Litware.com till din lista över tillåtna.
  
## <a name="important-considerations"></a>Viktiga överväganden

- Du kan skapa en lista över tillåtna eller en Neka-lista. Du kan inte ange båda typerna av listor. Som standard, oavsett domäner är inte i listan över tillåtna finns på listan över nekade och vice versa. 
- Du kan skapa en enda princip per organisation. Du kan uppdatera principen för att inkludera flera domäner eller kan du ta bort princip för att skapa en ny. 
- Den här listan fungerar oberoende av OneDrive för företag och SharePoint Online Tillåt /-block-listor. Om du vill begränsa enskilda fildelning i SharePoint Online måste du ställa in en Tillåt eller neka lista för OneDrive för företag och SharePoint Online. Mer information finns i [begränsad domäner som delar i SharePoint Online och OneDrive för företag](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- Den här listan gäller inte för externa användare som redan har lösts in inbjudan. Listan tillämpas när listan har konfigurerats. Om en användarinbjudan är i ett väntande tillstånd, och du har angett en princip som blockerar sin domän, misslyckas anslutningsförsöket att lösa in inbjudan.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Ange Tillåt eller neka listan principen i portal

Som standard den **Tillåt inbjudningar som ska skickas till en domän (mest omfattande)** inställningen är aktiverad. I det här fallet kan du erbjuda B2B användare från en organisation.

### <a name="add-a-deny-list"></a>Lägg till en Neka-lista

Detta är det vanligaste scenariot där din organisation vill arbeta med nästan alla organisationer, men vill hindra användare från vissa domäner bjudas in som B2B-användare.

Lägg till en Neka-lista:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Azure Active Directory** > **användare** > **användarinställningar**.
3. Under **externa användare**väljer **hantera inställningar för externt samarbete**.
4. Under **samarbete begränsningar**väljer **neka inbjudningar till de angivna domänerna**.
5. Under **MÅLDOMÄNER**, ange namnet på en av de domäner som du vill blockera. Ange varje domän på en ny rad för flera domäner. Exempel:

   ![Visar alternativet Neka med tillagda domäner](./media/allow-deny-list/DenyListSettings.png)
 
6. När du är klar klickar du på **spara**.

När du ställer in principen, om du försöker att bjuda in en användare från en blockerad domän får du ett meddelande om att användarens domän är blockerad för inbjudan principen.
 
### <a name="add-an-allow-list"></a>Lägg till en lista över tillåtna

Detta är en mer begränsande konfiguration, där du kan ange specifika domäner i listan över tillåtna och begränsa inbjudningar med andra organisationer eller domäner som inte anges. 

Om du vill använda en lista över tillåtna, se till att det tar tid att fullständigt utvärdera verksamheten behöver finns. Om du gör den här principen för begränsande kan användarna välja att skicka dokument via e-post eller hitta andra IT-sanktionerade sätt att samarbeta.


Lägg till en lista över tillåtna:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Azure Active Directory** > **användare** > **användarinställningar**.
3. Under **externa användare**väljer **hantera inställningar för externt samarbete**.
4. Under **samarbete begränsningar**väljer **Tillåt inbjudningar endast till de angivna domänerna (mest restriktiva)**.
5. Under **MÅLDOMÄNER**, ange namnet på en av de domäner som du vill tillåta. Ange varje domän på en ny rad för flera domäner. Exempel:

   ![Visar alternativet Tillåt med tillagda domäner](./media/allow-deny-list/AllowListSettings.png)
 
6. När du är klar klickar du på **spara**.

När du ställer in principen, om du försöker att bjuda in en användare från en domän som inte finns med på listan över tillåtna får ett meddelande om att användarens domän är blockerad för inbjudan principen.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Tillåt att växeln från Neka lista och vice versa 

Om du växlar från en princip till en annan ignorerar detta i befintliga konfiguration. Se till att säkerhetskopiera information om konfiguration innan du utför växeln. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Ange Tillåt eller neka listan principen med hjälp av PowerShell

### <a name="prerequisite"></a>Krav

Om du vill ange Tillåt eller neka listan med hjälp av PowerShell, måste du installera förhandsversionen av Azure Active Directory-modulen för Windows PowerShell. Mer specifikt installera AzureADPreview Modulversion 2.0.0.98 eller senare.

Kontrollera versionen för modulen (och om det är installerat):
 
1. Öppna Windows PowerShell som en förhöjd (Kör som administratör). 
2. Kör följande kommando för att se om du har alla versioner av det Azure Active Directory-modulen för Windows PowerShell installerad på datorn:

   ````powershell  
   Get-Module -ListAvailable AzureAD*
   ````

Om modulen inte är installerad eller om du inte har en version som krävs, gör du något av följande:

- Om inga resultat, kör du följande kommando för att installera den senaste versionen av modulen AzureADPreview:
  
   ````powershell  
   Install-Module AzureADPreview
   ````
- Om endast AzureAD modulen visas i resultaten, kör du följande kommandon för att installera modulen AzureADPreview: 

   ````powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ````
- Om endast AzureADPreview modulen visas i resultaten, men versionen som är mindre än 2.0.0.98, kör du följande kommandon för att uppdatera den: 

   ````powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ````

- Om både AzureAD och AzureADPreview moduler som visas i resultaten, men versionen av modulen AzureADPreview är mindre än 2.0.0.98, kör du följande kommandon för att uppdatera den: 

   ````powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ````

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Använda AzureADPolicy-cmdletar för att konfigurera principen

Skapa en Tillåt eller neka listan genom att använda den [ny AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) cmdlet. I följande exempel visas hur du ställer in en Neka-lista som blockerar ”live.com”-domän.

````powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
````

Här visas det här exemplet, men med den princip definition infogat.

````powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
````

Om du vill ange Tillåt eller neka listan princip, Använd den [Set AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) cmdlet. Exempel:

````powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
````

För att få principen kan använda den [Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) cmdlet. Exempel:

````powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
````

Ta bort principen med den [ta bort AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview) cmdlet. Exempel:

````powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
````

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure AD B2B finns [vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- Information om villkorlig åtkomst och B2B-samarbete finns [villkorlig åtkomst för användare för B2B-samarbete](conditional-access.md).



