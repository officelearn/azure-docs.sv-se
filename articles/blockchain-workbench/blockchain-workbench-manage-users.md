---
title: Hantera användare i Azure Blockchain arbetsstationen
description: Hur du hanterar användare i Azure Blockchain arbetsstationen.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/26/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 80f20e438b1d923e688aeef9e6b353642bd74c27
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158943"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Hantera användare i Azure Blockchain arbetsstationen

Azure Blockchain arbetsstationen innehåller användarhantering för personer och organisationer som ingår i din consortium.

## <a name="prerequisites"></a>Förutsättningar

En distribution av Blockchain Workbench krävs. Se [Azure Blockchain arbetsstationen distribution](blockchain-workbench-deploy.md) mer information om distribution.

## <a name="add-azure-ad-users"></a>Lägg till Azure AD-användare

Azure Blockchain arbetsstationen använder Azure Active Directory (Azure AD) för autentisering och åtkomstkontroll roller. Användare i Blockchain arbetsstationen Azure AD-klienten kan autentisera och använda Blockchain arbetsstationen. Lägga till användare i rollen Administratör program att interagera och utföra åtgärder.

Blockchain arbetsstationen användare måste finnas i Azure AD-klienten innan du kan tilldela dem till program och roller. Använd följande steg om du vill lägga till användare i Azure AD:

1.  Logga in på [Azure Portal](https://portal.azure.com).
2.  Välj ditt konto i det övre högra hörnet och växla till Azure AD-klient som är associerade med Blockchain arbetsstationen.
3.  Välj **Azure Active Directory > användare**. Du kan se en lista med användare i din katalog.
4.  Om du vill lägga till användare i katalogen, Välj **ny användare**. För externa användare väljer **nya gästanvändare**.

    ![Ny användare](media/blockchain-workbench-manage-users/add-ad-user.png)

5.  Fyll i obligatoriska fält för den nya användaren. Välj **Skapa**.

Besök [Azure AD](../active-directory/add-users-azure-active-directory.md) dokumentationen för mer information om hur du hanterar användare i Azure AD.

## <a name="manage-blockchain-workbench-administrators"></a>Hantera Blockchain arbetsstationen administratörer

När en användare har lagts till katalogen, är nästa steg att välja vilka användare som är administratörer Blockchain arbetsstationen. Användare i den **administratör** grupp är associerade med den **programmet administratörsroll** i Blockchain arbetsstationen. Administratörer kan lägga till eller ta bort användare, tilldela användare till specifika scenarier och skapa nya program.

Att lägga till användare till den **administratör** i Azure AD-katalog:

1.  Logga in på [Azure Portal](https://portal.azure.com).
2.  Kontrollera att du är i Azure AD-klient som är associerade med Blockchain arbetsstationen genom att välja kontot i det övre högra hörnet.
3.  Välj **Azure Active Directory > företagsprogram**.
4.  Välj program för Azure AD-klient för Blockchain arbetsstationen
    
    ![Alla registreringar för enterprise-programmet](media/blockchain-workbench-manage-users/select-blockchain-client-app.png)

5.  Välj **användare och grupper > Lägg till användare**.
6.  I **Lägg uppdrag**väljer **användare**. Välj eller Sök efter den användare som du vill lägga till som administratör. Klicka på **Välj** när du är klar att välja.

    ![Lägg till tilldelning ](media/blockchain-workbench-manage-users/add-user-assignment.png)

9.  Kontrollera **rollen** är inställd på **administratör**
10. Välj **Tilldela**. Tillagda användare visas i listan med en administratörsroll som tilldelats.

    ![Blockchain klienten app-användare](media/blockchain-workbench-manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Hantera Blockchain arbetsstationen medlemmar

Använda i Blockchain Workbench för att hantera användare och organisationer som ingår i din consortium. Du kan lägga till eller ta bort användare i program och roller.

1. [Öppna Blockchain arbetsstationen](blockchain-workbench-deploy.md#blockchain-workbench-web-url) i webbläsaren och logga in som administratör.

    ![Blockchain Workbench](media/blockchain-workbench-manage-users/blockchain-workbench-applications.png)

    Medlemmar läggs till varje program. Medlemmar kan ha en eller flera programroller att inleda kontrakt eller vidta åtgärder.

2. Om du vill hantera medlemskap för ett program, väljer du en program-panelen i den **program** fönstret.

    Antalet medlemmar som hör till det valda programmet visas i panelen medlemmar.

    ![Välj program](media/blockchain-workbench-manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Lägg till medlem i programmet

1. Välj medlem panelen visas en lista över de aktuella medlemmarna.
2. Välj **lägga till medlemmar**.

    ![Lägg till medlemmar](media/blockchain-workbench-manage-users/application-add-members.png)

3. Sök efter användarens namn.  Endast visas Azure AD-användare som finns i Blockchain arbetsstationen klienten. Om användaren inte hittas, behöver du [lägga till Azure AD-användare](#add-azure-ad-users).

    ![Lägg till medlemmar](media/blockchain-workbench-manage-users/find-user.png)

4. Välj en **rollen** från listrutan.

    ![Välj medlemmar i rollen](media/blockchain-workbench-manage-users/application-select-role.png)

5. Välj **Lägg till** att lägga till medlem med rollen kopplade till programmet.

#### <a name="remove-member-from-application"></a>Ta bort medlemmen från programmet

1. Välj medlem panelen visas en lista över de aktuella medlemmarna.
2. Användaren som du vill ta bort, Välj **ta bort** från rollen listrutan.

    ![Ta bort medlem](media/blockchain-workbench-manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Ändra eller lägga till rollen

1. Välj medlem panelen visas en lista över de aktuella medlemmarna.
2. Klicka på listrutan och välj den nya rollen för den användare som du vill ändra.

    ![Ändra roll](media/blockchain-workbench-manage-users/application-change-role.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du hanterar användare för Azure Blockchain arbetsstationen. Information om hur du skapar ett program för blockchain fortsätta i nästa artikel.

> [!div class="nextstepaction"]
> [Skapa en blockchain program i Azure Blockchain arbetsstationen](blockchain-workbench-create-app.md)
