---
title: Hantera användare i Azure Blockchain Workbench
description: Så här hanterar du användare i Azure Blockchain Workbench.
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 573ec477a3e75beb91f90da0545fb7d4c0f9bf39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252186"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Hantera användare i Azure Blockchain Workbench

Azure Blockchain Workbench innehåller användarhantering för personer och organisationer som ingår i ditt konsortium.

## <a name="prerequisites"></a>Krav

En Blockchain Workbench-distribution krävs. Mer information om distribution finns [i Azure Blockchain Workbench-distributionen.](deploy.md)

## <a name="add-azure-ad-users"></a>Lägga till Azure AD-användare

Azure Blockchain Workbench använder Azure Active Directory (Azure AD) för autentisering, åtkomstkontroll och roller. Användare i Blockchain Workbench Azure AD-klienten kan autentisera och använda Blockchain Workbench. Lägg till användare i administratörsprogrammets roll för att interagera och utföra åtgärder.

Blockchain Workbench-användare måste finnas i Azure AD-klienten innan du kan tilldela dem till program och roller. Så här lägger du till användare i Azure AD:

1.  Logga in på [Azure-portalen](https://portal.azure.com).
2.  Välj ditt konto i det övre högra hörnet och växla till Azure AD-klienten som är kopplad till Blockchain Workbench.
3.  Välj **Azure Active Directory-> användare**. Du ser en lista över användare i katalogen.
4.  Om du vill lägga till användare i katalogen väljer du **Ny användare**. För externa användare väljer du **Ny gästanvändare**.

    ![Ny användare](./media/manage-users/add-ad-user.png)

5.  Fyll i de obligatoriska fälten för den nya användaren. Välj **Skapa**.

Mer information om hur du hanterar användare i Azure AD finns i Azure AD-dokumentationen. [Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md)

## <a name="manage-blockchain-workbench-administrators"></a>Hantera Blockchain Workbench-administratörer

När användare har lagts till i katalogen är nästa steg att välja vilka användare som är Blockchain Workbench-administratörer. Användare i **gruppen Administratör** associeras med **administratörsprogrammets roll** i Blockchain Workbench. Administratörer kan lägga till eller ta bort användare, tilldela användare till specifika scenarier och skapa nya program.

Så här lägger du till användare i **administratörsgruppen** i Azure AD-katalogen:

1.  Logga in på [Azure-portalen](https://portal.azure.com).
2.  Verifiera att du finns i Azure AD-klienten som är kopplad till Blockchain Workbench genom att välja ditt konto i det övre högra hörnet.
3.  Välj **Azure Active Directory > Enterprise-program**.
4.  Välj Azure AD-klientprogrammet för Blockchain Workbench
    
    ![Alla registreringar av företagsansökan](./media/manage-users/select-blockchain-client-app.png)

5.  Välj **Användare och grupper > Lägg till användare**.
6.  I **Lägg till tilldelning** väljer du **Användare**. Välj eller sök efter den användare som du vill lägga till som administratör. Klicka på **Välj** när du är klar med valet.

    ![Lägg till tilldelning](./media/manage-users/add-user-assignment.png)

9.  Verifiera **roll** är inställd **på Administratör**
10. Välj **Tilldela**. De tillagda användarna visas i listan med administratörsrollen tilldelad.

    ![Användare av blockchain-klientappar](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Hantera Blockchain Workbench-medlemmar

Använd Blockchain Workbench-programmet för att hantera användare och organisationer som ingår i ditt konsortium. Du kan lägga till eller ta bort användare i program och roller.

1. [Öppna Blockchain Workbench](deploy.md#blockchain-workbench-web-url) i din webbläsare och logga in som administratör.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    Medlemmar läggs till i varje program. Medlemmar kan ha en eller flera programroller för att initiera kontrakt eller vidta åtgärder.

2. Om du vill hantera medlemmar för ett program väljer du en programpanel i **programfönstret.**

    Antalet medlemmar som är associerade till det valda programmet återspeglas i medlemspanelen.

    ![Välj program](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Lägg till medlem i programmet

1. Välj medlemspanelen om du vill visa en lista över de aktuella medlemmarna.
2. Välj **Lägg till medlemmar**.

    ![Lägg till medlemmar](./media/manage-users/application-add-members.png)

3. Sök efter användarens namn.  Endast Azure AD-användare som finns i Blockchain Workbench-klientorganisationen visas. Om användaren inte hittas måste du [lägga till Azure AD-användare](#add-azure-ad-users).

    ![Lägg till medlemmar](./media/manage-users/find-user.png)

4. Välj en **roll** i listrutan.

    ![Välj rollmedlemmar](./media/manage-users/application-select-role.png)

5. Välj **Lägg till** för att lägga till medlemmen med den tillhörande rollen i programmet.

#### <a name="remove-member-from-application"></a>Ta bort medlem från programmet

1. Välj medlemspanelen om du vill visa en lista över de aktuella medlemmarna.
2. För den användare som du vill ta bort väljer du **Ta bort** från listrutan roll.

    ![Ta bort medlem](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Ändra eller lägga till roll

1. Välj medlemspanelen om du vill visa en lista över de aktuella medlemmarna.
2. För den användare som du vill ändra klickar du på listrutan och väljer den nya rollen.

    ![Ändra roll](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du hanterar användare för Azure Blockchain Workbench. Om du vill veta hur du skapar ett blockchain-program fortsätter du till nästa how-to-artikel.

> [!div class="nextstepaction"]
> [Skapa ett blockkedjeprogram i Azure Blockchain Workbench](create-app.md)
