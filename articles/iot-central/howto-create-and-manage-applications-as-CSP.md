---
title: Skapa och hantera program i Azure IoT Central som en Kryptografiprovider | Microsoft Docs
description: Som en Kryptografiprovider, hur du skapar ett Azure IoT Central program för kunden.
services: iot-central
ms.service: iot-central
author: tbhagwat3
ms.author: tanmayb
ms.date: 07/09/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: d32e05e99543b30ee92ea455ae2f800b09d83661
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009296"
---
# <a name="as-a-csp-create-and-manage-an-azure-iot-central-application-on-behalf-of-your-customer"></a>Som en Kryptografiprovider, skapa och hantera en Azure IoT Central-App för din kunds räkning 

Programmet Microsoft Cloud Solution Provider (CSP) är ett Microsoft-Reseller-program. Avsikten är att ge våra partner med ett enda program att återförsälja alla kommersiella onlinetjänster. Läs mer om den [programmet Cloud Solution Provider](https://partner.microsoft.com/cloud-solution-provider).

Som en Kryptografiprovider som du kan skapa och hantera Microsoft Azure IoT Central program åt dina kunder via de [Microsoft Partner Center](https://partnercenter.microsoft.com/partner/home). När Azure IoT Central program skapas åt kunder med CSP: er, precis som med andra CSP hanterade Azure-tjänster, CSP: er hanterar fakturering för kunder. En avgift för Azure IoT Central visas din totala faktura i Microsoft Partner Center.

För att komma igång, logga in på ditt konto på Microsoft Partner-portalen och välj en kund som du vill skapa ett program i Azure IoT Central. Gå till Service management för kunden från det vänstra navigeringsfältet.

![Microsoft Partner Center, anpassad vy](media\howto-create-application-asCSP\image1.png)

Azure IoT Central anges som en tjänst som är tillgängliga för att administrera. Klicka på länken för Azure IoT Central på sidan för att skapa nya program eller hantera befintliga program för den här kunden.

![Azure IoT Central som är tillgängliga för att hantera](media\howto-create-application-asCSP\image2.png)

Du hamnar på sidan Programhanterare för Azure IoT Central. Azure IoT Central behåller kontext att du kommer från Microsoft Partner Center och att du kommer att hantera just den kunden. Du ser det här erkända i rubriken på sidan Programhanterare. Härifrån kan du antingen navigera till ett befintligt program som du skapade tidigare för kunden att hantera den eller skapa ett nytt program för kunden.

![Skapa Manager för CSP: er](media\howto-create-application-asCSP\image3.png)

Om du vill skapa ett Azure IoT Central program klickar du på den **nytt program** panelen. Detta kommer att läsa in sidan Skapa program. Du måste fylla i alla fält på den här sidan och välj sedan **skapa**. Du hittar mer information om vart och ett av fälten nedan.

![Skapa program sida för CSP: er](media\howto-create-application-asCSP\image4-1.png)

![Skapa program sida för CSP: er](media\howto-create-application-asCSP\image4-2.png)

## <a name="payment-plan"></a>Betalningsplan

Du kan skapa bara skapa betalprogram som en Kryptografiprovider. Om du vill visa Azure IoT Central till kunden, kan du skapa en utvärderingsprogram separat. Läs mer om testversion och betald program på den [Azure IoT Central prissättningssidan](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Programnamn

Namnet på ditt program visas på den **Programhanterare** sidan och inom varje Azure IoT Central program. Du kan välja ett namn för ditt program med Azure IoT Central. Välj ett namn som passar dig och till andra i din organisation.

## <a name="application-url"></a>Program-URL

Programmets URL är en länk till ditt program. Du kan spara ett bokmärke till den i webbläsaren eller dela den med andra.

När du anger namnet på programmet, genereras programmets URL automatiskt. Om du vill kan välja du en annan URL för ditt program. Varje Azure IoT Central-URL måste vara unikt i Azure IoT Central. Du ser ett felmeddelande visas om URL: en som du väljer finns redan.

## <a name="directory"></a>Katalog

Eftersom Azure IoT Central har kontext som du kommer att hantera den kund som du valde i Microsoft Partner-portalen, visas bara Azure Active Directory-klient för kunden i fältet Directory. 

En Azure Active Directory-klient innehåller användaridentiteter, autentiseringsuppgifter och annan organisationens information. Flera Azure-prenumerationer kan associeras med en enda Azure Active Directory-klient.

Mer information finns i [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Azure-prenumeration

En Azure-prenumeration kan du skapa instanser av Azure-tjänster. Azure IoT Central automatiskt söker efter alla Azure-prenumerationer till kunden som du har åtkomst och visar dem i en listruta på den **skapa program** sidan. Välj en Azure-prenumeration att skapa ett nytt Azure IoT Central-program.

Om du inte har en Azure-prenumeration kan skapa du en i Microsoft Partner Center. När du skapar Azure-prenumeration kan du gå tillbaka till den **skapa program** sidan. Din nya prenumeration visas i den **Azure-prenumeration** utskriftsjobb.

Mer information finns i [Azure-prenumerationer](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Region

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

Nu när du har lärt dig hur du skapar ett Azure IoT Central program som en Kryptografiprovider, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)