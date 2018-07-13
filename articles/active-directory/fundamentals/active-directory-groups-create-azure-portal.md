---
title: Skapa en grupp för användare i Azure AD | Microsoft Docs
description: Så skapar du en grupp i Azure Active Directory och lägger till medlemmar i gruppen
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 08/04/2017
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 3c71c9c49413045e3a730c10e90ea3c12648b4cb
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857735"
---
# <a name="create-a-group-and-add-members-in-azure-active-directory"></a>Skapa en grupp och lägga till medlemmar i Azure Active Directory
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-groups-create-azure-portal.md)
> * [PowerShell](../users-groups-roles/groups-settings-v2-cmdlets.md)

Den här artikeln beskriver hur du skapar och fyller i en ny grupp i Azure Active Directory. Använd en grupp för att utföra hanteringsuppgifter som att tilldela licenser eller behörigheter till flera användare eller enheter på en gång.

## <a name="how-do-i-create-a-group"></a>Hur skapar jag en grupp?
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **Alla tjänster**, ange **Användare och grupper** i textrutan och tryck på **Retur**.

   ![Öppna användarhantering](./media/active-directory-groups-create-azure-portal/search-user-management.png)
3. På bladet **Användare och grupper** väljer du **Alla grupper**.

   ![Öppna gruppbladet](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)
4. På bladet **Användare och grupper – alla grupper** väljer du kommandot **Lägg till**.

   ![Välja kommandot Lägg till](./media/active-directory-groups-create-azure-portal/add-group-command.png)
5. På bladet **Grupp** lägger du till ett namn och en beskrivning för gruppen.
6. För att välja medlemmar som ska läggas till i gruppen väljer du **Tilldelad** i rutan **Medlemstyp** och väljer sedan **Medlemmar**. Mer information om hur du hanterar medlemskap i en grupp dynamiskt finns i [Använda attribut för att skapa avancerade regler för gruppmedlemskap](../users-groups-roles/groups-dynamic-membership.md).

   ![Välja medlemmar att lägga till](./media/active-directory-groups-create-azure-portal/select-members.png)
7. På bladet **Medlemmar** väljer du en eller flera användare eller enheter att lägga till i gruppen och väljer knappen **Välj** längst ned på bladet för att lägga till dem i gruppen. Rutan **Användare** filtrerar visningen baserat på matchningen av din inmatning till någon del av ett användar- eller enhetsnamn. Jokertecken accepteras inte i den rutan.
8. När du är klar med att lägga till medlemmar i gruppen väljer du **Skapa** på bladet **Grupp**.    

   ![Skapa gruppbekräftelse](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)


## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Se befintliga grupper](active-directory-groups-view-azure-portal.md)
* [Hantera inställningar för en grupp](active-directory-groups-settings-azure-portal.md)
* [Hantera medlemmar i en grupp](active-directory-groups-members-azure-portal.md)
* [Hantera medlemskap i en grupp](active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](../users-groups-roles/groups-dynamic-membership.md)
