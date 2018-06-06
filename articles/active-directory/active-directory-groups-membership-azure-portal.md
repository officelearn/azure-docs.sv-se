---
title: Hantera grupper som gruppen tillhör i Azure Active Directory | Microsoft Docs
description: Grupper kan innehålla andra grupper i Azure Active Directory. Här är hur du hanterar dessa medlemskap.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 9865f871f925b4ab1dd290b0afba3258581f0bf6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714331"
---
# <a name="manage-to-which-groups-a-group-belongs-in-your-azure-active-directory-tenant"></a>Hantera vilka grupper som en grupp tillhör i Azure Active Directory-klient
Grupper kan innehålla andra grupper i Azure Active Directory. Här är hur du hanterar dessa medlemskap.

## <a name="how-do-i-find-the-groups-of-which-my-group-is-a-member"></a>Hur hittar grupperna som är medlem i min grupp?
1. Logga in på [Azure AD administratörscenter](https://aad.portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **användare och grupper**.

   ![Öppna användare och grupper bild](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
1. Välj **alla grupper**.

   ![Välja grupper bild](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
1. Välj en grupp.
2. Välj **gruppmedlemskap**.

   ![Öppna grupp medlemskap bild](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
1. Att lägga till gruppen som en medlem i en annan grupp på den **Group - gruppmedlemskap** bladet väljer den **Lägg till** kommando.
2. Väljer du en grupp i **Välj grupp** bladet och välj sedan den **Välj** längst ned på bladet. Du kan lägga till din grupp bara en grupp i taget. Den **användaren** rutan filtrerar baserat på matchning inmatningen till alla delar av namnet på en användare eller enhet. Inga jokertecken godkänns i rutan.

   ![Lägg till en gruppmedlemskap](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. Ta bort gruppen som en medlem i en annan grupp på den **Group - gruppmedlemskap** bladet Välj en grupp.
9. Välj den **ta bort** kommando och bekräfta valet i Kommandotolken.

   ![ta bort medlemskap kommandot](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. När du har ändrat medlemskap för gruppen, Välj **spara**.

## <a name="additional-information"></a>Ytterligare information
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Se befintliga grupper](active-directory-groups-view-azure-portal.md)
* [Skapa en ny grupp och lägga till medlemmar](active-directory-groups-create-azure-portal.md)
* [Hantera inställningar för en grupp](active-directory-groups-settings-azure-portal.md)
* [Hantera medlemmar i en grupp](active-directory-groups-members-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](active-directory-groups-dynamic-membership-azure-portal.md)
