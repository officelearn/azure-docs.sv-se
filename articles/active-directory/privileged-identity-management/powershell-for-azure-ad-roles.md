---
title: PowerShell för Azure AD-roller i PIM – Azure AD | Microsoft Docs
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
ms.date: 05/11/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c42c0dd3848ec913f991e4b07612669c5a25c9f1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197276"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>PowerShell för Azure AD-roller i Privileged Identity Management

Den här artikeln innehåller instruktioner för att använda PowerShell-cmdlets för Azure Active Directory (Azure AD) för att hantera Azure AD-roller i Privileged Identity Management (PIM). Du får också information om hur du konfigurerar med Azure AD PowerShell-modulen.

> [!Note]
> Vår officiella PowerShell stöds bara om du använder den nya versionen av Azure AD Privileged Identity Management. Gå till Privileged Identity Management och kontrol lera att du har följande banderoll på bladet snabb start.
> [![kontrol lera vilken version av Privileged Identity Management du har](media/pim-how-to-add-role-to-user/pim-new-version.png "Välj Azure AD-> Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) Om du inte har den här banderollen, vänta medan vi för närvarande håller på att lansera den här uppdaterade upplevelsen under de kommande veckorna.
> Privileged Identity Management PowerShell-cmdletar stöds via för hands versionen av Azure AD. Om du har använt en annan modul och den modulen nu returnerar ett fel meddelande, kan du börja använda den här nya modulen. Om du har ett produktions system som bygger ovanpå en annan modul kan du kontaktapim_preview@microsoft.com

## <a name="installation-and-setup"></a>Installation och konfiguration

1. Installera Azure AD Preview-modulen

        Install-module AzureADPreview

1. Se till att du har de nödvändiga roll behörigheterna innan du fortsätter. Om du försöker utföra hanterings uppgifter som att ge roll tilldelning eller uppdatera roll inställningar, se till att du har rollen som global administratör eller administratör för privilegie rad roll. Om du bara försöker aktivera din egen tilldelning krävs inga behörigheter utöver standard användar behörigheterna.

1. Anslut till Azure AD.

        $AzureAdCred = Get-Credential  
        Connect-AzureAD -Credential $AzureAdCred

1. Hitta klient-ID: t för din Azure AD-organisation genom att gå till **Azure Active Directory**  >  **Egenskaper**  >  **katalog-ID**. I avsnittet cmdlets använder du detta ID när du behöver ange resourceId.

    ![Hitta organisations-ID: t i egenskaperna för Azure AD-organisationen](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> Följande avsnitt innehåller enkla exempel som hjälper dig att komma igång. Du hittar mer detaljerad dokumentation om följande cmdlets på https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management . Du måste dock ersätta "azureResources" i parametern providerID med "aadRoles". Du måste också komma ihåg att använda organisations-ID: t för din Azure AD-organisation som resourceId-parameter.

## <a name="retrieving-role-definitions"></a>Hämtar roll definitioner

Använd följande cmdlet för att hämta alla inbyggda och anpassade Azure AD-roller i din Azure AD-organisation. Det här viktiga steget ger mappningen mellan roll namnet och roleDefinitionId. RoleDefinitionId används i alla dessa cmdletar för att referera till en speciell roll.

RoleDefinitionId är unik för din Azure AD-organisation och skiljer sig från roleDefinitionId som returneras av roll hanterings-API: et.

    Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26

Resultat:

![Hämta alla roller för Azure AD-organisationen](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>Hämtar roll tilldelningar

Använd följande cmdlet för att hämta alla roll tilldelningar i din Azure AD-organisation.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"

Använd följande cmdlet för att hämta alla roll tilldelningar för en viss användare. Den här listan kallas även "mina roller" i Azure AD-portalen. Den enda skillnaden är att du har lagt till ett filter för ämnes-ID: t. Ämnes-ID: t i den här kontexten är användar-ID eller grupp-ID.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 

Använd följande cmdlet för att hämta alla roll tilldelningar för en viss roll. RoleDefinitionId här är det ID som returneras av föregående cmdlet.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"

Cmdletarna resulterar i en lista över roll tilldelnings objekt som visas nedan. Ämnes-ID är användar-ID för den användare som rollen är tilldelad. Tilldelningens status kan antingen vara aktiv eller berättigad. Om användaren är aktiv och det finns ett ID i fältet LinkedEligibleRoleAssignmentId innebär det att rollen för närvarande är aktive rad.

Resultat:

![Hämta alla roll tilldelningar för Azure AD-organisationen](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>Tilldela en roll

Använd följande cmdlet för att skapa en berättigad tilldelning.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 

Schemat, som definierar start-och slut tid för tilldelningen, är ett objekt som kan skapas som följande exempel:

    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.endDateTime = "2020-07-25T20:49:11.770Z"
> [!Note]
> Om värdet för endDateTime är inställt på null, indikerar det en permanent tilldelning.

## <a name="activate-a-role-assignment"></a>Aktivera en roll tilldelning

Använd följande cmdlet för att aktivera en berättigad tilldelning.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas" 

Denna cmdlet är nästan identisk med cmdlet: en för att skapa en roll tilldelning. Den största skillnaden mellan cmdletarna är att för parametern – type är aktiveringen "userAdd" i stället för "adminAdd". Den andra skillnaden är att parametern – AssignmentState är "aktiv" i stället för "giltig".

> [!Note]
> Det finns två begränsande scenarier för roll aktivering via PowerShell.
> 1. Om du kräver biljett system/biljett nummer i din roll inställning finns det inget sätt att ange dem som en parameter. Därför är det inte möjligt att aktivera rollen utanför Azure Portal. Den här funktionen distribueras till PowerShell under de kommande månaderna.
> 1. Om du behöver Multi-Factor Authentication för roll aktivering finns det för närvarande inget sätt för PowerShell att utmana användaren när de aktiverar sin roll. I stället måste användarna utlösa MFA-utmaningen när de ansluter till Azure AD genom att följa [det här blogg inlägget](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) från någon av våra tekniker. Om du utvecklar en app för PIM, är det en möjlig implementering att utmana användare och återansluta dem till modulen när de får ett "MfaRule"-fel.

## <a name="retrieving-and-updating-role-settings"></a>Hämtar och uppdaterar roll inställningar

Använd följande cmdlet för att hämta alla roll inställningar i din Azure AD-organisation.

    Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'" 

Det finns fyra huvud objekt i inställningen. Endast tre av de här objekten används för närvarande av PIM. UserMemberSettings är aktiverings inställningar, AdminEligibleSettings är tilldelnings inställningar för kvalificerade tilldelningar och AdminmemberSettings är tilldelnings inställningar för aktiva tilldelningar.

[![](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png "Get and update role settings")](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

Om du vill uppdatera roll inställningen måste du hämta det befintliga inställnings objektet för en viss roll och göra ändringar i den:

    $setting = Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "roleDefinitionId eq 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'"
    $setting.UserMemberSetting.justificationRule = '{"required":false}'

Du kan sedan gå vidare och tillämpa inställningen på ett av objekten för en viss roll som visas nedan. ID: t här är roll inställnings-ID: t som kan hämtas från resultatet av cmdleten List Role Settings.

    Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $setting 

## <a name="next-steps"></a>Nästa steg

- [Tilldela en anpassad Azure AD-roll](azure-ad-custom-roles-assign.md)
- [Ta bort eller uppdatera en anpassad roll tilldelning för Azure AD](azure-ad-custom-roles-update-remove.md)
- [Konfigurera en anpassad roll tilldelning för Azure AD](azure-ad-custom-roles-configure.md)
- [Roll definitioner i Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
