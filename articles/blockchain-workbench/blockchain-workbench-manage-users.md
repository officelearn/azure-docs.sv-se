---
title: Hantera användare i Azure Blockchain Workbench
description: Så här att hantera användare i Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/26/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: ff2c6a2d9b2aec7abc684a4b189ccf31c454aaeb
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47093045"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Hantera användare i Azure Blockchain Workbench

Azure Blockchain Workbench omfattar hantering av användare för människor och organisationer som ingår i din consortium.

## <a name="prerequisites"></a>Förutsättningar

En Blockchain Workbench-distribution krävs. Se [Azure Blockchain Workbench distribution](blockchain-workbench-deploy.md) mer information om distribution.

## <a name="add-azure-ad-users"></a>Lägg till Azure AD-användare

Azure Blockchain Workbench använder Azure Active Directory (Azure AD) för autentisering, åtkomstkontroll och roller. Användare i Blockchain Workbench Azure AD-klient kan autentisera och använda Blockchain Workbench. Lägga till användare till rollen Administratör program att interagera och utföra åtgärder.

Blockchain Workbench användare måste finnas i Azure AD-klienten innan du kan tilldela dem till program och roller. Använd följande steg för att lägga till användare till Azure AD:

1.  Logga in på [Azure Portal](https://portal.azure.com).
2.  Välj ditt konto i det övre högra hörnet och växla till Azure AD-klient som är kopplad till Blockchain Workbench.
3.  Välj **Azure Active Directory > användare**. Du kan se en lista över användare i din katalog.
4.  Om du vill lägga till användare i katalogen, Välj **ny användare**. För externa användare, väljer **ny gästanvändare**.

    ![Ny användare](media/blockchain-workbench-manage-users/add-ad-user.png)

5.  Slutför fälten som krävs för den nya användaren. Välj **Skapa**.

Besök [Azure AD](../active-directory/fundamentals/add-users-azure-active-directory.md) dokumentationen för mer information om hur du hanterar användare i Azure AD.

## <a name="manage-blockchain-workbench-administrators"></a>Hantera administratörer Blockchain Workbench

När användare har lagts till katalogen, är nästa steg att välja vilka användare som är administratörer Blockchain Workbench. Användare i den **administratör** grupp är associerade med den **program administratörsroll** i Blockchain Workbench. Administratörer kan lägga till eller ta bort användare, tilldela användare till specifika scenarier och skapa nya program.

Lägga till användare till den **administratör** i Azure AD-katalog:

1.  Logga in på [Azure Portal](https://portal.azure.com).
2.  Kontrollera att du är i Azure AD-klient som är kopplad till Blockchain Workbench genom att välja ditt konto i det övre högra hörnet.
3.  Välj **Azure Active Directory > företagsprogram**.
4.  Välj Azure AD-klientprogram för Blockchain Workbench
    
    ![Alla registreringar för enterprise-program](media/blockchain-workbench-manage-users/select-blockchain-client-app.png)

5.  Välj **användare och grupper > Lägg till användare**.
6.  I **Lägg till tilldelning**väljer **användare**. Välja eller söka efter den användare som du vill lägga till som administratör. Klicka på **Välj** när du har valt.

    ![Lägg till tilldelning ](media/blockchain-workbench-manage-users/add-user-assignment.png)

9.  Kontrollera **rollen** är inställd på **administratör**
10. Välj **Tilldela**. Tillagda användare visas i listan med administratörsrollen tilldelas.

    ![Blockchain klienten app-användare](media/blockchain-workbench-manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Hantera Blockchain Workbench medlemmar

Använda Blockchain Workbench-programmet för att hantera användare och organisationer som ingår i din consortium. Du kan lägga till eller ta bort användare i program och roller.

1. [Öppna Blockchain Workbench](blockchain-workbench-deploy.md#blockchain-workbench-web-url) i din webbläsare och logga in som administratör.

    ![Blockchain Workbench](media/blockchain-workbench-manage-users/blockchain-workbench-applications.png)

    Medlemmar läggs till i varje program. Medlemmar kan ha en eller flera programroller att initiera kontrakt eller vidta åtgärder.

2. För att hantera medlemmar för ett program, Välj en panel för programmet i den **program** fönstret.

    Antalet medlemmar som är kopplad till det valda programmet visas i panelen medlemmar.

    ![Välj program](media/blockchain-workbench-manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Lägg till medlem till program

1. Välj panelen medlem för att visa en lista över de aktuella medlemmarna.
2. Välj **lägga till medlemmar**.

    ![Lägg till medlemmar](media/blockchain-workbench-manage-users/application-add-members.png)

3. Sök efter användarens namn.  Endast visas Azure AD-användare som finns i Blockchain Workbench-klient. Om användaren inte hittas, måste du [lägga till Azure AD-användare](#add-azure-ad-users).

    ![Lägg till medlemmar](media/blockchain-workbench-manage-users/find-user.png)

4. Välj en **rollen** från listrutan.

    ![Välj rollmedlemmar](media/blockchain-workbench-manage-users/application-select-role.png)

5. Välj **Lägg till** att lägga till medlemmen med tillhörande rolltjänster i programmet.

#### <a name="remove-member-from-application"></a>Ta bort medlem från programmet

1. Välj panelen medlem för att visa en lista över de aktuella medlemmarna.
2. För den användare som du vill ta bort väljer **ta bort** från rollen utskriftsjobb.

    ![Ta bort medlem](media/blockchain-workbench-manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Ändra eller lägga till roll

1. Välj panelen medlem för att visa en lista över de aktuella medlemmarna.
2. För den användare som du vill ändra, klickar du på listrutan och väljer den nya rollen.

    ![Ändra roll](media/blockchain-workbench-manage-users/application-change-role.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du hanterar användare för Azure Blockchain Workbench. Fortsätt till nästa artikel om anvisningar om du vill veta hur du skapar en blockchain-program.

> [!div class="nextstepaction"]
> [Skapa ett blockchain-program i Azure Blockchain Workbench](blockchain-workbench-create-app.md)
