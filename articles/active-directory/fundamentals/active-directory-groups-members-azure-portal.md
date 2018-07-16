---
title: Hantera medlemmarna för en grupp i Azure AD | Microsoft Docs
description: Lägga till eller ta bort användare och enheter från en grupp i Azure Active Directory
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
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: 947b0c11aba211530e3ae25d6617079bcaf2995f
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860491"
---
# <a name="manage-group-membership-for-users-in-your-azure-active-directory-tenant"></a>Hantera gruppmedlemskap för användare i din Azure Active Directory-klientorganisation
Den här artikeln förklarar hur du hanterar medlemmarna för en grupp i Azure Active Directory (AD Azure).

## <a name="how-do-i-find-the-members-and-manage-them"></a>Hur gör jag för att hitta och hantera medlemmarna?
1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **Alla tjänster**, ange **Användare och grupper** i textrutan och tryck på **Retur**.

   ![Öppna användarhantering](./media/active-directory-groups-members-azure-portal/search-user-management.png)
3. På bladet **Användare och grupper** väljer du **Alla grupper**.

   ![Öppna gruppbladet](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)
4. Välj en grupp på bladet **Användare och grupper – alla grupper**.
5. Välj **Medlemmar** på bladet **Grupp – *gruppnamn***.

   ![Öppna bladet Medlemmar](./media/active-directory-groups-members-azure-portal/view-group-members.png)
6. Du lägger till medlemmar i gruppen genom att välja **Lägg till medlemmar** på bladet **Grupp – medlemmar**.

   ![Kommandot Lägg till medlemmar](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)
7. På bladet **Medlemmar** väljer du en eller flera användare eller enheter att lägga till i gruppen och väljer knappen **Välj** längst ned på bladet för att lägga till dem i gruppen. Rutan **Användare** filtrerar visningen baserat på matchningen av din inmatning till någon del av ett användar- eller enhetsnamn. Jokertecken accepteras inte i den rutan.
8. Om du vill ta bort medlemmar från gruppen väljer du en medlem på bladet **Grupp – medlemmar**.
9. På bladet ***medlemsnamn*** väljer du kommandot **Ta bort** och bekräftar ditt val.

   ![kommando för att ta bort medlemmar](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)
10. När du har ändrat medlemmarna för gruppen väljer du **Spara**.

## <a name="additional-information"></a>Ytterligare information
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Visa befintliga grupper](active-directory-groups-view-azure-portal.md)
* [Skapa en ny grupp och lägga till medlemmar](active-directory-groups-create-azure-portal.md)
* [Hantera inställningar för en grupp](active-directory-groups-settings-azure-portal.md)
* [Hantera medlemskap i en grupp](active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](../users-groups-roles/groups-dynamic-membership.md)
