---
title: Administrera en Azure IoT Central program | Microsoft Docs
description: Som administratör, hur du administrerar Azure IoT Central programmet
author: viv-liu
ms.author: viviali
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 87ed31836fcda922b085ec951eb6d9d14542db6a
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467567"
---
# <a name="administer-your-iot-central-application"></a>Administrera ditt IoT Central-program

När du har skapat ett IoT Central-program går du till den **Administration** avsnitt:

- Hantera tillämpningsinställningar
- Hantera användare
- Hantera roller
- Visa din faktura
- Konvertera din utvärderingsversion till betala per användning
- Exportera data
- Hantera enhetsanslutning
- Använd åtkomsttoken för developer tools
- Anpassa Användargränssnittet för ditt program
- Anpassa hjälplänkar i programmet
- Hantera IoT Central programmässigt

Du använder den **Administration** avsnittet, måste du vara i den **administratör** för ett program med Azure IoT Central. Om du skapar ett Azure IoT Central program tilldelas du automatiskt till den **administratör** rollen för programmet. Den [hantera användare](#manage-users) i den här artikeln innehåller mer information om hur du tilldelar den **administratör** rollen till andra användare.

## <a name="manage-application-settings"></a>Hantera tillämpningsinställningar

### <a name="change-application-name-and-url"></a>Ändra namn och URL: en
I den **programinställningar** kan du kan ändra namnet och URL: en för ditt program och sedan välja **spara**.

![Inställningssidan för program](media/howto-administer/image0-a.png)

Om administratören skapar ett anpassat tema för ditt program, den här sidan innehåller ett alternativ för att dölja den **programnamn** i Användargränssnittet. Detta är användbart om programlogotyp i anpassat tema som innehåller programnamnet. Mer information finns i [anpassa Azure IoT Central Användargränssnittet](./howto-customize-ui.md).

> [!Note]
> Om du ändrar din URL måste kan din gamla URL utföras av en annan Azure IoT Central-kund. Om det sker så är det inte längre tillgängliga som du kan använda. När du ändrar din URL gammalt URL: en fungerar inte längre och du behöver att meddela användarna om den nya URL som ska användas.

### <a name="prepare-and-upload-image"></a>Förbereda och ladda upp avbildning
Om du vill ändra programavbildningen [Förbered och ladda upp bilder till Azure IoT Central programmet](howto-prepare-images.md).

### <a name="copy-an-application"></a>Kopiera ett program
Du kan skapa en kopia av alla program, minus eventuella enhetsinstanser, historik för enheten och användardata. Kopian är en betala per användning-program som du kommer att debiteras för. Du kan inte skapa ett program för utvärdering i det här sättet.

Välj **kopiera**. I dialogrutan, ange information för det nya programmet betala per användning. Välj sedan **kopia** att bekräfta att du vill fortsätta. Mer information om fälten i det här formuläret i [skapar ett program](quick-deploy-iot-central.md) Snabbstart.

![Inställningssidan för program](media/howto-administer/appcopy2.png)

När appen kopieringen lyckas, kan du navigera till det nya programmet med hjälp av länken.

![Inställningssidan för program](media/howto-administer/appcopy3a.png)

> [!Note]
> Kopiera ett program kopierar också definitionen av regler och åtgärder. Men eftersom användare som har åtkomst till den ursprungliga appen inte kopieras till den kopierade app, behöver du lägga till användare manuellt till åtgärder, till exempel e-post som användare är en förutsättning. I allmänhet är det en bra idé att kontrollera de regler och åtgärder för att se till att de är aktuella i den nya appen.

### <a name="delete-an-application"></a>Ta bort ett program

> [!Note]
> Om du vill ta bort ett program, måste du också ha behörighet att ta bort resurser i Azure-prenumeration som du angav när du skapade programmet. Mer information finns i [använda rollbaserad åtkomstkontroll för att hantera åtkomst till din Azure-prenumerationsresurser](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

Använd den **ta bort** för att permanent ta bort ditt IoT Central-program. Den här åtgärden tar bort alla data som är associerat med programmet.

## <a name="manage-users"></a>Hantera användare

### <a name="add-users"></a>Lägg till användare

Varje användare måste ha ett användarkonto innan de kan logga in och komma åt ett program med Azure IoT Central. Microsoft-Accounts (MSA: er) och Azure Active Directory (Azure AD)-konton stöds i Azure IoT Central. Azure Active Directory-grupper stöds inte för närvarande i Azure IoT Central.

Mer information finns i [Microsoft-konto hjälp](https://support.microsoft.com/products/microsoft-account?category=manage-account) och [snabbstarten: Lägga till nya användare i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Om du vill lägga till en användare i ett IoT Central-program går du till den **användare** sidan i den **Administration** avsnittet.

    ![Lista över användare](media/howto-administer/image1.png)

1. Lägga till en användare på den **användare** väljer **+ Lägg till användare**.

1. Välj en roll för användaren från den **rollen** nedrullningsbara menyn. Mer information om roller i den [hantera roller](#manage-roles) i den här artikeln.

    ![Systemroll](media/howto-administer/image3.png)

    > [!NOTE]
    >  Lägga till användare i grupp genom att ange användar-ID för alla användare som du vill lägga till avgränsade med semikolon. Välj en roll från den **rollen** nedrullningsbara menyn. Välj sedan **Spara**.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Redigera de roller som är tilldelade till användare

Roller kan inte ändras när de har tilldelats. Om du vill ändra den roll som har tilldelats en användare, ta bort användaren och Lägg sedan till användaren igen med en annan roll.

### <a name="delete-users"></a>Ta bort användare

Om du vill ta bort användare, väljer du en eller flera kryssrutor på den **användare** sidan. Välj sedan **Ta bort**.

## <a name="manage-roles"></a>Hantera roller

Roller kan du styra vem i din organisation kan utföra olika uppgifter i IoT Central. Det finns tre roller som du kan tilldela till användare i ditt program.

### <a name="administrator"></a>Administratör

Användare i den **administratör** rollen har åtkomst till alla funktioner i ett program.

Den användare som skapar ett program tilldelas automatiskt till den **administratör** roll. Det alltid måste finnas minst en användare i den **administratör** roll.

### <a name="application-builder"></a>Application Builder

Användare i den **Application Builder** roll kan göra allt i ett program förutom administrera programmet. Builders kan skapa, redigera och ta bort enheten mallar och enheter, hantera enhetsuppsättningar och köra analyser och jobb. Builders har inte åtkomst till den **Administration** avsnittet av programmet.

### <a name="application-operator"></a>Application Operator

Användare i den **program operatorn** rollen kan inte göra ändringar i mallarna för enheten och kan inte administrera programmet. Operatörer kan lägga till och ta bort enheter, hantera enhetsuppsättningar och köra analyser och jobb. Operatörer har inte åtkomst till den **Application Builder** och **Administration** sidor.

## <a name="view-your-bill"></a>Visa din faktura

Om du vill visa din faktura, går du till den **fakturering** sidan i den **Administration** avsnittet. Sidan för Azure-fakturering öppnas i en ny flik, där du kan se på fakturan för var och en av dina Azure IoT Central program.

### <a name="convert-your-trial-to-pay-as-you-go"></a>Konvertera din utvärderingsversion till betala per användning

Du kan konvertera din utvärderingsversion-program till betala per användning. Här är skillnaderna mellan dessa typer av program.

- **Utvärderingsversion** program är kostnadsfria under sju dagar innan de upphör att gälla. De kan konverteras till betala per användning när som helst innan de upphör att gälla.
- **Betala per användning** debiteras programmen per enhet, där de första fem enheterna som är kostnadsfri.

Läs mer om prissättning på [sidan med prisinformation för Azure IoT Central.](https://azure.microsoft.com/pricing/details/iot-central/)

Följ dessa steg för att slutföra denna självbetjäningsprocess:

1. Gå till den **fakturering** sidan i den **Administration** avsnittet.

    ![Utvärderingsversion tillstånd](media/howto-administer/freetrialbilling.png)

1. Välj **konvertera till betala per användning**.

    ![Konvertera utvärderingsversionen](media/howto-administer/convert.png)

1. Välj lämplig Azure Active Directory och sedan Azure-prenumeration du använder för din betala per användning-program.

1. När du har valt **konvertera**, ditt program är nu en betala per användning-program och du börja faktureras.

## <a name="export-data"></a>Exportera data

Du kan aktivera **löpande dataexport** att exportera mått, enheter och enhetsdata för mallar till Azure Blob storage-kontot. Mer information om hur du [exportera dina data](howto-export-data.md).

## <a name="manage-device-connection"></a>Hantera enhetsanslutning

Anslut enheter i stor skala i ditt program som använder nycklar och certifikat här. Läs mer om [ansluta enheter](concepts-connectivity.md).

## <a name="use-access-tokens"></a>Använd åtkomsttoken

Generera åtkomsttoken för att använda dem i utvecklarverktyg. Verktyget endast developer är för närvarande IoT Central-explorer för övervakning enhetsmeddelanden och ändringar i inställningar och egenskaper. Läs mer om den [IoT Central explorer](howto-use-iotc-explorer.md).

## <a name="customize-your-application"></a>Anpassa ditt program

Mer information om hur du ändrar färg och ikoner i ditt program finns i [anpassa Azure IoT Central Användargränssnittet](./howto-customize-ui.md).

## <a name="customize-help"></a>Anpassa hjälp

Läs mer om att lägga till anpassade hjälplänkar i ditt program, [anpassa Azure IoT Central Användargränssnittet](./howto-customize-ui.md).

## <a name="manage-programatically"></a>Hantera programmässigt

IoT Central Azure Resource Manager SDK-paket är tillgängliga för Node, Python, C#, Ruby, Java och Go. Du kan använda dessa paket för att skapa, visa, uppdatera eller ta bort IoT Central-program. De ingår hjälp för att hantera autentisering och felhantering.

Du hittar exempel på hur du använder Azure Resource Manager SDK: er på [ https://github.com/emgarten/iotcentral-arm-sdk-examples ](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Mer information finns i följande GitHub-databaser och paket:

| Språk | Lagringsplats | Paket |
| ---------| ---------- | ------- |
| Nod | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Gå till | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du administrerar Azure IoT Central programmet, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Konfigurera enheten-mall](howto-set-up-template.md)
