---
title: Administrera en Azure IoT centralt program | Microsoft Docs
description: Som en administratör så att administrera dina Azure IoT centralt program
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0bdd798f9bc8fdaef54abd721a986c607c6323a5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628563"
---
# <a name="how-to-administer-your-application"></a>Så här administrerar du ditt program

När du skapar en Microsoft Azure IoT Central program, kan du använda den **Administration** avsnitt i Azure IoT centrala användargränssnittet för att administrera den. Navigera till den **Administration** väljer **Administration** på den vänstra navigeringsmenyn.

Den **Administration** avsnittet kan du:

- Hantera användare

- Hantera roller

- Visa faktureringsinformation

- Hantera tillämpningsinställningar

- Utöka en kostnadsfri utvärderingsversion

I den **Administration** avsnittet, finns det en sekundär navigeringsmenyn med länkar till olika administrationsuppgifter.

Du använder den **Administration** avsnittet måste du vara i den **administratör** roll i Azure IoT centralt program. Om du skapar ett Azure IoT centrala program du tilldelas automatiskt den **administratör** roll för programmet. Den *hantera användare* i den här artikeln förklarar mer om hur du tilldelar den **administratör** roll till andra användare.

## <a name="change-application-name"></a>Ändra programnamn

Om du vill ändra namnet på ditt program, Använd sekundära navigeringsmenyn för att navigera till den **programinställningar** sidan i den **Administration** avsnitt.

På den **programinställningar** anger du ett namn på ditt val i den **programnamn** och välj sedan **spara**.

## <a name="change-the-application-url"></a>Ändra programmets URL

Använd sekundära navigeringsmenyn ändra URL: en för ditt program genom för att navigera till den **programinställningar** sidan i den **Administration** avsnitt.

![Programinställningssidan](media\howto-administer\image0-a.png)

På den **programinställningar** anger Webbadressen för ditt val i den **URL** och välj sedan **spara**. URL: en kan vara högst 200 tecken. Om URL: en inte är tillgänglig, visas ett verifieringsfel

> [!Note]
> Om du ändrar URL: en kan gamla URL: en hämtas av en annan Azure IoT centrala kund. I så fall är det inte längre tillgängliga som du kan använda. När du ändrar URL: en, gammalt Webbadressen inte längre fungerar och måste du meddela användarna om den nya URL som ska användas.

## <a name="change-the-application-image"></a>Ändra programavbildning

Läs mer om att använda bilder i ett Azure IoT centrala program [Förbered och ladda upp bilder till din Azure IoT centralt program](howto-prepare-images.md).

## <a name="delete-an-application"></a>Ta bort ett program

Använd sekundära navigeringsmenyn ta bort programmet genom för att navigera till den **programinställningar** sidan i den **Administration** avsnitt.

Välj **ta bort**.

> [!Note]
> Tar bort ett program blir alla data som är associerade med programmet. Om du vill ta bort ett program måste du också ha behörighet att ta bort resurser i Azure-prenumeration som du valde när du skapade programmet. Läs mer i [Use Role-Based behörighet att hantera åtkomst till resurserna i Azure-prenumeration](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="roles-in-azure-iot-central"></a>Roller i Azure IoT Central

Roller kan du kontrollera vem i din organisation kan utföra olika uppgifter för Azure IoT Central. Azure IoT Central har tre roller som du kan tilldela användare av ditt program. Roller har tilldelats av varje program. Samma användare kan ha olika roller i olika program. Du kan tilldela samma användaren kan flera roller i ett program.

### <a name="administrator"></a>Administratör

Användare i den **administratör** rollen har åtkomst till alla funktioner i ett Azure IoT centrala program.

Användaren som skapar ett program tilldelas automatiskt den **administratör** roll. Det måste alltid finnas minst en användare i den **administratör** roll.

### <a name="application-builder"></a>Application Builder

Användare i den **Application Builder** roll kan göra allt i ett Azure IoT centrala program utom administrera programmet.

### <a name="application-operator"></a>Programmet Operator

Användare i den **programmet operatorn** rollen har inte åtkomst till den **Application Builder** sidan. De kan inte administrera programmet.

## <a name="manage-users"></a>Hantera användare

Administratörer kan tilldela användare till roller i programmet.

### <a name="add-users"></a>Lägga till användare

Varje användare måste ha ett användarkonto innan de kan logga in och komma åt ett Azure IoT centrala program. Azure Active Directory (AAD) och Microsoft Accounts (MSAs) stöds i Azure IoT Central. Azure Active Directory-grupper stöds inte i Azure IoT Central.

Läs mer i [hjälp för Microsoft-konto](https://support.microsoft.com/products/microsoft-account?category=manage-account) och [Snabbstart: lägga till nya användare i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Använd sekundära navigeringsmenyn för att lägga till ett användarkonto till ett Azure IoT centrala program för att navigera till den **användare** sidan i den **Administration** avsnitt.

    ![Lista över användare](media\howto-administer\image1.png)

1. På den **användare** väljer **+ Lägg till användare** lägga till en användare.

    ![Lägg till användare](media\howto-administer\image2.png)

1. När du lägger till en användare till din Azure IoT centralt program väljer du en roll för användaren från den **rollen** listrutan. Mer information om roller i den *roller i Azure IoT centrala* i den här artikeln.

    ![Urval för Systemroll](media\howto-administer\image3.png)

    > [!NOTE]
    >  Ange användar-ID för alla användare som du vill lägga till om du vill lägga till användare i grupp avgränsade med semikolon. Välj en roll från den **rollen** listrutan och välj **spara**.

1. När du lägger till en användare visas en post för användaren på den **användare** sidan.

    ![Lista över användare](media\howto-administer\image4.png)

### <a name="edit-the-roles-assigned-to-users"></a>Redigera de roller som tilldelats till användare

Roller kan inte ändras när assinged. Om du vill ändra den roll som tilldelats till en användare, ta bort användaren och Lägg till användaren igen med en annan roll.

### <a name="delete-users"></a>Ta bort användare

Ta bort användare genom att markera kryssrutorna för en eller flera på den **användare** och väljer sedan **ta bort**.

## <a name="view-your-bill"></a>Visa fakturan

Om du vill visa fakturan, navigera till den **fakturering** sidan i den **Administration** avsnittet och väljer **fakturering**. Azure fakturering sidan öppnas i en ny flik och du kan se växeln för var och en av dina Azure IoT centrala program.

## <a name="convert-your-trial-to-a-paid-application"></a>Konvertera en utvärderingsversion till en betald program

När du har utvärderat IoT Central, kan du konvertera en utvärderingsversion till en betald program. Följ dessa steg om du vill slutföra processen självbetjäning:

1. Använd sekundära navigeringsmenyn för att navigera till den **fakturering** sidan i den **Administration** avsnitt. Om du inte har utökat utvärderingsperioden ser sidan ut så här:

    ![Kostnadsfri utvärderingsversion tillstånd](media/howto-administer/freetrial.png)

2. Klicka på **konvertera till betald**. Om du inte har utökat utvärderingsperioden popup-fönstret ser ut som följande:

    Välj lämplig Azure Active Directory-klient och Azure-prenumeration som du vill använda för IoT Central programmet i popup-fönstret.

    ![Utöka kostnadsfri utvärderingsversion](media/howto-administer/extend.png)

3. När du klickar på **konvertera**, din utvärderingsversion konverteras till en betald program och du startar hämtning debiteras.

## <a name="extend-your-free-trial"></a>Utöka din kostnadsfria utvärderingsversion

Som standard är alla gratisutvärderingar tillgängliga för 7 dagar. Om du vill öka utvärderingsperioden på 30 dagar kan du följa dessa steg:

1. Använd sekundära navigeringsmenyn för att navigera till den **fakturering** sidan i den **Administration** avsnitt:

1. Klicka på **förlänga utvärderingsperioden**. Välj lämplig Azure Active Directory-klienten och sedan Azure-prenumeration om du vill använda för tillämpningsprogrammet IoT Central i popup-fönstret:

1. Klicka på **utöka**. Utvärderingsperioden är nu giltigt i 30 dagar.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du administrerar Azure IoT centrala programmet, är här det föreslagna nästa steget:

> [!div class="nextstepaction"]
> [Skapa mall för enhet](howto-set-up-template.md)