---
title: Konfigurera meddelanden och e-postmallar i Azure API Management | Microsoft Docs
description: Lär dig mer om att konfigurera meddelanden och e-postmallar i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: apimpm
ms.openlocfilehash: 2a959c9d131c6aa0bdc99450cf2b6f09a5d8bfa7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102357"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Konfigurera meddelanden och e-postmallar i Azure API Management
API Management gör möjligheten att konfigurera meddelanden för specifika händelser och för att konfigurera e-postmallar som används för att kommunicera med administratörer och utvecklare av en API Management-instans. Den här artikeln visar hur du konfigurerar aviseringar för de tillgängliga händelserna och ger en översikt över hur du konfigurerar e-mallarna som används för dessa händelser.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en API Management-tjänstinstans, Slutför följande Snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="publisher-notifications"> </a>Konfigurera meddelanden

1. Välj din **API MANAGEMENT** instans.
2. Klicka på **meddelanden** att visa tillgängliga meddelanden.

    ![Publisher-meddelanden][api-management-publisher-notifications]

    Följande lista över händelser kan konfigureras för meddelanden.

   * **Prenumerationsbegäranden (kräva godkännande)** -den angivna e-postmottagare och användarna får ett e-postaviseringar om prenumerationsbegäranden för API-produkter som kräver godkännande.
   * **Nya prenumerationer** -den angivna e-postmottagare och användare får ett e-postmeddelanden om nya prenumerationer för API-produkt.
   * **Galleriet programförfrågningar** -angivna e-postmottagare och användarna får ett e-postmeddelanden när nya program skickas till programgalleriet.
   * **Hemlig kopia** -den angivna e-postmottagare och användare får ett e-hemliga kopior av alla e-postmeddelanden skickas till utvecklare.
   * **Nytt ärende eller kommentar** – den angivna e-postmottagare och användarna får ett e-postmeddelanden när ett nytt ärende eller kommentar har skickats i developer-portalen.
   * **Stäng konto meddelande** -angivna e-postmottagare och användarna får ett e-postmeddelanden när ett konto har avslutats.
   * **Närmar sig kvotgränsen för prenumerationen** -följande e-postmottagare och användarna får ett e-postmeddelanden när prenumerationsanvändning hämtar nära kvot för användning.

     Du kan ange e-postmottagare med textrutan för e-postadress för varje händelse eller du kan välja användare från en lista.

3. Om du vill meddelas e-postadresser, anger du dem i textrutan för e-postadress. Om du har flera e-postadresser kan du avgränsa dem med kommatecken.

    ![Meddelandemottagare][api-management-email-addresses]
4. Tryck på **Lägg till**.

## <a name="email-templates"> </a>Konfigurera aviseringsmallar
API Management ger aviseringsmallar för e-postmeddelanden som skickas under administrationen och med hjälp av tjänsten. Följande e-postmallar tillhandahålls.

* Programmet galleriet bidrag godkänd
* Developer farewell bokstav
* Kvotgränsen för utvecklare närmar sig meddelande
* Bjud in användare
* Ny kommentar har lagts till på ett problem
* Nytt ärende som tagits emot
* Ny prenumeration som aktiverats
* Prenumerationen förnyas bekräftelse
* Prenumerationsbegäran nekar
* Prenumerationsbegäran mottagen

Dessa mallar kan ändras enligt önskemål.

Om du vill visa och konfigurera de e-postmallar för din API Management-instans, klickar du på **meddelanden mallar**.

![E-postmallar][api-management-email-templates]

Varje e-postmallen har ett ämne i oformaterad text och en brödtext definition i HTML-format. Varje objekt som kan anpassas enligt önskemål.

![Redigeraren för e-mall][api-management-email-template]

Den **parametrar** listan innehåller en lista över parametrar som under infogas i ämnet eller texten, kommer att ersättas avsedda värdet när e-postmeddelandet skickas. Placera markören där du vill att parametern för att gå för att infoga en parameter, och klicka på pilen till vänster om parameternamnet.

> [!NOTE] 
> Parametrarna har inte ersatts med faktiska värden när jag förhandsgranskar eller skicka ett test.

Om du vill spara ändringarna till den e-postmallen klickar du på **spara**, eller för att avbryta ändringar Klicka **Ignorera**.
 

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
