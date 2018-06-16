---
title: Hantera medlemmar för en grupp i Azure Active Directory | Microsoft Docs
description: Lägga till eller ta bort användare och enheter från en grupp i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: 43514a612cbc8dcba5ebf33bea369c1d7855ab58
ms.sourcegitcommit: 5821eef990c26fa045e4beacce39f6b02b83156b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/15/2018
ms.locfileid: "35664652"
---
# <a name="manage-group-membership-for-users-in-your-azure-active-directory-tenant"></a>Hantera gruppmedlemskap för användare i din Azure Active Directory-klient
Den här artikeln förklarar hur du hanterar medlemmar för en grupp i Azure Active Directory (AD Azure).

## <a name="how-do-i-find-the-members-and-manage-them"></a>Hur gör hitta medlemmarna och hantera dem?
1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **alla tjänster**, ange **användare och grupper** i textrutan och välj sedan **RETUR**.

   ![Öppna användarhantering](./media/active-directory-groups-members-azure-portal/search-user-management.png)
3. På den **användare och grupper** bladet väljer **alla grupper**.

   ![Öppna bladet grupper](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)
4. På den **användare och grupper – alla grupper** bladet Välj en grupp.
5. På den **grupp - *groupname***  bladet väljer **medlemmar**.

   ![Öppna bladet medlemmar](./media/active-directory-groups-members-azure-portal/view-group-members.png)
6. Lägga till medlemmar i gruppen på den **grupp - medlemmar** bladet väljer **lägga till medlemmar**.

   ![Lägga till medlemmar kommando](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)
7. På den **medlemmar** bladet, Välj en eller flera användare eller enheter att lägga till i gruppen och välj den **Välj** längst ned på bladet för att lägga till dem i gruppen. Den **användaren** rutan filtrerar baserat på matchning inmatningen till alla delar av namnet på en användare eller enhet. Inga jokertecken godkänns i rutan.
8. Ta bort medlemmar från gruppen på den **grupp - medlemmar** bladet Välj en medlem.
9. På den ***membername*** bladet väljer den **ta bort** kommando och bekräfta valet i Kommandotolken.

   ![ta bort medlemmar kommandot](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)
10. När du har ändrat medlemmar för gruppen, Välj **spara**.

## <a name="additional-information"></a>Ytterligare information
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Se befintliga grupper](active-directory-groups-view-azure-portal.md)
* [Skapa en ny grupp och lägga till medlemmar](active-directory-groups-create-azure-portal.md)
* [Hantera inställningar för en grupp](active-directory-groups-settings-azure-portal.md)
* [Hantera medlemskap i en grupp](active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](../active-directory-groups-dynamic-membership-azure-portal.md)
