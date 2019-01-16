---
title: Återställa en borttagen Office 365-grupp i Azure AD | Microsoft Docs
description: Återställa en borttagen grupp, visa återställningsbara grupper och ta bort en grupp permanent i Azure Active Directory
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 08/28/2017
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 090c6c2d20bf169a3b3362cc796f6fc5c04b3c83
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54155039"
---
# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Återställa en borttagen Office 365-grupp i Azure Active Directory

När du tar bort en Office 365-grupp i Azure Active Directory (Azure AD) bevaras den borttagna gruppen, men visas inte, i 30 dagar räknat från borttagningsdatumet. På så sätt kan gruppen och dess innehåll återställas om det behövs. Den här funktionen är begränsad till Office 365-grupper i Azure AD. Den är inte tillgänglig för säkerhetsgrupper och distributionsgrupper.

> [!NOTE]
> Använd inte `Remove-MsolGroup` eftersom det gör att gruppen tas bort permanent. Använd alltid `Remove-AzureADMSGroup` för att ta bort en O365-grupp.

Behörigheterna som krävs för att återställa en grupp kan vara någon av följande:

Roll | Behörigheter
--------- | ---------
Företagsadministratör, Partnersupport, nivå 2 och InTune-tjänstadministratörer | Kan återställa alla borttagna Office 365-grupper
Användarkontoadministratör och Partnersupport, nivå 2 | Kan återställa alla borttagna Office 365-grupper utom de som tilldelats rollen Företagsadministratör
Användare | Kan återställa alla borttagna Office 365-grupper som de äger


## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore"></a>Visa de borttagna Office 365-grupper som är tillgängliga för återställning

Du kan använda följande cmdlets för att visa de borttagna grupperna och kontrollera att den eller de grupper som du är intresserad av inte har raderats permanent än. Dessa cmdlets är en del av [Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD/). Mer information om den här modulen finns i artikeln [Azure Active Directory PowerShell Version 2](/powershell/azure/install-adv2?view=azureadps-2.0).

1.  Kör följande cmdlet för att visa alla borttagna Office 365-grupper i din klientorganisation som fortfarande är tillgängliga för återställning.
   
  ```
  Get-AzureADMSDeletedGroup
  ```

2.  Om du har objectID för en specifik grupp (och du kan hämta det från cmdleten i steg 1), kan du även köra följande cmdlet för att kontrollera att den specifika borttagna gruppen inte har raderats permanent än.
  
  ```
  Get-AzureADMSDeletedGroup –Id <objectId>
  ```

## <a name="how-to-restore-your-deleted-office-365-group"></a>Återställa den borttagna Office 365-gruppen
När du har kontrollerat att gruppen fortfarande är tillgänglig för återställning kan du återställa den borttagna gruppen med hjälp av något av följande steg. Om gruppen innehåller dokument, SharePoint-webbplatser eller andra beständiga objekt, kan det ta upp till 24 timmar att helt återställa en grupp och dess innehåll.

1.  Kör följande cmdlet för att återställa gruppen och dess innehåll.
 
 ```
 Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
 ``` 

Du kan också köra följande cmdlet om du vill ta bort den borttagna gruppen permanent.
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
