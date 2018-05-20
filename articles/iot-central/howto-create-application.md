---
title: Skapa ett program i Azure IoT centrala | Microsoft Docs
description: Som en administratör, hur du skapar ett Azure IoT centrala program.
services: iot-central
author: TanmayBhagwat
ms.author: tanmayb
ms.date: 03/20/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 83879c6b81985f61b9fcff665e9f764c1346592e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="create-your-azure-iot-central-application"></a>Skapa Central för Azure IoT-program

Du skapar din Microsoft Azure IoT Central program från den [skapa program](https://apps.microsoftiotcentral.com/create) sidan. Om du vill skapa ett Azure IoT centrala program måste du fylla i alla fält på den här sidan och välj sedan **skapa**. Den här artikeln innehåller mer information om varje fält.

![Skapa sida för programmet](media\howto-create-application\image1.png)

## <a name="payment-plan"></a>Betalningsplan

Du kan skapa en utvärderingsversion eller betald program. Läs mer om testversion och betald program på den här sidan.

## <a name="application-name"></a>Programnamn

Namnet på ditt program visas på den **Programhanterare** sidan och inom varje Azure IoT centralt program. Du kan välja valfritt namn för din Azure IoT centralt program. Välj ett namn som är meningsfullt för dig och andra i din organisation.

## <a name="application-url"></a>Program-URL

Programmets URL är en länk till programmet. Du kan spara ett bokmärke i webbläsaren eller dela den med andra.

När du anger namnet för ditt program är programmets URL genereras automatiskt. Om du vill kan välja du en annan URL för ditt program. Varje Azure IoT centrala URL måste vara unika. Ett felmeddelande visas om den URL som du redan har tagits.

## <a name="directory"></a>Katalog

Endast i betald program.

Välj en Azure Active Directory-klient för att skapa ett Azure IoT centralt program. En Azure Active Directory-klient innehåller användaridentiteter, autentiseringsuppgifter och andra organisationsinformation. Azure-prenumerationer kan associeras med en enda Azure Active Directory-klient.

Om du inte har en Azure Active Directory-klient skapas ett åt dig när du skapar en Azure-prenumeration.

Läs mer i [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Azure-prenumeration

En Azure-prenumeration kan du skapa instanser av Azure-tjänster. Azure IoT Central automatiskt hittar alla Azure-prenumerationer som du har åtkomst till och visar dem i en listruta på den **skapa program** sidan. Välj en Azure-prenumeration att skapa en ny Azure IoT centralt program.

Om du inte har en Azure-prenumeration kan skapa du en på den här sidan. När du har skapat den Azure-prenumerationen, gå tillbaka till den **skapa program** sidan. Den nya prenumerationen visas i den **Azure-prenumeration** listrutan.

Läs mer i [Azure-prenumerationer](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Region

Endast i betald program.

Välj den region där du vill skapa Azure IoT centrala programmet. Normalt bör du välja den region som är fysiskt närmast dina enheter att få bästa möjliga prestanda.

Läs mer i [Azure-regioner](https://docs.microsoft.com/en-us/azure/guides/developer/azure-developer-guide#azure-regions).

Du kan se de regioner där Azure IoT centrala är tillgängligt på den [produkter som är tillgängliga efter region](https://azure.microsoft.com/regions/services/) sidan.

> [!Note]
> När du väljer region flytta du inte senare programmet till annan region.

## <a name="application-template"></a>Mall för program

Du kan välja något av mallar för tillgängliga program för det nya Azure IoT centrala programmet. En programmall kan innehåller fördefinierade mallar för enheten och instrumentpaneler som hjälper dig att komma igång:

| Mall för program | Beskrivning |
| -------------------- | ----------- |
| Anpassat program   | Skapar ett tomt program att fylla i med din egen enhet mallar och enheter. |
| Exempel Contoso       | Skapar ett program som innehåller en mall för enheten för en enkel ansluten enhet. Du kan använda mallen för att börja utforska Azure IoT Central. |
| Exempel Devkits       | Skapar ett program med enheten mallar du kan ansluta en MXChip eller hallon Pi-enhet. Använd den här mallen om du utvecklar enheten experimentera med kod på en av dessa enheter. |

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar ett Azure IoT centrala program, är här det föreslagna nästa steget:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)