---
title: Konfigurera meddelanden och e-postmallar
titleSuffix: Azure API Management
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
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 786a9e26003a7afb98307e0bd7fae94c42a2f00d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244074"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Konfigurera meddelanden och e-postmallar i Azure API Management

API Management ger möjlighet att konfigurera meddelanden för specifika händelser och att konfigurera e-postmallar som används för att kommunicera med administratörer och utvecklare av en API Management-instans. Den här artikeln visar hur du konfigurerar meddelanden för tillgängliga händelser och ger en översikt över hur du konfigurerar e-postmallar som används för dessa händelser.

## <a name="prerequisites"></a>Krav

Om du inte har en API Management-tjänstinstans slutför du följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-notifications"></a><a name="publisher-notifications"> </a>Konfigurera meddelanden

1.  Välj din **API MANAGEMENT-instans.**
2.  Klicka på **Meddelanden** om du vill visa tillgängliga meddelanden.

    ![Meddelanden om Utgivare][api-management-publisher-notifications]

    Följande lista över händelser kan konfigureras för meddelanden.

    -   **Prenumerationsbegäranden (kräver godkännande)** – De angivna e-postmottagarna och användarna får e-postmeddelanden om prenumerationsbegäranden för API-produkter som kräver godkännande.
    -   **Nya prenumerationer** – De angivna e-postmottagarna och användarna får e-postmeddelanden om nya API-produktprenumerationer.
    -   **Begäran om programgalleri** - De angivna e-postmottagarna och användarna får e-postmeddelanden när nya program skickas till programgalleriet.
    -   **BCC** - De angivna e-postmottagare och användare kommer att få e-post blinda kopior av alla e-postmeddelanden som skickas till utvecklare.
    -   **Nytt ärende eller kommentar** – De angivna e-postmottagarna och användarna får e-postmeddelanden när ett nytt ärende eller en ny kommentar skickas till utvecklarportalen.
    -   **Stäng kontomeddelande** - De angivna e-postmottagarna och användarna får e-postmeddelanden när ett konto stängs.
    -   Närmar sig gränsen för **prenumerationskvot** – Följande e-postmottagare och användare får e-postmeddelanden när prenumerationsanvändningen närmar sig användningskvoten.

        > [!NOTE]
        > Meddelanden utlöses endast av [kvoten av prenumerationsprincipen.](api-management-access-restriction-policies.md#SetUsageQuota) [Kvot efter nyckelprincip](api-management-access-restriction-policies.md#SetUsageQuotaByKey) genererar inte meddelanden.

    För varje händelse kan du ange e-postmottagare med hjälp av textrutan för e-postadress eller välja användare från en lista.

3.  Om du vill ange vilka e-postadresser som ska meddelas anger du dem i textrutan för e-postadressen. Om du har flera e-postadresser separerar du dem med kommatecken.

    ![Mottagare av meddelanden][api-management-email-addresses]

4.  Tryck på **Lägg till**.

## <a name="configure-notification-templates"></a><a name="email-templates"> </a>Konfigurera meddelandemallar

API Management tillhandahåller meddelandemallar för e-postmeddelanden som skickas i samband med att administrera och använda tjänsten. Följande e-postmallar tillhandahålls.

-   Inlämning av programgalleri godkänt
-   Farvälbrev för utvecklare
-   Kvotgräns för utvecklare närmar sig meddelande
-   Bjud in användare
-   Ny kommentar läggs till i ett problem
-   Nyemission har mottagits
-   Ny prenumeration aktiverad
-   Förnyad bekräftelse för prenumeration
-   Avböjning av prenumerationsbegäran
-   Mottagen prenumerationsbegäran

Dessa mallar kan ändras efter behov.

Om du vill visa och konfigurera e-postmallarna för API Management-instansen klickar du på **Meddelandenmallar**.

![E-postmallar][api-management-email-templates]

Varje e-postmall har ett ämne i oformaterad text och en brödtextdefinition i HTML-format. Varje objekt kan anpassas efter önskemål.

![Redigeraren för e-postmall][api-management-email-template]

**Parameterlistan** innehåller en lista över parametrar som när de infogas i ämnet eller brödtexten ersätts det angivna värdet när e-postmeddelandet skickas. Om du vill infoga en parameter placerar du markören där du vill att parametern ska gå och klickar på pilen till vänster om parameternamnet.

> [!NOTE]
> Parametrarna ersätts inte med faktiska värden när du förhandsgranskar eller skickar ett test.

Om du vill spara ändringarna i e-postmallen klickar du på **Spara**eller avbryter ändringarna klickar du på **Ignorera**.

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png
[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png
[configure publisher notifications]: #publisher-notifications
[configure email templates]: #email-templates
[how to create and use groups]: api-management-howto-create-groups.md
[how to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[get started with azure api management]: get-started-create-service-instance.md
[create an api management service instance]: get-started-create-service-instance.md
