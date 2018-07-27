---
title: Administrera en Azure IoT Central program | Microsoft Docs
description: Som en administratör, hur du administrerar Azure IoT Central programmet
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a43febf1e78f80451b6aeed19e095b2c313d3216
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284861"
---
# <a name="how-to-administer-your-application"></a>Hur du administrerar ditt program

När du skapar ett Microsoft Azure IoT Central-program kan du använda den **Administration** avsnitt av Azure IoT Central användargränssnitt för att administrera den. Navigera till den **Administration** väljer **Administration** på den vänstra navigeringsmenyn.

Den **Administration** avsnittet kan du:

- Hantera användare

- Hantera roller

- Visa faktureringsinformation

- Hantera tillämpningsinställningar

- Utöka en kostnadsfri utvärderingsversion

I den **Administration** avsnittet, finns det en sekundär navigeringsmenyn med länkar till olika administrationsuppgifter.

Du använder den **Administration** avsnittet, måste du vara i den **administratör** roll i programmet Azure IoT Central. Om du skapar ett Azure IoT Central program kan du automatiskt tilldelas den **administratör** rollen för programmet. Den *hantera användare* i den här artikeln innehåller mer information om hur du tilldelar den **administratör** rollen till andra användare.

## <a name="change-application-name"></a>Ändra programnamn

Om du vill ändra namnet på ditt program, använder du den sekundära navigeringsmenyn för att navigera till den **programinställningar** sidan i den **Administration** avsnittet.

På den **programinställningar** anger du ett namn på ditt val i den **programnamn** och välj sedan **spara**.

## <a name="change-the-application-url"></a>Ändra programmets URL

Om du vill ändra URL: en för ditt program, använder du den sekundära navigeringsmenyn för att navigera till den **programinställningar** sidan i den **Administration** avsnittet.

![Inställningssidan för program](media\howto-administer\image0-a.png)

På den **programinställningar** anger du URL: en för ditt val i den **URL** och välj sedan **spara**. Din URL får vara högst 200 tecken. Om URL: en inte är tillgänglig, visas ett valideringsfel

> [!Note]
> Om du ändrar din URL måste kan din gamla URL utföras av en annan Azure IoT Central-kund. I så fall kan den inte längre tillgängliga som du kan använda. När du ändrar din URL för gammalt URL: en inte längre fungerar och måste du meddela användarna om den nya URL som ska användas.

## <a name="change-the-application-image"></a>Ändra programavbildningen

Mer information om hur du använder avbildningar i Azure IoT Central program finns i [Förbered och ladda upp bilder till Azure IoT Central programmet](howto-prepare-images.md).

## <a name="copy-an-application"></a>Kopiera ett program

Du kan skapa en kopia av alla program, minus eventuella enhetsinstanser, historik för enheten och användardata. Kopian är ett avgiftsbelagt program som du kommer att debiteras för. Du kan inte skapa en utvärderingsprogram genom att kopiera ett annat program.

Om du vill kopiera ett program, navigerar du till den **programinställningar** och klicka på den **kopia** knappen.

![Inställningssidan för program](media\howto-administer\appCopy1.png)

Klicka på den **kopia** knappen öppnas en dialogruta där du kan välja ett namn, URL: en, AAD directory, prenumeration och Azure-region för det nya programmet som ska skapas genom att kopiera ditt program. Välj värden för var och en av dessa fält och klickar sedan på den **kopiera** för att bekräfta att du vill fortsätta. Du kan läsa mer om vad du ska ange för de här värdena i artikeln om [så här skapar du ett program](howto-create-application.md).

![Inställningssidan för program](media\howto-administer\appCopy2.png)

När appen kopieringen lyckas, du kommer att kunna navigera till det nya programmet som har skapats genom att kopiera ditt program genom att klicka på länken som visas på den **programinställningar** sidan.

![Inställningssidan för program](media\howto-administer\appCopy3.png)

> [!Note]
> Kopiera ett program kommer att kopiera definitionen av regler och åtgärder. Eftersom användare som har åtkomst till den ursprungliga appen inte kopieras till den kopierade appen, kommer du måste manuellt lägga till användare i åtgärder, till exempel e-post som användare är ett krav.

## <a name="delete-an-application"></a>Ta bort ett program

Om du vill ta bort ditt program, använder du den sekundära navigeringsmenyn för att navigera till den **programinställningar** sidan i den **Administration** avsnittet.

Välj **ta bort**.

> [!Note]
> Tar bort ett program oåterkalleligt tar bort alla data kopplade till programmet. Om du vill ta bort ett program, måste du också ha behörighet att ta bort resurser i Azure-prenumeration som du angav när du skapade programmet. Mer information finns i [Use Role-Based Access Control för att hantera åtkomst till din Azure-prenumerationsresurser](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="roles-in-azure-iot-central"></a>Roller i Azure IoT Central

Roller kan du styra vem i din organisation kan utföra olika uppgifter i Azure IoT Central. Azure IoT Central har tre roller som du kan tilldela till användare i ditt program. Roller har tilldelats av varje program. Samma användare kan ha olika roller i olika program. Du kan tilldela den samma användare kan till flera roller i ett program.

### <a name="administrator"></a>Administratör

Användare i den **administratör** rollen har åtkomst till alla funktioner i ett program med Azure IoT Central.

Den användare som skapar ett program tilldelas automatiskt till den **administratör** roll. Det alltid måste finnas minst en användare i den **administratör** roll.

### <a name="application-builder"></a>Application Builder

Användare i den **Application Builder** roll kan göra allt i ett Azure IoT Central program förutom administrera programmet.

### <a name="application-operator"></a>Program-Operator

Användare i den **program operatorn** rollen har inte åtkomst till den **Application Builder** sidan. De kan inte administrera programmet.

## <a name="manage-users"></a>Hantera användare

Administratörer kan tilldela användare till roller i programmet.

### <a name="add-users"></a>Lägga till användare

Varje användare måste ha ett användarkonto innan de kan logga in och komma åt ett program med Azure IoT Central. Microsoft-Accounts (MSA: er) och Azure Active Directory (AAD)-konton stöds i Azure IoT Central. Azure Active Directory-grupper stöds inte för närvarande i Azure IoT Central.

Mer information finns i [Microsoft-konto hjälp](https://support.microsoft.com/products/microsoft-account?category=manage-account) och [Snabbstart: lägga till nya användare till Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Om du vill lägga till ett användarkonto till ett Azure IoT Central program, använder du den sekundära navigeringsmenyn för att navigera till den **användare** sidan i den **Administration** avsnittet.

    ![Lista över användare](media\howto-administer\image1.png)

1. På den **användare** väljer **+ Lägg till användare** lägga till en användare.

    ![Lägg till användare](media\howto-administer\image2.png)

1. När du lägger till en användare till din Azure IoT Central program, Välj en roll för användaren från den **rollen** utskriftsjobb. Mer information om roller i den *roller i Azure IoT Central* i den här artikeln.

    ![Systemroll](media\howto-administer\image3.png)

    > [!NOTE]
    >  Lägga till användare i grupp genom att ange användar-ID för alla användare som du vill lägga till avgränsade med semikolon. Välj en roll från den **rollen** listrutan och välj **spara**.

1. När du lägger till en användare visas en post för användaren på den **användare** sidan.

    ![Användarlista](media\howto-administer\image4.png)

### <a name="edit-the-roles-assigned-to-users"></a>Redigera de roller som tilldelats användare

Roller kan inte ändras när assinged. Om du vill ändra rolltilldelningen för en användare, ta bort användaren och Lägg till användaren igen med en annan roll.

### <a name="delete-users"></a>Ta bort användare

Om du vill ta bort användare, markera en eller flera kryssrutor på den **användare** och välj sedan **ta bort**.

## <a name="view-your-bill"></a>Visa din faktura

Om du vill visa din faktura, navigera till den **fakturering** sidan i den **Administration** avsnittet och välj **fakturering**. Sidan för Azure-fakturering öppnas i en ny flik och du kan se på fakturan för var och en av dina Azure IoT Central program.

## <a name="convert-your-trial-to-a-paid-application"></a>Konvertera din utvärderingsversion till ett betalprogram

När du har utvärderat IoT Central, kan du konvertera din utvärderingsversion till ett betalprogram. Följ dessa steg för att slutföra denna självbetjäningsprocess:

1. Använd den sekundära navigeringsmenyn för att navigera till den **fakturering** sidan i den **Administration** avsnittet. Om du inte har utökat utvärderingsperioden ser sidan ut så här:

    ![Kostnadsfri utvärderingsversion tillstånd](media/howto-administer/freetrial.png)

2. Klicka på **konvertera till betalda**. Om du inte har utökat utvärderingsperioden popup-fönstret ser ut som följande:

    I popup-fönstret väljer du lämplig Azure Active Directory-klient och sedan på Azure-prenumeration som du vill använda för ditt IoT Central-program.

    ![Utöka kostnadsfri utvärderingsversion](media/howto-administer/extend.png)

3. När du klickar på **konvertera**, din utvärderingsversion konverteras till ett betalprogram och du börja faktureras.

## <a name="extend-your-free-trial"></a>Utöka din kostnadsfria utvärderingsversion

Som standard är alla kostnadsfria utvärderingsversioner tillgängliga i 7 dagar. Om du vill öka din utvärdering med 30 dagar kan följa du dessa steg:

1. Använd den sekundära navigeringsmenyn för att navigera till den **fakturering** sidan i den **Administration** avsnittet:

1. Klicka på **utöka utvärdering**. I popup-fönstret väljer du lämplig Azure Active Directory-klient och sedan Azure-prenumeration om du vill använda för ditt IoT Central-program:

1. Klicka sedan på **utöka**. Din utvärderingsversion är nu giltig i 30 dagar.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du administrerar Azure IoT Central programmet, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Konfigurera enheten-mall](howto-set-up-template.md)