---
title: PowerShell för Azure AD-roller i PIM - Azure AD | Microsoft-dokument
description: Hantera Azure AD-roller med PowerShell-cmdletar i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fa241a261b8dcb21dd39b5dacacac9aa4889304
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519654"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>PowerShell för Azure AD-roller i privilegierad identitetshantering

Den här artikeln innehåller instruktioner för hur du använder Azure Active Directory (Azure AD) PowerShell-cmdlets för att hantera Azure AD-roller i Privilegierad identitetshantering (PIM). Den berättar också hur du konfigurerar med Azure AD PowerShell-modulen.

> [!Note]
> Vår officiella PowerShell stöds bara om du använder den nya versionen av Azure AD Privileged Identity Management. Gå till Privileged Identity Management och se till att du har följande banner på snabbstartsbladet.
> [![kontrollera vilken version av privilegierad identitetshantering du har](media/pim-how-to-add-role-to-user/pim-new-version.png "Välj Azure AD-> privilegierad identitetshantering")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) Om du inte har den här bannern, vänta som vi för närvarande är i färd med att rulla ut denna uppdaterade upplevelse under de närmaste veckorna.
> PowerShell-cmdlets för privilegierad identitetshantering stöds via Azure AD Preview-modulen. Om du har använt en annan modul och den modulen nu returnerar ett felmeddelande, börja använda den här nya modulen. Om du har några produktionssystem byggda ovanpå en annan modul, vänligen kontaktapim_preview@microsoft.com

## <a name="installation-and-setup"></a>Installation och konfiguration

1. Installera azure AD-förhandsgranskningsmodulen

        Install-module AzureADPreview

1. Kontrollera att du har de behörigheter som krävs innan du fortsätter. Om du försöker utföra hanteringsuppgifter som att ge en rolltilldelning eller uppdatera rollinställningen kontrollerar du att du har rollen Global administratör eller Rollen Privilegierad roll. Om du bara försöker aktivera din egen tilldelning krävs inga behörigheter utöver standardanvändarbehörigheterna.

1. Anslut till Azure AD.

        $AzureAdCred = Get-Credential  
        Connect-AzureAD -Credential $AzureAdCred

1. Hitta ditt klient-ID genom att gå till **Azure Active Directory** > **Properties** > **Directory ID**. I avsnittet cmdletar använder du det här ID:t när du behöver ange resourceId.

    ![Hitta klient-ID:t i egenskaperna för Azure AD-organisationen](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> Följande avsnitt är enkla exempel som kan hjälpa dig att komma igång. Du hittar mer detaljerad dokumentation om följande https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_managementcmdlets på . Du måste dock ersätta "azureResources" i providerID-parametern med "aadRoles". Du måste också komma ihåg att använda klient-ID för din Azure AD-organisation som resourceId-parameter.

## <a name="retrieving-role-definitions"></a>Hämta rolldefinitioner

Använd följande cmdlet för att få alla inbyggda och anpassade Azure AD-roller i din Azure AD-organisation (klientorganisation). Det här viktiga steget ger dig mappningen mellan rollnamnet och rollenDefinitionId. RollenDefinitionId används i dessa cmdlets för att referera till en viss roll.

RollenDefinitionId är specifik för din Azure AD-organisation och skiljer sig från rollenDefinitionId som returneras av rollhanterings-API:et.

    Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26

Resultat:

![Hämta alla roller för Azure AD-organisationen](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>Hämta rolltilldelningar

Använd följande cmdlet för att hämta alla rolltilldelningar i din Azure AD-organisation.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"

Använd följande cmdlet för att hämta alla rolltilldelningar för en viss användare. Den här listan kallas även "Mina roller" i Azure AD-portalen. Den enda skillnaden här är att du har lagt till ett filter för ämnes-ID. Ämnes-ID:t i den här kontexten är användar-ID eller grupp-ID.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 

Använd följande cmdlet för att hämta alla rolltilldelningar för en viss roll. RollenDefinitionId här är ID:t som returneras av föregående cmdlet.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"

Cmdlets resulterar i en lista över rolltilldelningsobjekt som visas nedan. Ämnes-ID:et är användar-ID för den användare som rollen är tilldelad till. Tilldelningstillståndet kan antingen vara aktivt eller kvalificerat. Om användaren är aktiv och det finns ett ID i fältet LinkedEligibleRoleAssignmentId betyder det att rollen för närvarande är aktiverad.

Resultat:

![Hämta alla rolltilldelningar för Azure AD-organisationen](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>Tilldela en roll

Använd följande cmdlet för att skapa en kvalificerad tilldelning.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 

Schemat, som definierar början- och sluttiden för tilldelningen, är ett objekt som kan skapas som följande exempel:

    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.endDateTime = "2020-07-25T20:49:11.770Z"
> [!Note]
> Om värdet endDateTime är inställt på null anger det en permanent tilldelning.

## <a name="activate-a-role-assignment"></a>Aktivera en rolltilldelning

Använd följande cmdlet för att aktivera en kvalificerad tilldelning.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas" 

Den här cmdleten är nästan identisk med cmdleten för att skapa en rolltilldelning. Den viktigaste skillnaden mellan cmdlets är att för parametern -Type är aktiveringen "userAdd" i stället för "adminAdd". Den andra skillnaden är att parametern -AssignmentState är aktiv i stället för "Berättigat".

> [!Note]
> Det finns två begränsande scenarier för rollaktivering via PowerShell.
> 1. Om du behöver biljettsystem /biljettnummer i din rollinställning, finns det inget sätt att leverera dem som en parameter. Det skulle därför inte vara möjligt att aktivera rollen utanför Azure-portalen. Den här funktionen rullas ut till PowerShell under de närmaste månaderna.
> 1. Om du behöver multifaktorautentisering för rollaktivering finns det för närvarande inget sätt för PowerShell att utmana användaren när de aktiverar sin roll. I stället måste användarna utlösa MFA-utmaningen när de ansluter till Azure AD genom att följa [det här blogginlägget](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) från en av våra tekniker. Om du utvecklar en app för PIM är en möjlig implementering att utmana användare och återansluta dem till modulen när de har fått ett "MfaRule"-fel.

## <a name="retrieving-and-updating-role-settings"></a>Hämta och uppdatera rollinställningar

Använd följande cmdlet för att hämta alla rollinställningar i din Azure AD-organisation.

    Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'" 

Det finns fyra huvudobjekt i inställningen. Endast tre av dessa objekt används för närvarande av PIM. UserMemberSettings är aktiveringsinställningar, AdminEligibleSettings är tilldelningsinställningar för kvalificerade tilldelningar och Administratörsinställningarna är tilldelningsinställningar för aktiva tilldelningar.

[![](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png "Get and update role settings")](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

Om du vill uppdatera rollinställningen måste du först definiera ett inställningsobjekt på följande sätt:

    $setting = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting 
    $setting.RuleIdentifier = "JustificationRule"
    $setting.Setting = "{'required':false}"

Du kan sedan gå vidare och tillämpa inställningen på ett av objekten för en viss roll som visas nedan. ID:t här är rollinställnings-ID:t som kan hämtas från resultatet av cmdlet för listrollinställningar.

    Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $setting 

## <a name="next-steps"></a>Nästa steg

- [Tilldela en anpassad Azure AD-roll](azure-ad-custom-roles-assign.md)
- [Ta bort eller uppdatera en anpassad Azure AD-rolltilldelning](azure-ad-custom-roles-update-remove.md)
- [Konfigurera en anpassad Azure AD-rolltilldelning](azure-ad-custom-roles-configure.md)
- [Rolldefinitioner i Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
