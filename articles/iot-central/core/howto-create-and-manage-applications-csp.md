---
title: Skapa och hantera Azure IoT Central-program från CSP-portalen | Microsoft-dokument
description: Som en CSP, hur man skapar ett Azure IoT Central-program på uppdrag av din kund.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 02481d5dcbaba15c9b17a27348207d9af64f3355
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982046"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Skapa och hantera ett Azure IoT Central-program från CSP-portalen

Programmet Microsoft Cloud Solution Provider (CSP) är ett Microsoft-återförsäljarprogram. Avsikten är att förse våra kanalpartner med ett enda program för att sälja alla Microsoft Commercial Online Services vidare. Läs mer om [Cloud Solution Provider-programmet](https://partner.microsoft.com/cloud-solution-provider).

Som CSP kan du skapa och hantera Microsoft Azure IoT Central-program för dina kunders räkning via [Microsoft Partner Center](https://partnercenter.microsoft.com/partner/home). När Azure IoT Central-program skapas för kunders räkning av CSP:er, precis som med andra CSP-hanterade Azure-tjänster, hanterar CSP fakturering för kunder. En avgift för Azure IoT Central visas i din totala faktura i Microsoft Partner Center.

För att komma igång loggar du in på ditt konto på Microsoft Partner Portal och väljer en kund som du vill skapa ett Azure IoT Central-program för. Navigera till Service Management för kunden från den vänstra navigeringsskeppet.

![Microsoft Partner Center, kundvy](media/howto-create-and-manage-applications-csp/image1.png)

Azure IoT Central visas som en tjänst som är tillgänglig för administrering. Välj länken Azure IoT Central på sidan för att skapa nya program eller hantera befintliga program för den här kunden.

![Azure IoT Central tillgänglig för hantering](media/howto-create-and-manage-applications-csp/image2.png)

Du landar på sidan Azure IoT Central Application Manager. Azure IoT Central behåller kontexten som du kom från Microsoft Partner Center och att du kom för att hantera just den kunden. Detta visas i sidhuvudet på sidan Programhanteraren. Härifrån kan du antingen navigera till ett befintligt program som du hade skapat tidigare för den här kunden att hantera eller skapa ett nytt program för kunden.

![Skapa hanteraren för CSP:er](media/howto-create-and-manage-applications-csp/image3.png)

Om du vill skapa ett Azure IoT Central-program väljer du **Skapa** i den vänstra menyn. Välj en av branschmallarna eller välj **Anpassad app** för att skapa ett program från grunden. Då läses sidan Programskapande in. Du måste fylla i alla fält på den här sidan och sedan välja **Skapa**. Du hittar mer information om vart och ett av fälten nedan.

![Skapa programsida för CSP:er](media/howto-create-and-manage-applications-csp/image4.png)

![Skapa programsida för CSP:er](media/howto-create-and-manage-applications-csp/image4-1.png)

![Skapa programsida för faktureringsinformation för CSP-adresser](media/howto-create-and-manage-applications-csp/image4-2.png)

## <a name="pricing-plan"></a>Prisplan

Du kan bara skapa program som använder en standardprisplan som en CSP. Om du vill visa upp Azure IoT Central för kunden kan du skapa ett program som använder den kostnadsfria prisplanen separat. Läs mer om de kostnadsfria och standardiserade prisplanerna på [prissidan för Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Du kan bara skapa program som använder en standardprisplan som en CSP. Om du vill visa upp Azure IoT Central för kunden kan du skapa ett program som använder den kostnadsfria prisplanen separat. Läs mer om de kostnadsfria och standardiserade prisplanerna på [prissidan för Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Programnamn

Namnet på ditt program visas på **sidan Programhanteraren** och i varje Azure IoT Central-program. Du kan välja vilket namn som helst för ditt Azure IoT Central-program. Välj ett namn som passar dig och andra i organisationen.

## <a name="application-url"></a>Program-URL

Programmets URL är länken till ditt program. Du kan spara ett bokmärke i det i din webbläsare eller dela det med andra.

När du anger namnet på programmet skapas program-URL:en automatiskt. Om du vill kan du välja en annan webbadress för ditt program. Varje Azure IoT Central URL måste vara unik i Azure IoT Central. Ett felmeddelande visas om webbadressen du väljer redan har tagits.

## <a name="directory"></a>Katalog

Eftersom Azure IoT Central har kontext som du kom för att hantera kunden du valde i Microsoft Partner Portal, ser du bara Azure Active Directory-klienten för den kunden i fältet Katalog. 

En Azure Active Directory-klient innehåller användaridentiteter, autentiseringsuppgifter och annan organisationsinformation. Flera Azure-prenumerationer kan associeras med en enda Azure Active Directory-klientorganisation.

Mer information finns i [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Azure-prenumeration

Med en Azure-prenumeration kan du skapa instanser av Azure-tjänster. Azure IoT Central söker automatiskt alla Azure-prenumerationer för kunden som du har åtkomst till och visar dem i en listruta på sidan **Skapa program.** Välj en Azure-prenumeration för att skapa ett nytt Centralprogram för Azure IoT.

Om du inte har en Azure-prenumeration kan du skapa en i Microsoft Partner Center. När du har skapat Azure-prenumerationen kan du gå tillbaka till sidan **Skapa program**. Din nya prenumeration visas i listrutan **Azure-prenumeration**.

Mer information finns i [Azure-prenumerationer](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="location"></a>Location

**Plats** är den [geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill skapa programmet. Vanligtvis bör du välja den plats som är fysiskt närmast dina enheter för att få optimal prestanda. För närvarande kan du skapa en IoT Central ansökan i **Australien,** **Asien och Stillahavsområdet**, **Europa**, **USA,** **Storbritannien**och **Japan** geografiska områden. När du har valt en plats kan du inte flytta programmet till en annan plats senare.

## <a name="application-template"></a>Programmall

Välj den programmall som du vill använda för ditt program.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar ett Azure IoT Central-program som en CSP, här är det föreslagna nästa steget:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)
