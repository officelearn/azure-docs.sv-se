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
ms.component: fundamentals
ms.topic: article
ms.date: 05/09/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: e743bdd5fd7e302e4c7c5d229bb14753776df079
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723538"
---
# <a name="restore-a-deleted-user-in-azure-active-directory"></a>Återställa en borttagen användare i Azure Active Directory

Den här artikeln innehåller anvisningar för att återställa eller ta bort en användare som tidigare har tagits bort permanent. När du tar bort en användare i Azure Active Directory (AD Azure) bevaras den borttagna användaren i 30 dagar från det datum då tas bort. Under den tiden kan kan användaren och dess egenskaper återställas. 

> [!WARNING]
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

Roll | Behörigheter 
--------- | ---------
Företagsadministratör<p>Partnersupport, nivå 1<p>Partnersupport, nivå 2<p>Användarkonto-administratör | Kan återställa borttagna användare 
Företagsadministratör<p>Partnersupport, nivå 1<p>Partnersupport, nivå 2<p>Användarkonto-administratör | Permanent ta bort användare

## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om hantering av Azure Active Directory-användare.

* [Snabbstart: Lägg till eller ta bort användare till Azure Active Directory](add-users-azure-active-directory.md)
* [Hantera användarprofiler](active-directory-users-profile-azure-portal.md)
* [Lägg till gästanvändare från en annan katalog](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Tilldela en användare till en roll i din Azure AD](active-directory-users-assign-role-azure-portal.md)
