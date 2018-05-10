---
title: Återställ eller permanent ta bort en nyligen borttagna användare i Azure Active Directory | Microsoft Docs
description: Återställa en borttagen användare, visa återställningsbara användare eller permanent ta bort en användare i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 05/09/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 8dbb546954f8eee0bf997b3d2f4f92d67c0b7869
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="restore-a-deleted-user-in-azure-active-directory"></a>Återställa en borttagen användare i Azure Active Directory

Den här artikeln innehåller anvisningar för att återställa eller ta bort en användare som tidigare har tagits bort permanent. När du tar bort en användare i Azure Active Directory (AD Azure) bevaras den borttagna användaren i 30 dagar från det datum då tas bort. Under den tiden kan kan användaren och dess egenskaper återställas. 

> [!wARNING]
> Användaren kan inte återställas när den bort permanent.


## <a name="how-to-restore-a-recently-deleted-user"></a>Återställa en nyligen borttagna användare
När en användare har nyligen tagits bort, bevaras all kataloginformation. Om användaren återställs återställs den också.

1. I den [administrationscentret för Azure AD](https://aad.portal.azure.com)väljer **användare** &gt; **bort användare**. 
2. Välj en eller flera användare som nyligen tagits bort.
3. Välj **återställning användaren**.

## <a name="how-to-permanently-delete-a-recently-deleted-user"></a>Hur man permanent tar bort en nyligen borttagna användare

1. I den [administrationscentret för Azure AD](https://aad.portal.azure.com)väljer **användare** &gt; **bort användare**. 
2. Välj en eller flera användare som nyligen tagits bort.
3. Välj **ta bort permanent**.

## <a name="required-permissions"></a>Nödvändiga behörigheter
Följande behörigheter är tillräckliga för att återställa en användare.

Roll  | Behörigheter 
--------- | ---------
Företagsadministratör<p>Partnersupport, nivå 1<p>Partnersupport, nivå 2<p>Användarkonto-administratör | Kan återställa borttagna användare 
Företagsadministratör<p>Partnersupport, nivå 1<p>Partnersupport, nivå 2<p>Användarkonto-administratör | Permanent ta bort användare

## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om hantering av Azure Active Directory-användare.

* [Snabbstart: Lägg till eller ta bort användare till Azure Active Directory](add-users-azure-active-directory.md)
* [Hantera användarprofiler](active-directory-users-profile-azure-portal.md)
* [Lägg till gästanvändare från en annan katalog](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Tilldela en användare till en roll i din Azure AD](active-directory-users-assign-role-azure-portal.md)
