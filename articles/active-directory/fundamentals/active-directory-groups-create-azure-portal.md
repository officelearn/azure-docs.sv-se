---
title: Skapa en grupp för användare i Azure Active Directory | Microsoft Docs
description: Hur du skapar en grupp i Azure Active Directory och lägga till medlemmar i gruppen
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: article
ms.date: 08/04/2017
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 0a757ff93531e95da632086c2526dd36406f94c2
ms.sourcegitcommit: 5821eef990c26fa045e4beacce39f6b02b83156b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/15/2018
ms.locfileid: "35664921"
---
# <a name="create-a-group-and-add-members-in-azure-active-directory"></a>Skapa en grupp och lägga till medlemmar i Azure Active Directory
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-groups-create-azure-portal.md)
> * [PowerShell](../active-directory-accessmanagement-groups-settings-v2-cmdlets.md)

Den här artikeln förklarar hur du skapar och fylla i en ny grupp i Azure Active Directory. Använda en grupp för att utföra hanteringsuppgifter, till exempel tilldela licenser eller behörigheter till ett antal användare eller enheter på samma gång.

## <a name="how-do-i-create-a-group"></a>Hur skapar jag en grupp?
1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **alla tjänster**, ange **användare och grupper** i textrutan och välj sedan **RETUR**.

   ![Öppna användarhantering](./media/active-directory-groups-create-azure-portal/search-user-management.png)
3. På den **användare och grupper** bladet väljer **alla grupper**.

   ![Öppna bladet grupper](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)
4. På den **användare och grupper – alla grupper** bladet väljer den **Lägg till** kommando.

   ![Att välja kommandot Lägg till](./media/active-directory-groups-create-azure-portal/add-group-command.png)
5. På den **grupp** bladet Lägg till ett namn och en beskrivning för gruppen.
6. Om du vill välja medlemmar som ska läggas till i gruppen **tilldelad** i den **medlemskapstypen** och välj sedan **medlemmar**. Mer information om hur du hanterar medlemskap i en grupp dynamiskt finns [använda attribut för att skapa avancerade regler för gruppmedlemskap](../active-directory-groups-dynamic-membership-azure-portal.md).

   ![Att välja medlemmar för att lägga till](./media/active-directory-groups-create-azure-portal/select-members.png)
7. På den **medlemmar** bladet, Välj en eller flera användare eller enheter att lägga till i gruppen och välj den **Välj** längst ned på bladet för att lägga till dem i gruppen. Den **användaren** rutan filtrerar baserat på matchning inmatningen till alla delar av namnet på en användare eller enhet. Inga jokertecken godkänns i rutan.
8. När du är klar med att lägga till medlemmar i gruppen, Välj **skapa** på den **grupp** bladet.    

   ![Skapa grupp bekräftelse](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)


## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Se befintliga grupper](active-directory-groups-view-azure-portal.md)
* [Hantera inställningar för en grupp](active-directory-groups-settings-azure-portal.md)
* [Hantera medlemmar i en grupp](active-directory-groups-members-azure-portal.md)
* [Hantera medlemskap i en grupp](active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](../active-directory-groups-dynamic-membership-azure-portal.md)
