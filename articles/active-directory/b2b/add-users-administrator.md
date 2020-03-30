---
title: Lägga till B2B-samarbetsanvändare i Azure-portalen - Azure AD
description: Visar hur en administratör kan lägga till gästanvändare i sin katalog från en partnerorganisation med Hjälp av Azure Active Directory (Azure AD) B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf2ca3bde0cfee97adee8bb875df1522e95b1f53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263509"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Lägga till Azure Active Directory B2B-samarbetsanvändare i Azure-portalen

Som en användare som har tilldelats någon av de begränsade administratörskatalogrollerna kan du använda Azure-portalen för att bjuda in B2B-samarbetsanvändare. Du kan bjuda in gästanvändare till katalogen, till en grupp eller till ett program. När du har bjudit in en användare via någon av dessa metoder läggs den inbjudna användarens konto till i Azure Active Directory (Azure AD), med en användartyp av *Gäst*. Gästanvändaren måste sedan lösa in sin inbjudan att komma åt resurser. En inbjudan från en användare upphör inte att gälla.

När du har lagt till en gästanvändare i katalogen kan du antingen skicka en direktlänk till gästanvändaren till en delad app eller gästanvändaren kan klicka på inlösen-URL:en i e-postmeddelandet för inbjudan. Mer information om inlösenprocessen finns i [B2B:s inlösen av inlösen av samarbetsinbjudan](redemption-experience.md).

> [!IMPORTANT]
> Du bör följa stegen i [How-to: Lägg till organisationens sekretessinformation i Azure Active Directory](https://aka.ms/adprivacystatement) för att lägga till url:en till organisationens sekretesspolicy. Som en del av den första inlösningsprocessen för inbjudan måste en inbjuden användare samtycka till att dina sekretessvillkor fortsätter. 

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att organisationens inställningar för externt samarbete är konfigurerade så att du får bjuda in gäster. Som standard kan alla användare och administratörer bjuda in gäster. Men organisationens externa samarbetsprinciper kan vara konfigurerade för att förhindra att vissa typer av användare eller administratörer bjuder in gäster. Information om hur du visar och anger dessa principer finns i [Aktivera B2B externt samarbete och hantera vem som kan bjuda in gäster](delegate-invitations.md).

## <a name="add-guest-users-to-the-directory"></a>Lägga till gästanvändare i katalogen

Så här lägger du till B2B-samarbetsanvändare i katalogen:

1. Logga in på [Azure-portalen](https://portal.azure.com) som en användare som har tilldelats en begränsad administratörskatalogroll eller rollen Gäst inbjudna.
2. Sök efter och välj **Azure Active Directory** från valfri sida.
3. Under **Hantera** väljer du **Användare**.
4. Välj **Ny gästanvändare**.

   ![Visar var Ny gästanvändare finns i användargränssnittet](./media/add-users-administrator/new-guest-user-in-all-users.png) 
 
   > [!NOTE]
   > Alternativet **Ny gästanvändare** är också tillgängligt på sidan **Organisationsrelationer.** Välj **Organisationsrelationer**under **Hantera**i **Azure Active Directory**.

5. På sidan **Ny användare** väljer du Bjud **in användare** och lägger sedan till gästanvändarens information. 

    > [!NOTE]
    > Grupp-e-postadresser stöds inte. ange e-postadressen för en person. Vissa e-postleverantörer tillåter också användare att lägga till en plussymbol (+) och ytterligare text till sina e-postadresser för att hjälpa till med saker som filtrering i inkorgen. Azure AD stöder dock för närvarande inte plus symboler i e-postadresser. Undvik leveransproblem genom att utelämna plussymbolen och eventuella tecken som följer den upp till @-symbolen.

   - **Namn.** Gästanvändarens för- och efternamn.
   - **E-postadress (obligatoriskt)**. Gästanvändarens e-postadress.
   - **Personligt meddelande (valfritt)** Inkludera ett personligt välkomstmeddelande till gästanvändaren.
   - **Grupper**: Du kan lägga till gästanvändaren i en eller flera befintliga grupper eller göra det senare.
   - **Katalogroll**: Om du kräver administrativa Azure AD-behörigheter för användaren kan du lägga till dem i en Azure AD-roll. 

7. Välj **Bjud in** för att skicka inbjudan till gästanvändaren automatiskt. 
 
När du har skickat inbjudan läggs användarkontot automatiskt till i katalogen som gäst.


![Visar B2B-användare med gästanvändartyp](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Lägga till gästanvändare i en grupp
Om du behöver lägga till B2B-samarbetsanvändare manuellt i en grupp gör du så här:

1. Logga in till [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. Sök efter och välj **Azure Active Directory** från valfri sida.
3. Välj **Grupper**under **Hantera**.
4. Markera en grupp (eller klicka på **Ny grupp** om du vill skapa en ny). Det är en bra idé att i gruppbeskrivningen inkludera att gruppen innehåller B2B-gästanvändare.
5. Välj **medlemmar**. 
6. Gör något av följande:
   - Om gästanvändaren redan finns i katalogen söker du efter B2B-användaren. Markera användaren och klicka sedan på **Markera** för att lägga till användaren i gruppen.
   - Om gästanvändaren inte redan finns i katalogen bjuder du in dem till gruppen genom att skriva in deras e-postadress i sökrutan, skriva ett valfritt personligt meddelande och sedan klicka på **Välj**. Inbjudan går automatiskt ut till den inbjudna användaren.
     
     ![Knappen Lägg till inbjudan för att lägga till gästmedlemmar](./media/add-users-administrator/GroupInvite.png)
   
Du kan också använda dynamiska grupper med Azure AD B2B-samarbete. Mer information finns i [Dynamiska grupper och Azure Active Directory B2B-samarbete](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Lägga till gästanvändare i ett program

Så här lägger du till B2B-samarbetsanvändare i ett program:

1. Logga in till [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. Sök efter och välj **Azure Active Directory** från valfri sida.
3. Under **Hantera**väljer du **Företagsprogram** > **Alla program**.
4. Välj det program som du vill lägga till gästanvändare i.
5. På programmets instrumentpanel väljer du **Totalt antal användare** för att öppna fönstret Användare och **grupper.**

    ![Knappen Totalt antal användare för att lägga till öppna användare och grupper](./media/add-users-administrator/AppUsersAndGroups.png)

6. Välj **Lägg till användare**.
7. Under **Lägg till tilldelning**väljer du Användare och **grupper**.
8. Gör något av följande:
   - Om gästanvändaren redan finns i katalogen söker du efter B2B-användaren. Markera användaren, klicka på **Markera**och klicka sedan på **Tilldela** om du vill lägga till användaren i appen.
   - Om gästanvändaren inte redan finns i katalogen skriver du användarens e-postadress under **Välj medlem eller inbjudan till en extern användare.** Skriv ett personligt meddelande i meddelanderutan. Klicka på Bjud **in**under meddelanderutan .
           
       ![Knappen Lägg till inbjudan för att lägga till gästmedlemmar](./media/add-users-administrator/AppInviteUsers.png)
   
      Klicka på **Markera**och sedan på **Tilldela** om du vill lägga till användaren i appen. En inbjudan går automatiskt ut till den inbjudna användaren.

9. Gästanvändaren visas i programmets **lista Användare och grupper** med den tilldelade rollen **standardåtkomst**. Om du vill ändra rollen gör du följande:
   - Markera gästanvändaren och välj sedan **Redigera**. 
   - Klicka på **Välj roll**under **Redigera tilldelning**och välj den roll som du vill tilldela den markerade användaren.
   - Klicka på **Markera**.
   - Klicka på **Tilldela**.
 
## <a name="resend-invitations-to-guest-users"></a>Skicka om inbjudningar till gästanvändare

Om en gästanvändare ännu inte har löst in sin inbjudan kan du skicka in e-postmeddelandet igen.

1. Logga in till [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. Sök efter och välj **Azure Active Directory** från valfri sida.
3. Under **Hantera** väljer du **Användare**.
5. Välj användarkonto.
6. Välj **Profil**under **Hantera**.
7. Om användaren ännu inte har accepterat inbjudan är ett **alternativ för skicka inbjudan** tillgänglig igen. Välj den här knappen om du vill skicka igen.

   ![Alternativet Skicka inbjudan igen i användarprofilen](./media/add-users-administrator/b2b-user-resend-invitation.png)

> [!NOTE]
> Om du skickar en inbjudan som ursprungligen riktade användaren till en viss app, bör du förstå att länken i den nya inbjudan tar användaren till den översta åtkomstpanelen i stället.

## <a name="next-steps"></a>Nästa steg

- Mer information om hur icke-Azure AD-administratörer kan lägga till B2B-gästanvändare finns i [Hur lägger informationsarbetare till B2B-samarbetsanvändare?](add-users-information-worker.md)
- Information om e-postmeddelandet med inbjudan finns i [Elementen i e-postmeddelandet med B2B-samarbetsanbjudan](invitation-email-elements.md).

