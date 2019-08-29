---
title: Konfigurera meddelanden och e-postmallar i Azure API Management | Microsoft Docs
description: Lär dig hur du konfigurerar meddelanden och e-postmallar i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/02/2018
ms.author: apimpm
ms.openlocfilehash: d49e861a529f3b2265f65c53cc0ee2f6feb151da
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072484"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Konfigurera meddelanden och e-postmallar i Azure API Management
API Management ger möjlighet att konfigurera meddelanden för vissa händelser och konfigurera e-postmallarna som används för att kommunicera med administratörer och utvecklare av en API Management instans. Den här artikeln visar hur du konfigurerar meddelanden för de tillgängliga händelserna och ger en översikt över hur du konfigurerar e-postmallarna som används för dessa händelser.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en API Management tjänst instans slutför du följande snabb start: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="publisher-notifications"> </a>Konfigurera meddelanden

1. Välj din **API Management** -instans.
2. Klicka på aviseringar för att visa tillgängliga meddelanden.

    ![Utgivar meddelanden][api-management-publisher-notifications]

    Följande lista över händelser kan konfigureras för meddelanden.

   * **Prenumerations begär Anden (kräver godkännande)** – angivna e-postmottagare och användare får e-postaviseringar om prenumerations begär Anden för API-produkter som kräver godkännande.
   * **Nya prenumerationer** – angivna e-postmottagare och användare får e-postaviseringar om nya API-produkt prenumerationer.
   * **Program Galleri förfrågningar** – angivna e-postmottagare och användare får e-postaviseringar när nya program skickas till program galleriet.
   * **Hemlig kopia** – angivna e-postmottagare och användare får e-postkopior av alla e-postmeddelanden som skickas till utvecklare.
   * **Nytt ärende eller kommentar** – angivna e-postmottagare och användare får e-postaviseringar när ett nytt ärende eller en ny kommentar skickas på Developer-portalen.
   * **Stäng konto meddelande** – angivna e-postmottagare och användare får e-postaviseringar när ett konto stängs.
   * **Närmar sig prenumerations kvot gränsen** – följande e-postmottagare och användare får e-postaviseringar när prenumerations användningen är nära användnings kvoten.

     För varje händelse kan du ange e-postmottagare med hjälp av text rutan e-postadress, eller så kan du välja användare i en lista.

3. Ange de e-postadresser som ska meddelas genom att ange dem i text rutan e-postadress. Om du har flera e-postadresser avgränsar du dem med kommatecken.

    ![Meddelande mottagare][api-management-email-addresses]
4. Tryck på **Lägg till**.

## <a name="email-templates"> </a>Konfigurera aviserings mallar
API Management innehåller aviserings mallar för e-postmeddelanden som skickas under Administration och användning av tjänsten. Följande e-postmallar har angetts.

* Program galleris överföring godkänd
* Farewell-brev för utvecklare
* Meddelande om kvot gräns för utvecklare
* Bjud in användare
* Ny kommentar har lagts till i ett ärende
* Nytt problem har tagits emot
* Ny prenumeration har Aktiver ATS
* Bekräftelse av förnyad prenumeration
* Begäran om prenumeration nekas
* Begäran om prenumeration togs emot

Dessa mallar kan ändras efter behov.

Klicka på **mallar för meddelanden**om du vill visa och konfigurera e-postmallarna för din API Management instans.

![E-postmallar][api-management-email-templates]

Varje e-postmall har ett ämne i oformaterad text och en brödtext definition i HTML-format. Varje objekt kan anpassas efter behov.

![Redigeraren för e-postmall][api-management-email-template]

**Parameter** listan innehåller en lista över parametrar, som när de infogas i ämnet eller bröd texten, kommer att ersättas med det angivna värdet när e-postmeddelandet skickas. Om du vill infoga en parameter placerar du markören där du vill att parametern ska gå och klickar på pilen till vänster om parameter namnet.

> [!NOTE] 
> Parametrarna ersätts inte med faktiska värden vid för hands visning eller sändning av ett test.

Spara ändringarna i e-postmallen genom att klicka på **Spara**eller avbryta ändringarna genom att klicka på **Ignorera**.
 

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
