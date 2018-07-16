---
title: Hantera de grupper som din grupp tillhör i Azure AD | Microsoft Docs
description: Grupper kan innehålla andra grupper i Azure Active Directory. Här ser du hur du hanterar dessa medlemskap.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 10/10/2017
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: 8a71677ae3ceb5617f0a817a8eff438d5e3f2774
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860443"
---
# <a name="manage-to-which-groups-a-group-belongs-in-your-azure-active-directory-tenant"></a>Hantera vilka grupper som en grupp hör till i din Azure Active Directory-klientorganisation
Grupper kan innehålla andra grupper i Azure Active Directory. Här ser du hur du hanterar dessa medlemskap.

## <a name="how-do-i-find-the-groups-of-which-my-group-is-a-member"></a>Hur gör jag för att hitta de grupper som min grupp är medlem i?
1. Logga in på [Azure AD administratörscenter](https://aad.portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **Användare och grupper**.

   ![Bild – Öppna användare och grupper](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
1. Välj **Alla grupper**.

   ![Bild – Välja grupper](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
1. Välj en grupp.
2. Välj **Gruppmedlemskap**.

   ![Bild – Öppna gruppmedlemskap](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
1. Om du vill lägga till en grupp som medlem i en annan grupp väljer du kommandot **Lägg till** på bladet **Grupp – gruppmedlemskap**.
2. Välj en grupp på bladet **Välj grupp** och välj sedan knappen **Välj** längst ned på bladet. Du kan bara lägga till en grupp till en grupp i taget. Rutan **Användare** filtrerar visningen baserat på matchningen av din inmatning till någon del av ett användar- eller enhetsnamn. Jokertecken accepteras inte i den rutan.

   ![Lägga till ett gruppmedlemskap](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. Om du vill ta bort en grupp som medlem i en annan grupp väljer du gruppen på bladet **Grupp – gruppmedlemskap**.
9. Välj kommandot **Ta bort** och bekräfta valet.

   ![kommando för att ta bort medlemskap](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. När du har ändrat gruppmedlemskapen för din grupp väljer du **Spara**.

## <a name="additional-information"></a>Ytterligare information
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Visa befintliga grupper](active-directory-groups-view-azure-portal.md)
* [Skapa en ny grupp och lägga till medlemmar](active-directory-groups-create-azure-portal.md)
* [Hantera inställningar för en grupp](active-directory-groups-settings-azure-portal.md)
* [Hantera medlemmar i en grupp](active-directory-groups-members-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](../users-groups-roles/groups-dynamic-membership.md)
