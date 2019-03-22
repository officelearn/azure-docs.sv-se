---
title: Återställa en borttagen Office 365-grupp - Azure AD | Microsoft Docs
description: Så återställer du en borttagen grupp, visar återställningsbara grupper och tar bort en grupp permanent i Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81bf41dd183944a43d9558d0aec0c416d30620b2
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202579"
---
# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Återställa en borttagen Office 365-grupp i Azure Active Directory

När du tar bort en Office 365-grupp i Azure Active Directory (Azure AD) bevaras den borttagna gruppen, men visas inte, i 30 dagar räknat från borttagningsdatumet. På så sätt kan gruppen och dess innehåll återställas om det behövs. Den här funktionen är begränsad till Office 365-grupper i Azure AD. Den är inte tillgänglig för säkerhetsgrupper och distributionsgrupper.

> [!NOTE]
> Använd inte `Remove-MsolGroup` eftersom det gör att gruppen tas bort permanent. Använd alltid `Remove-AzureADMSGroup` för att ta bort en Office 365-grupp.

Behörigheterna som krävs för att återställa en grupp kan vara någon av följande:

Roll | Behörigheter
--------- | ---------
Global administratör, support för Partner – nivå 2 och Intune-administratör | Kan återställa alla borttagna Office 365-grupper
Användaradministratör och support för Partner nivå 1 | Kan återställa alla borttagna Office 365-grupper utom de grupper som tilldelats till rollen Företagsadministratör
Användare | Återställa en borttagen Office 365-grupp som de äger

## <a name="view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore"></a>Visa och hantera de borttagna Office 365-grupper som är tillgängliga för återställning

1. Logga in på den [Azure AD administratörscenter](https://aad.portal.azure.com) användare med ett administratörskonto.

2. Välj **Grupper** och sedan **Borttagna grupper** för att visa de borttagna grupper som är tillgängliga för återställning.

    ![Visa grupper som är tillgängliga för återställning](media/groups-lifecycle/deleted-groups3.png)

3. På bladet **Borttagna grupper** kan du:

   - Återställa den borttagna gruppen och dess innehåll genom att välja **Återställ grupp**.
   - Ta bort den borttagna gruppen permanent genom att välja **Ta bort permanent**. Du måste vara administratör för att ta bort en grupp permanent.

## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore-using-powershell"></a>Visa de borttagna Office 365-grupper som är tillgängliga för återställning med hjälp av PowerShell
Du kan använda följande cmdlets för att visa de borttagna grupperna och kontrollera att den eller de grupper som du är intresserad av inte har raderats permanent än. Dessa cmdlets är en del av [Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD/). Mer information om den här modulen finns i artikeln [Azure Active Directory PowerShell Version 2](/powershell/azure/install-adv2?view=azureadps-2.0).

1.  Kör följande cmdlet för att visa alla borttagna Office 365-grupper i din klientorganisation som fortfarande är tillgängliga för återställning.
   
    ```
    Get-AzureADMSDeletedGroup
    ```

2.  Om du har objectID för en specifik grupp (och du kan hämta det från cmdleten i steg 1), kan du även köra följande cmdlet för att kontrollera att den specifika borttagna gruppen inte har raderats permanent än.

    ```
    Get-AzureADMSDeletedGroup –Id <objectId>
    ```

## <a name="how-to-restore-your-deleted-office-365-group-using-powershell"></a>Så återställer du den borttagna Office 365-gruppen med hjälp av PowerShell
När du har kontrollerat att gruppen fortfarande är tillgänglig för återställning kan du återställa den borttagna gruppen med hjälp av något av följande steg. Om gruppen innehåller dokument, SharePoint-webbplatser eller andra beständiga objekt, kan det ta upp till 24 timmar att helt återställa en grupp och dess innehåll.

1. Kör följande cmdlet för att återställa gruppen och dess innehåll.
 
   ```
    Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
    ``` 

2. Du kan också köra följande cmdlet om du vill ta bort den borttagna gruppen permanent.
    
    ```
    Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
    ```

## <a name="how-do-you-know-this-worked"></a>Hur vet du att åtgärden har fungerat?

Du kan kontrollera att en Office 365-grupp har återställts med cmdleten `Get-AzureADGroup –ObjectId <objectId>` som visar information om gruppen. När återställningsbegäran har slutförts:

- Gruppen visas i det vänstra navigeringsfältet i Exchange
- Planen för gruppen visas i Planner
- Eventuella SharePoint-webbplatser och allt deras innehåll är tillgängliga
- Gruppen kan nås från någon av Exchange-slutpunkterna och andra Office 365-arbetsbelastningar som har stöd för Office 365-grupper

## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure Active Directory-grupper.

* [Visa befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Hantera inställningar för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Hantera medlemmar i en grupp](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](groups-dynamic-membership.md)
