---
title: Skapa ett Azure IoT Central-program | Microsoft Docs
description: Som en administratör, hur du skapar ett Azure IoT Central program.
services: iot-central
ms.service: iot-central
author: tbhagwat3
ms.author: tanmayb
ms.date: 07/09/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 1fbe3ea142e1dd738cd341f57d2b8f48b539ac75
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003774"
---
# <a name="create-your-azure-iot-central-application"></a>Skapa ditt Azure IoT Central-program

Du skapar ditt Microsoft Azure IoT Central program från den [skapa program](https://apps.microsoftiotcentral.com/create) sidan. Om du vill skapa ett Azure IoT Central program, måste du fylla i alla fält på den här sidan och välj sedan **skapa**. Hittar du har mer information om vart och ett av fälten nedan.

![Skapa program sida](media\howto-create-application\image1.png)

## <a name="payment-plan"></a>Betalningsplan

Du kan skapa antingen en utvärderingsversion eller ett betalprogram. Läs mer om testversion och betald program på [Azure IoT Central prissättningssidan](https://azure.microsoft.com/pricing/details/iot-central/)...

## <a name="application-name"></a>Programnamn

Namnet på ditt program visas på den **Programhanterare** sidan och inom varje Azure IoT Central program. Du kan välja ett namn för ditt program med Azure IoT Central. Välj ett namn som passar dig och till andra i din organisation.

## <a name="application-url"></a>Program-URL

Programmets URL är en länk till ditt program. Du kan spara ett bokmärke till den i webbläsaren eller dela den med andra.

När du anger namnet på programmet, genereras programmets URL automatiskt. Om du vill kan välja du en annan URL för ditt program. Varje Azure IoT Central-URL måste vara unikt i Azure IoT Central. Du ser ett felmeddelande visas om URL: en som du väljer finns redan.

## <a name="directory"></a>Katalog

Endast i betalprogram.

Välj en Azure Active Directory-klient för att skapa ett Azure IoT Central program. En Azure Active Directory-klient innehåller användaridentiteter, autentiseringsuppgifter och annan organisationens information. Flera Azure-prenumerationer kan associeras med en enda Azure Active Directory-klient.

Om du inte har en Azure Active Directory-klient, skapas en åt dig när du skapar en Azure-prenumeration.

Mer information finns i [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Azure-prenumeration

En Azure-prenumeration kan du skapa instanser av Azure-tjänster. Azure IoT Central automatiskt söker efter alla Azure-prenumerationer som du har åtkomst till och visar dem i en listruta på den **skapa program** sidan. Välj en Azure-prenumeration att skapa ett nytt Azure IoT Central-program.

Om du inte har en Azure-prenumeration kan du skapa en på den [Azure registreringssidan](https://aka.ms/createazuresubscription). När du skapar Azure-prenumeration kan du gå tillbaka till den **skapa program** sidan. Din nya prenumeration visas i den **Azure-prenumeration** utskriftsjobb.

Mer information finns i [Azure-prenumerationer](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Region

Endast i betalprogram.

Välj den region där du vill skapa din Azure IoT Central-App. Normalt bör du välja den region som är fysiskt närmast dina enheter att få bästa möjliga prestanda.

Mer information finns i [Azure-regioner](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#azure-regions).

Du kan se de regioner där Azure IoT Central är tillgänglig på den [produkttillgänglighet per region](https://azure.microsoft.com/regions/services/) sidan.

> [!Note]
> När du väljer en region kan flytta du inte senare ditt program till en annan region.

## <a name="application-template"></a>Mall för program

Du kan välja någon av de tillgängliga mallarna för din nya Azure IoT Central program. En programmall kan innehålla fördefinierade objekt, till exempel mallar för enheten och instrumentpaneler som hjälper dig att komma igång.

| Mall för program | Beskrivning |
| -------------------- | ----------- |
| Anpassade program   | Skapar ett tomt program som du kan fylla med dina egna enhet mallar och enheter. |
| Exemplet Contoso       | Skapar ett program som innehåller en mall för enheten för en enkel ansluten enhet. Använd den här mallen för att börja utforska Azure IoT Central. |
| Exemplet Devkits       | Skapar ett program med mallar för enheten där du kan ansluta en MXChip eller Raspberry Pi-enhet. Använd den här mallen om du är en enhet utvecklare experimentera med kod på någon av dessa enheter. |

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar ett Azure IoT Central program, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)