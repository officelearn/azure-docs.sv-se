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
ms.openlocfilehash: 25b4777be4257933b84d58d0f10cf12571de9590
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50155328"
---
# <a name="administer-your-iot-central-application"></a>Administrera programmets IOT Central

När du skapar ett Microsoft Azure IoT Central-program kan du använda den **Administration** avsnitt av Azure IoT Central användargränssnitt för att administrera den. Gå till den **Administration** väljer **Administration** på den vänstra navigeringsmenyn.

Den **Administration** avsnittet kan du:

- Hantera användare

- Hantera roller

- Visa faktureringsinformation

- Hantera tillämpningsinställningar

- Prova en kostnadsfri utvärderingsversion

I den **Administration** avsnittet, en sekundär navigeringsmenyn innehåller länkar till olika administrationsuppgifter.

Du använder den **Administration** avsnittet, måste du vara i den **administratör** för ett program med Azure IoT Central. Om du skapar ett Azure IoT Central program tilldelas du automatiskt till den **administratör** rollen för programmet. Den [hantera användare](#manage-users) i den här artikeln innehåller mer information om hur du tilldelar den **administratör** rollen till andra användare.

## <a name="change-application-name"></a>Ändra programnamn

Du kan ändra namnet på ditt program med den sekundära navigeringsmenyn för att gå till den **programinställningar** sidan i den **Administration** avsnittet.

På den **programinställningar** anger du ett namn på ditt val i den **programnamn** fält. Välj sedan **Spara**.

## <a name="change-the-application-url"></a>Ändra programmets URL

Om du vill ändra URL: en för ditt program, använder du den sekundära navigeringsmenyn för att navigera till den **programinställningar** sidan i den **Administration** avsnittet.

![Inställningssidan för program](media\howto-administer\image0-a.png)

På den **programinställningar** anger du URL: en för ditt val i den **URL** och välj sedan **spara**. Din URL får vara högst 200 tecken. Om URL: en är inte tillgängligt, visas ett verifieringsfel.

> [!Note]
> Om du ändrar din URL måste kan din gamla URL utföras av en annan Azure IoT Central-kund. Om det sker så är det inte längre tillgängliga som du kan använda. När du ändrar din URL gammalt URL: en fungerar inte längre och du behöver att meddela användarna om den nya URL som ska användas.

## <a name="change-the-application-image"></a>Ändra programavbildningen

Mer information om hur du använder avbildningar i Azure IoT Central program finns i [Förbered och ladda upp bilder till Azure IoT Central programmet](howto-prepare-images.md).

## <a name="copy-an-application"></a>Kopiera ett program

Du kan skapa en kopia av alla program, minus eventuella enhetsinstanser, historik för enheten och användardata. Kopian är ett avgiftsbelagt program som du debiteras för. Du kan inte skapa en utvärderingsprogram genom att kopiera ett annat program.

Om du vill kopiera ett program, går du till den **programinställningar** sidan. Välj sedan den **kopia** knappen.

![Inställningssidan för program](media\howto-administer\appCopy1.png)

Att välja den **kopia** knappen öppnas en dialogruta där du kan välja ett namn, URL: en, Azure AD directory, prenumeration och Azure-region för det nya programmet som ska skapas genom att kopiera ditt program. Välj värden för var och en av dessa fält. Välj sedan den **kopiera** för att bekräfta att du vill fortsätta. Du kan läsa mer om vad du ska ange för de här värdena i den här artikeln om [så här skapar du ett program](howto-create-application.md).

![Inställningssidan för program](media\howto-administer\appCopy2.png)

När appen kopieringen lyckas, går du till det nya programmet som har skapats genom att kopiera ditt program. Gå till appen genom att klicka på länken som visas på den **programinställningar** sidan.

![Inställningssidan för program](media\howto-administer\appCopy3.png)

> [!Note]
> Kopiera ett program kopierar också definitionen av regler och åtgärder. Men eftersom användare som har åtkomst till den ursprungliga appen inte kopieras till den kopierade app, behöver du lägga till användare manuellt till åtgärder, till exempel e-post som användare är ett krav.

## <a name="delete-an-application"></a>Ta bort ett program

Om du vill ta bort ditt program, använder du den sekundära navigeringsmenyn för att navigera till den **programinställningar** sidan i den **Administration** avsnittet.

Välj **ta bort**.

> [!Note]
> Tar bort ett program permanent tar bort alla data som är associerat med programmet.  Om du vill ta bort ett program, måste du också ha behörighet att ta bort resurser i Azure-prenumeration som du angav när du skapade programmet. Mer information finns i [använda rollbaserad åtkomstkontroll för att hantera åtkomst till din Azure-prenumerationsresurser](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="roles-in-azure-iot-central"></a>Roller i Azure IoT Central

Roller ger dig kontroll över vilka i organisationen kan utföra olika uppgifter i Azure IoT Central. Azure IoT Central har tre roller som du kan tilldela till användare i ditt program. Roller har tilldelats av varje program. Samma användare kan ha olika roller i olika program. Du kan tilldela samma användare till flera roller i ett program.

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

Varje användare måste ha ett användarkonto innan de kan logga in och komma åt ett program med Azure IoT Central. Microsoft-Accounts (MSA: er) och Azure Active Directory (Azure AD)-konton stöds i Azure IoT Central. Azure Active Directory-grupper stöds inte för närvarande i Azure IoT Central.

Mer information finns i [Microsoft-konto hjälp](https://support.microsoft.com/products/microsoft-account?category=manage-account) och [Snabbstart: lägga till nya användare till Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Om du vill lägga till ett användarkonto till ett Azure IoT Central program, använder du den sekundära navigeringsmenyn för att gå till den **användare** sidan i den **Administration** avsnittet.

    ![Lista över användare](media\howto-administer\image1.png)

1. Lägga till en användare på den **användare** väljer **+ Lägg till användare**.

    ![Lägga till användare](media\howto-administer\image2.png)

1. Välj en roll för användaren från den **rollen** nedrullningsbara menyn. Mer information om roller i den *roller i Azure IoT Central* i den här artikeln.

    ![Systemroll](media\howto-administer\image3.png)

    > [!NOTE]
    >  Lägga till användare i grupp genom att ange användar-ID för alla användare som du vill lägga till avgränsade med semikolon. Välj en roll från den **rollen** nedrullningsbara menyn. Välj sedan **Spara**.

1. När du lägger till en användare visas en post för användaren på den **användare** sidan.

    ![Användarlista](media\howto-administer\image4.png)

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Redigera de roller som är tilldelade till användare

Roller kan inte ändras när de har tilldelats. Om du vill ändra den roll som har tilldelats en användare, ta bort användaren och Lägg sedan till användaren igen med en annan roll.

### <a name="delete-users"></a>Ta bort användare

Om du vill ta bort användare, väljer du en eller flera kryssrutor på den **användare** sidan. Välj sedan **Ta bort**.

## <a name="view-your-bill"></a>Visa din faktura

Om du vill visa din faktura, går du till den **fakturering** sidan i den **Administration** avsnittet. Välj sedan **fakturering**. Sidan för Azure-fakturering öppnas i en ny flik, där du kan se på fakturan för var och en av dina Azure IoT Central program.

## <a name="convert-your-trial-to-a-paid-application"></a>Konvertera din utvärderingsversion till ett betalprogram

När du har utvärderat IoT Central, kan du konvertera din utvärderingsversion till ett betalprogram. Följ dessa steg för att slutföra denna självbetjäningsprocess:

1. Använd den sekundära navigeringsmenyn för att gå till den **fakturering** sidan i den **Administration** avsnittet. Om du inte har utökat utvärderingsperioden sidan ser ut som på följande skärmbild:

    ![Kostnadsfri utvärderingsversion tillstånd](media/howto-administer/freetrial.png)

2. Välj **konvertera till betalda**. Om du inte har utökat utvärderingsperioden popup-fönstret ser ut som på följande skärmbild:

    ![Utöka kostnadsfri utvärderingsversion](media/howto-administer/extend.png)

3. I popup-fönstret väljer du lämplig Azure Active Directory-klient och sedan Azure-prenumeration du använder för din IoT Central-program.

3. När du har valt **konvertera**, din utvärderingsversion nyttolastobjekt till ett betalprogram och du börja faktureras.

## <a name="extend-your-free-trial"></a>Utöka din kostnadsfria utvärderingsversion

Som standard är alla kostnadsfria utvärderingsversioner tillgängliga i sju dagar. Följ dessa steg om du vill öka din utvärdering med 30 dagar:

1. Använd den sekundära navigeringsmenyn för att gå till den **fakturering** sidan i den **Administration** avsnittet.

1. Välj **utöka utvärdering**. I popup-fönstret väljer du lämplig Azure Active Directory-klient och sedan Azure-prenumeration om du vill använda för ditt IoT Central-program.

1. Välj sedan **utöka**. Din utvärderingsversion är nu giltig i 30 dagar.

## <a name="use-the-azure-sdks-for-control-plane-operations"></a>Använd Azure SDK för kontrollplanåtgärder

IoT Central Azure Resource Manager SDK-paket är tillgängliga för Node, Python, C#, Ruby, Java och Go. Dessa bibliotek support kontrollplanåtgärder för IoT Central, vilket gör att du kan skapa, visa, uppdatera eller ta bort IoT Central-program. Du får även hjälp för att hantera autentisering och felhantering som är specifika för varje språk. 

Du hittar exempel på hur du använder Azure Resource Manager SDK: er på [ https://github.com/emgarten/iotcentral-arm-sdk-examples ](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Om du vill veta mer kan du ta en titt på dessa paket på GitHub.

| Språk | Lagringsplats | Paket |
| ---------| ---------- | ------- |
| Node | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du administrerar Azure IoT Central programmet, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Konfigurera enheten-mall](howto-set-up-template.md)
