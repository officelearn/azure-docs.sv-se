---
title: Lägg till B2B-samarbete användare i Azure portal – Azure Active Directory | Microsoft Docs
description: Visar hur en administratör kan lägga till gästanvändare sina kataloger från en organisation med hjälp av Azure Active Directory (AD Azure) B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 9d0565468d953c83ca5fee864b3079fbfee9bbf1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Lägg till Azure Active Directory B2B-samarbete användare i Azure-portalen

Du kan använda Azure-portalen att bjuda in användare för B2B-samarbete som global administratör eller en användare som har tilldelats någon av rollerna begränsad administratör directory. Du kan be gästanvändare i katalogen, en grupp eller ett program. När du bjuda in användare via någon av dessa metoder inbjudna användarkontot har lagts till Azure Active Directory (Azure AD), med en användare *gäst*. Gästanvändaren måste sedan lösa sina inbjudan för att komma åt resurser.

När du lägger till en gästanvändare till katalogen kan du antingen skicka gästanvändaren en direktlänk till en delad app eller gästanvändaren kan klicka på inlösning Webbadressen i e-postinbjudan. Mer information om hur du inlösning finns [B2B-samarbete inbjudan inlösning](active-directory-b2b-redemption-experience.md).

> [!IMPORTANT]
> Du bör följa stegen i [anvisningar: lägga till din organisations sekretess information i Azure Active Directory](https://aka.ms/adprivacystatement) att lägga till URL-Adressen för din organisations sekretesspolicy. Som en del av den första gången inbjudan inlösning, måste en inbjudna användare godkänna Licensvillkor för din integritet fortsätta. 

## <a name="add-guest-users-to-the-directory"></a>Lägga till gästanvändare i katalogen

Följ dessa steg om du vill lägga till B2B-samarbete användare i katalogen:

1. Logga in på den [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. I navigeringsfönstret väljer **Azure Active Directory**.
3. Under **hantera**väljer **användare och grupper** > **alla användare**.
4. Välj **nya gästanvändare**.

   ![Visar där nya gästanvändaren i Användargränssnittet](./media/active-directory-b2b-admin-add-users/NewGuestUser-Directory.png) 
 
7. Under **bjuda in gäst**, ange den externa användaren e-postadress. Du kan också innehålla ett välkomstmeddelande. Exempel:

   ![Visar där nya gästanvändaren i Användargränssnittet](./media/active-directory-b2b-admin-add-users/InviteGuest.png) 

8. Välj **bjuda in** att automatiskt skicka inbjudan till gästanvändaren. I den **meddelande** område, leta efter en **har inbjudna användare** meddelande. 
 
När du har skickat inbjudan läggs användarkontot automatiskt till katalogen som gäst.


![Visar B2B-användare med gästen användartyp](./media/active-directory-b2b-admin-add-users/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Lägg till gästanvändare i en grupp
Följ dessa steg om du behöver lägga till B2B-samarbete användare manuellt till en grupp som Azure AD-administratör:

1. Logga in på den [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. I navigeringsfönstret väljer **Azure Active Directory**.
3. Under **hantera**väljer **användare och grupper** > **alla grupper**.
4. Välj en grupp (eller klicka på **ny grupp** att skapa en ny). Det är en bra idé att inkludera i beskrivningen av gruppen att gruppen innehåller B2B gästanvändare.
5. Välj **medlemmar** > **lägga till medlemmar**. 
6. Gör något av följande:
   - Om gästanvändaren finns redan i katalogen, söka efter B2B-användare. Välj användaren > klickar du på **Välj** att lägga till användaren i gruppen.
   - Välj om gästanvändaren inte redan finns i katalogen **bjuda in**.
   ![Lägg till inbjudan för att lägga till gäst-medlemmar](./media/active-directory-b2b-admin-add-users/GroupInvite.png)
   
      Under **bjuda in gäst**, ange den e-postadressen och ett valfritt personligt meddelande > Välj **bjuda in**. Klicka på **Välj** att lägga till användaren i gruppen.

      Inbjudan överförs automatiskt till inbjudna användare. I den **meddelande** område, leta efter en lyckad **Invited användaren** meddelande. 

Du kan också använda dynamiska grupper med Azure AD B2B-samarbete. Mer information finns i [dynamiska grupper och Azure Active Directory B2B-samarbete](active-directory-b2b-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Lägg till gästanvändare i ett program

Så här lägger du till B2B-samarbete användare till ett program som administratör för Azure AD:

1. Logga in på den [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. I navigeringsfönstret väljer **Azure Active Directory**.
3. Under **hantera**väljer **företagsprogram** > **alla program**.
4. Välj det program som du vill lägga till gästanvändare.
5. Under **hantera**väljer **användare och grupper**.
6. Välj **Lägg till användare**.
7. Under **Lägg uppdrag**väljer **användare och grupper**.
8. Gör något av följande:
   - Om gästanvändaren finns redan i katalogen, söka efter B2B-användare. Välj användaren och klicka sedan på **Välj** att lägga till användaren i appen.
   - Välj om gästanvändaren inte redan finns i katalogen **bjuda in**.
   ![Lägg till inbjudan för att lägga till gäst-medlemmar](./media/active-directory-b2b-admin-add-users/AppInviteUsers.png)
   
      Under **bjuda in gäst**, ange den e-postadressen och ett valfritt personligt meddelande > Välj **bjuda in**. Klicka på **Välj** att lägga till användaren i appen.

      Inbjudan överförs automatiskt till inbjudna användare. I den **meddelande** område, leta efter en lyckad **Invited användaren** meddelande.

9. Under **Lägg uppdrag**, klickar du på **Välj roll** > Välj en roll ska gälla för den valda användaren (om tillämpligt) > Välj **OK**.
10. Klicka på **Tilldela**.
 
## <a name="resend-invitations-to-guest-users"></a>Skicka inbjudningar för gästanvändare

Om en gästanvändare inte har ännu Inlöst sina inbjudan, kan du skicka e-postinbjudan.

1. Logga in på den [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. I navigeringsfönstret väljer **Azure Active Directory**.
3. Under **hantera**väljer **användare och grupper**.
4. Välj **alla användare**.
5. Välj användarkontot.
6. Under **hantera**väljer **profil**.
7. Om användaren inte har accepterat inbjudan ännu en **skicka inbjudan** alternativet är tillgängligt. Välj den här knappen Skicka på nytt.

   ![Skicka inbjudan alternativet i användarprofilen](./media/active-directory-b2b-admin-add-users/Resend-Invitation.png)

> [!NOTE]
> Om du skicka en inbjudan som ursprungligen dirigeras användaren till en viss app måste du förstå att länken i den nya inbjudan tar användaren till panelen på översta nivån i stället.

## <a name="next-steps"></a>Nästa steg

- Information om hur icke-Azure AD-administratörer kan lägga till B2B gästanvändare finns [hur till B2B-samarbete användare av informationsarbetare?](active-directory-b2b-iw-add-users.md)
- Information om e-postinbjudan finns [elementen i e-postinbjudan B2B-samarbete](active-directory-b2b-invitation-email.md).

