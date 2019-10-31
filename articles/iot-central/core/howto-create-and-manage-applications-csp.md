---
title: Skapa och hantera Azure IoT Central-program från CSP-portalen | Microsoft Docs
description: Som KRYPTOGRAFIPROVIDER skapar du ett Azure IoT Central-program för kundens räkning.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 8e9c2077960dc64aa7bac13a53e0b3f74062aa55
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177271"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Skapa och hantera ett Azure IoT Central-program från CSP-portalen

CSP-programmet (Microsoft Cloud Solution Provider) är ett Microsoft-program för åter försäljare. Avsikten är att tillhandahålla våra kanal partner med ett program som gör att du kan sälja alla Microsofts kommersiella online tjänster. Läs mer om [Cloud Solution Provider-programmet](https://partner.microsoft.com/cloud-solution-provider).

Som KRYPTOGRAFIPROVIDER kan du skapa och hantera Microsoft Azure IoT Central program för dina kunders räkning via [Microsoft Partner Center](https://partnercenter.microsoft.com/partner/home). När Azure IoT Central-program skapas för kunders räkning med kryptografiproviders, precis som med andra CSP-hanterade Azure-tjänster, hanterar CSP fakturering för kunder. En avgift för Azure IoT Central kommer att visas i din totala faktura i Microsoft Partner Center.

Kom igång genom att logga in på ditt konto på Microsoft Partner Portal och välj en kund för vilken du vill skapa ett Azure IoT Central-program. Navigera till Service Management för kunden från det vänstra navigerings fältet.

![Microsoft Partner Center, kund visning](media/howto-create-application-csp/image1.png)

Azure IoT Central listas som en tjänst som är tillgänglig för administration. Välj Azure IoT Central-länken på sidan för att skapa nya program eller hantera befintliga program för den här kunden.

![Azure-IoT Central tillgängligt för hantering](media/howto-create-application-csp/image2.png)

Du land på sidan för Azure IoT Central Application Manager. Azure IoT Central håller kontexten som du kom från Microsoft Partner Center och som du kom att hantera den specifika kunden. Du ser det här bekräftelsen i rubriken på program hanterings sidan. Härifrån kan du antingen navigera till ett befintligt program som du har skapat tidigare för den här kunden för att hantera eller skapa ett nytt program för kunden.

![Skapa hanterare för kryptografiproviders](media/howto-create-application-csp/image3.png)

Om du vill skapa ett Azure IoT Central-program väljer du **skapa** på den vänstra menyn. Välj en av idustry-mallarna eller Välj **anpassad app** för att skapa ett program från grunden. Då öppnas sidan Skapa program. Du måste fylla i alla fält på den här sidan och sedan välja **skapa**. Du hittar mer information om vart och ett av fälten nedan.

![Skapa program sida för kryptografiproviders](media/howto-create-application-csp/image4.png)

![Skapa program sida för kryptografiproviders](media/howto-create-application-csp/image4-1.png)

## <a name="payment-plan"></a>Betalnings plan

Du kan bara skapa "betala per användning"-program som en CSP. För att presentera Azure-IoT Central till kunden kan du skapa ett utvärderings program separat. Läs mer om utvärderings versioner och program som du betalar per användning på [sidan med priser för Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Programnamn

Namnet på ditt program visas på **program hanterings** sidan och i varje Azure IoT Central-program. Du kan välja ett namn för ditt Azure IoT Central-program. Välj ett namn som passar dig och till andra i din organisation.

## <a name="application-url"></a>Program-URL

Programmets URL är länken till ditt program. Du kan spara ett bok märke i webbläsaren eller dela det med andra.

När du anger namnet på programmet genereras din program-URL automatiskt. Om du vill kan du välja en annan URL för ditt program. Varje Azure IoT Central-URL måste vara unik i Azure IoT Central. Ett fel meddelande visas om den URL du väljer redan har gjorts.

## <a name="directory"></a>Katalog

Eftersom Azure IoT Central har sammanhang som du har för att hantera kunden som du valde i Microsoft Partner Portal, ser du bara Azure Active Directory klienten för kunden i fältet katalog. 

En Azure Active Directory klient organisation innehåller användar identiteter, autentiseringsuppgifter och annan organisatorisk information. Flera Azure-prenumerationer kan associeras med en enda Azure Active Directory-klient.

Läs mer i [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Azure-prenumeration

Med en Azure-prenumeration kan du skapa instanser av Azure-tjänster. Azure IoT Central hittar automatiskt alla Azure-prenumerationer för kunden som du har åtkomst till och visar dem i en listruta på sidan **skapa program** . Välj en Azure-prenumeration för att skapa ett nytt Azure IoT Central-program.

Om du inte har någon Azure-prenumeration kan du skapa en i Microsoft Partner Center. När du har skapat Azure-prenumerationen kan du gå tillbaka till sidan **Skapa program**. Din nya prenumeration visas i listrutan **Azure-prenumeration**.

Mer information finns i [Azure-prenumerationer](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Region

Välj den region eller det [geografiskt](https://azure.microsoft.com/global-infrastructure/geographies/) område där du vill skapa ett Azure IoT Central-program. Normalt bör du välja den region som är närmast fysiskt till dina enheter för att få optimala prestanda.

> [!NOTE]
> För **hands versions** mal len är för närvarande endast tillgänglig i regionerna **Nord Europa** och **centrala USA** .

Mer information finns i [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/) och [Azure-geografiska](https://azure.microsoft.com/global-infrastructure/geographies/)områden.

Du kan se de regioner där Azure IoT Central är tillgängligt på sidan [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central) .

> [!Note]
> När du väljer en region kan du inte flytta ditt program till en annan region senare.

## <a name="application-template"></a>Programmall

Du kan välja en av de tillgängliga Programmallarna för ditt nya Azure IoT Central-program. En programmall kan innehålla fördefinierade objekt, till exempel mallar för enheten och instrumentpaneler som hjälper dig att komma igång.

| Programmall | Beskrivning |
| -------------------- | ----------- |
| Anpassat program   | Skapar ett tomt program som du kan fylla med dina egna enhetsmallar och enheter. |
| Exemplet Contoso       | Skapar ett program som innehåller en enhets mall för en enkel ansluten enhet. Använd den här mallen för att börja utforska Azure IoT Central. |
| Exemplet Devkits       | Skapar ett program med enhetsmallar där du kan ansluta en MXChip- eller Raspberry Pi-enhet. Använd den här mallen om du är en enhets utvecklare som experimenterar med kod på någon av dessa enheter. |

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar ett Azure IoT Central-program som en CSP, är det här det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)
