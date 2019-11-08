---
title: Lägg till eller ta bort användare – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du lägger till nya användare eller tar bort befintliga användare med hjälp av Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e98b09d8b07c625613e3be149e64ac8f06adc089
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73805552"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Lägga till eller ta bort användare med hjälp av Azure Active Directory
Lägg till nya användare eller ta bort befintliga användare från din Azure Active Directory (Azure AD)-organisation. Om du vill lägga till eller ta bort användare måste du vara en användar administratör eller global administratör. 

## <a name="add-a-new-user"></a>Lägg till en ny användare
Du kan skapa en ny användare med hjälp av Azure Active Directory portalen.

### <a name="to-add-a-new-user"></a>Lägga till en ny användare
1. Logga in på [Azure Portal](https://portal.azure.com/) som användar administratör för organisationen.

2. Välj **Azure Active Directory**, Välj **användare**och välj sedan **ny användare**.

    ![Användare – sidan alla användare med ny användare markerad](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. På sidan **Användare** fyller du i den information som krävs.

    ![Lägg till ny användare, sidan användare med användar information](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **Namn (obligatoriskt).** Det första och sista namnet på den nya användaren. Till exempel Mary parker.

   - **Användar namn (obligatoriskt).** Den nya användarens användar namn. Till exempel mary@contoso.com.
    
       Domän delen av användar namnet måste antingen använda det initiala standard domän namnet, <_yourdomainname_>. onmicrosoft. com eller ett anpassat domän namn, till exempel contoso.com. Mer information om hur du skapar ett anpassat domän namn finns i [så här lägger du till ett anpassat domän namn i Azure Active Directory](add-custom-domain.md).

   - **Upphandlarprofil.** Du kan också lägga till mer information om användaren. Du kan också lägga till användar information vid ett senare tillfälle. Mer information om hur du lägger till användar information finns i [så här lägger du till eller ändrar information om användar profiler](active-directory-users-profile-azure-portal.md).

   - **Användargrupp.** Du kan också lägga till användaren i en eller flera befintliga grupper. Du kan också lägga till användaren till grupper vid ett senare tillfälle. Mer information om hur du lägger till användare i grupper finns i [så här skapar du en grundläggande grupp och lägger till medlemmar](active-directory-groups-create-azure-portal.md).

   - **Katalog roll.** Du kan också lägga till användaren i en administratörs roll för Azure AD. Du kan tilldela användaren till en global administratör eller en eller flera av de begränsade administratörs rollerna i Azure AD. Mer information om hur du tilldelar roller finns i [tilldela roller till användare](active-directory-users-assign-role-azure-portal.md).

4. Kopiera det automatiskt genererade lösen ordet som anges i rutan **lösen ord** . Du måste ange det här lösen ordet för användaren för den inledande inloggnings processen.

5. Välj **Skapa**.

    Användaren skapas och läggs till i Azure AD-klienten.

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Lägg till en ny användare i en hybrid miljö
Om du har en miljö med både Azure Active Directory (moln) och Windows Server Active Directory (lokalt) kan du lägga till nya användare genom att synkronisera befintliga användar konto data. Mer information om hybrid miljöer och användare finns i [integrera dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Ta bort en användare
Du kan ta bort en befintlig användare med hjälp av Azure Active Directory Portal.

### <a name="to-delete-a-user"></a>Ta bort en användare
1. Logga in på [Azure Portal](https://portal.azure.com/) med ett användar administratörs konto för organisationen.

2. Välj **Azure Active Directory**, Välj **användare**och Sök sedan efter och välj den användare som du vill ta bort från Azure AD-klienten. Till exempel _Mary parker_.

3. Välj **ta bort användare**.

    ![Användare – sidan alla användare med ta bort användare markerad](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    Användaren tas bort och visas inte längre på sidan **användare – alla användare** . Användaren kan visas på sidan **borttagna användare** under de närmaste 30 dagarna och kan återställas under den tiden. Mer information om hur du återställer en användare finns i [så här återställer eller tar du bort en nyligen borttagen användare permanent](active-directory-users-restore.md). När en användare tas bort görs alla licenser som används av användaren tillgängliga för andra användare som ska förbrukas.

    >[!Note]
    >Du måste använda Windows Server Active Directory för att uppdatera identitet, kontakt information eller jobb information för användare vars auktoritets källa är Windows Server Active Directory. När du har slutfört uppdateringen måste du vänta tills nästa synkronisering har slutförts innan du kan se ändringarna.

## <a name="next-steps"></a>Nästa steg

När du har lagt till dina användare kan du utföra följande grundläggande processer:

- [Lägga till eller ändra profil information](active-directory-users-profile-azure-portal.md)

- [Tilldela roller till användare](active-directory-users-assign-role-azure-portal.md)

- [Skapa en grundläggande grupp och Lägg till medlemmar](active-directory-groups-create-azure-portal.md)

- [Arbeta med dynamiska grupper och användare](../users-groups-roles/groups-create-rule.md)

Du kan också utföra andra användar hanterings uppgifter, till exempel [lägga till gäst användare från en annan katalog](../b2b/what-is-b2b.md) eller [återställa en borttagen användare](active-directory-users-restore.md). Mer information om andra tillgängliga åtgärder finns i [Azure Active Directory User Management-dokumentation](../users-groups-roles/index.yml).
