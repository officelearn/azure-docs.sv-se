---
title: Lägg till användare i B2B-samarbetet i Azure portal – Azure Active Directory | Microsoft Docs
description: Visar hur en administratör kan lägga till gästanvändare till sin katalog från en partnerorganisation som använder Azure Active Directory (Azure AD) B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 07/10/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: f654aaa6d44011a089008558849d37bf6cdfa6f6
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39037265"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Lägg till användare i Azure Active Directory B2B-samarbetet i Azure portal

Du kan använda Azure-portalen som global administratör eller en användare som har tilldelats någon av begränsad administratör-katalogroller, för att bjuda in användare i B2B-samarbetet. Du kan bjuda in gästanvändare till katalogen, en grupp eller ett program. När du bjuder in en användare via någon av dessa metoder inbjudna användarens konto läggs till i Azure Active Directory (Azure AD) med en användartyp med *gäst*. Gästanvändaren måste sedan lösa in sin inbjudan att komma åt resurser.

När du lägger till en gästanvändare till katalogen du kan antingen skicka en direktlänk för gästanvändare till en delad app eller gästanvändaren kan klicka på URL-Adressen för inlösen i e-postinbjudan. Läs mer om processen för inlösen [inlösning av inbjudan för B2B-samarbete](redemption-experience.md).

> [!IMPORTANT]
> Du bör följa stegen i [instruktioner: Lägg till din organisations sekretess information i Azure Active Directory](https://aka.ms/adprivacystatement) att lägga till URL: en för din organisations sekretesspolicy. Som en del av den första gången inbjudan inlösen en inbjuden användare måste ge sitt godkännande till din sekretessvillkor för att fortsätta. 

## <a name="add-guest-users-to-the-directory"></a>Lägga till gästanvändare i katalogen

Följ dessa steg om du vill lägga till användare i B2B-samarbetet i katalogen:

1. Logga in på den [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. I navigeringsfönstret väljer **Azure Active Directory**.
3. Under **hantera**väljer **användare**.
4. Välj **ny gästanvändare**.

   ![Visar där ny gästanvändare är i Användargränssnittet](./media/add-users-administrator/NewGuestUser-Directory.png) 
 
5. Under **användarnamn**, ange den externa användaren e-postadress. Du kan också inkludera ett välkomstmeddelande. Exempel:

   ![Visar där ny gästanvändare är i Användargränssnittet](./media/add-users-administrator/InviteGuest.png) 

6. Välj **bjuda in** att skicka inbjudan till gästanvändaren automatiskt. 
 
När du skickar inbjudan, läggs användarkontot automatiskt till katalogen som gäst.


![Visar B2B-användare med gästen användartyp](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Lägga till gästanvändare till en grupp
Följ dessa steg om du vill lägga till användare i B2B-samarbetet manuellt till en grupp som en Azure AD-administratör:

1. Logga in på den [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. I navigeringsfönstret väljer **Azure Active Directory**.
3. Under **hantera**väljer **grupper**.
4. Välj en grupp (eller klicka på **ny grupp** att skapa en ny). Det är en bra idé att inkludera i gruppen att gruppen innehåller B2B-gästanvändare.
5. Välj **medlemmar**. 
6. Gör något av följande:
   - Om gästanvändaren finns redan i katalogen, söka efter B2B-användaren. Välj användaren och klicka sedan på **Välj** att lägga till användaren i gruppen.
   - Om gästanvändaren inte redan finns i katalogen, bjuda in dem i gruppen genom att skriva in sin e-postadress i sökrutan, skriva ett valfritt personligt meddelande och sedan klicka på **Välj**. Inbjudan skickas automatiskt ut till inbjuden användare.
     
     ![Lägg till inbjudan för att lägga till gästmedlemmar](./media/add-users-administrator/GroupInvite.png)
   
Du kan också använda dynamiska grupper med Azure AD B2B-samarbete. Mer information finns i [dynamiska grupper och Azure Active Directory B2B-samarbete](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Lägga till gästanvändare till ett program

Följ dessa steg om du vill lägga till användare i B2B-samarbetet i ett program som en Azure AD-administratör:

1. Logga in på den [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. I navigeringsfönstret väljer **Azure Active Directory**.
3. Under **hantera**väljer **företagsprogram** > **alla program**.
4. Välj det program som du vill lägga till gästanvändare.
5. Programmets instrumentpanelen, klicka **Totalt antal användare** att öppna den **användare och grupper** fönstret.

    ![Totalt antal användare för att lägga till open användare och grupper](./media/add-users-administrator/AppUsersAndGroups.png)

6. Välj **Lägg till användare**.
7. Under **Lägg till tilldelning**väljer **användare och grupper**.
8. Gör något av följande:
   - Om gästanvändaren finns redan i katalogen, söka efter B2B-användaren. Välj användaren, klicka på **Välj**, och klicka sedan på **tilldela** att lägga till användaren i appen.
   - Om gästanvändaren inte redan finns i katalogen, väljer **bjuda in**.
           
       ![Lägg till inbjudan för att lägga till gästmedlemmar](./media/add-users-administrator/AppInviteUsers.png)
   
      Under **Bjud in gäst**, ange den e-postadressen, skriver du ett valfritt personligt meddelande och välj sedan **bjuda in**. Klicka på **Välj**, och klicka sedan på **tilldela** att lägga till användaren i appen. En inbjudan skickas automatiskt ut till inbjuden användare.

9. Gästanvändaren visas i programmets **användare och grupper** listan med tilldelade rollen **standard åtkomst**. Om du vill ändra rollen gör du följande:
   - Välj gästanvändaren och välj sedan **redigera**. 
   - Under **redigera tilldelningen**, klickar du på **Välj roll**, och välj den roll som du vill tilldela till den valda användaren.
   - Klicka på **Välj**.
   - Klicka på **Tilldela**.
 
## <a name="resend-invitations-to-guest-users"></a>Skicka inbjudningar till gästanvändare

Om en gästanvändare inte har ännu in sin inbjudan, kan du skicka e-postinbjudan.

1. Logga in på den [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. I navigeringsfönstret väljer **Azure Active Directory**.
3. Under **hantera**väljer **användare**.
5. Välj användarkontot.
6. Under **hantera**väljer **profil**.
7. Om användaren inte har accepterat inbjudan, en **skicka om inbjudan** alternativet är tillgängligt. Välj den här knappen Skicka om.

   ![Skicka om inbjudan alternativet i användarprofilen](./media/add-users-administrator/Resend-Invitation.png)

> [!NOTE]
> Om du skicka en inbjudan som ursprungligen dirigeras användaren till en viss app införstådd med att länken i den nya inbjudan tar användaren till panelen på översta nivån i stället.

## <a name="next-steps"></a>Nästa steg

- Läs hur icke-Azure AD-administratörer kan lägga till B2B-gästanvändare i [hur informationsarbetare lägger till användare i B2B-samarbetet?](add-users-information-worker.md)
- Läs om hur e-postinbjudan [elementen i e-postinbjudan B2B-samarbete](invitation-email-elements.md).

