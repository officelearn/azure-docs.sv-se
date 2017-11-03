---
title: "Återställa en borttagen Office 365-grupp i Azure Active Directory | Microsoft Docs"
description: "Återställa en borttagen grupp, visa återställningsbara grupper och permamnently ta bort en grupp i Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: cc5f232a-1e77-45c2-b28b-1fcb4621725c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 5d06cee492e3360bcaf8c7663c97d0c8ed3e243f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Återställa en borttagen Office 365-grupp i Azure Active Directory

När du tar bort en Office 365-grupp i Azure Active Directory (AD Azure) är den borttagna gruppen kvarhållna men är inte synliga för 30 dagar från det datum då tas bort. Detta är så att gruppen och dess innehåll kan återställas om det behövs. Den här funktionen är begränsad till Office 365-grupper i Azure AD. Det är inte tillgänglig för säkerhetsgrupper och distributionsgrupper.

> [!NOTE] 
> Använd inte `Remove-MsolGroup` eftersom det tar bort gruppen permanent. Använd alltid `Remove-AzureADMSGroup` att ta bort en grupp för O365. 

De behörigheter som krävs för att återställa en grupp kan vara något av följande:

Roll  | Behörigheter 
--------- | ---------
Företagsadministratör, Partner Tier2 stöd och InTune-tjänsten-administratörer | Återställa en borttagen grupp för Office 365 
Stöd för användaren kontoadministratör och Partner Tier1 | Återställa alla borttagna Office 365-gruppen utom de som tilldelats till rollen företagsadministratör 
Användare | Återställa en borttagen Office 365-grupp som de ägs 


## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore"></a>Visa de borttagna Office 365-grupper som är tillgängliga för återställning
Följande cmdlets kan användas för att visa de borttagna grupperna för att verifiera att den eller de som du är intresserad inte har ännu har permanent bort. Dessa cmdletar är en del av den [Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD/). Mer information om den här modulen kan hittas i den [Azure Active Directory PowerShell Version 2](/powershell/azure/install-adv2?view=azureadps-2.0) artikel.

1.  Kör följande cmdlet bort om du vill visa alla Office 365-grupper i din klient som är fortfarande tillgängliga för återställning.
  ```
  Get-AzureADMSDeletedGroup
  ```

2.  Alternativt, om du vet objectID för en specifik grupp (och du får från cmdlet i steg 1) kör du följande cmdlet för att verifiera att den specifika borttagna gruppen inte har ännu permanent rensats.
  ```
  Get-AzureADMSDeletedGroup –Id <objectId>
  ```



## <a name="how-to-restore-your-deleted-office-365-group"></a>Så här återställer du din Office 365-grupp som tagits bort
När du har kontrollerat att gruppen är fortfarande tillgänglig för återställning kan du återställa den borttagna gruppen med något av följande steg. Om gruppen innehåller dokument, SP platser eller andra beständiga objekt, kan det ta upp till 24 timmar för att återställa en grupp och dess innehåll.

1.  Kör följande cmdlet om du vill återställa gruppen och dess innehåll.
  
  ```
  Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
  ``` 

Alternativt kan du köra följande cmdlet om du vill ta bort den borttagna gruppen.
  ```
  Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
  ```

## <a name="how-do-you-know-this-worked"></a>Hur vet du detta arbetat?
För att verifiera att du har har återställt en Office 365-grupp, kör de `Get-AzureADGroup –ObjectId <objectId>` för att visa information om gruppen. Efter återställningen har begäran slutförts:
- Gruppen visas i det vänstra navigeringsfältet i Exchange
- Planera för gruppen visas i Planner
- Sharepoint-webbplatser och allt innehåll ska vara tillgängliga
- Gruppen kan nås från någon av Exchange-slutpunkter och andra Office 365-arbetsbelastningar som har stöd för Office 365-grupper


## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure Active Directory-grupper.

* [Se befintliga grupper](active-directory-groups-view-azure-portal.md)
* [Hantera inställningar för en grupp](active-directory-groups-settings-azure-portal.md)
* [Hantera medlemmar i en grupp](active-directory-groups-members-azure-portal.md)
* [Hantera medlemskap i en grupp](active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](active-directory-groups-dynamic-membership-azure-portal.md)
