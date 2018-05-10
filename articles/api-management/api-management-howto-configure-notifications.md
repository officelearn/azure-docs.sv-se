---
title: Konfigurera aviseringar och e-mallar i Azure API Management | Microsoft Docs
description: Lär dig mer om att konfigurera aviseringar och e-mallar i Azure API Management.
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
ms.openlocfilehash: 60788f76dac58ead10e43e892d587a86bdd3fcad
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Konfigurera meddelanden och e-postmallar i Azure API Management
API Management gör möjligheten att konfigurera aviseringar för specifika händelser och konfigurera e-postmallar som används för kommunikation med administratörer och utvecklare av en API Management-instans. Den här artikeln visar hur du konfigurerar aviseringar för händelserna som är tillgängliga och ger en översikt över hur du konfigurerar e postmallar som används för dessa händelser.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en instans för API Management-tjänsten utför du följande Snabbstart: [skapa en instans av Azure API Management](get-started-create-service-instance.md).

## <a name="publisher-notifications"> </a>Konfigurera meddelanden

1. Välj din **API MANAGEMENT** instans.
2. Klicka på **meddelanden** att visa tillgängliga meddelanden.

    ![Publisher meddelanden][api-management-publisher-notifications]

    Följande lista över händelser kan konfigureras för meddelanden.

    * **Prenumerationsbegäranden (som kräver godkännande)** -de angivna e-postmottagare och användare får e-postmeddelanden om prenumerationen förfrågningar för API-produkter som kräver godkännande.
    * **Nya prenumerationer** -de angivna e-postmottagare och användare får e-postmeddelanden om nya prenumerationer för API-produkten.
    * **Galleriet programförfrågningar** -de angivna e-postmottagare och användare får e-postmeddelanden när nya program skickas till galleriet för programmet.
    * **Hemlig kopia** -de angivna e-postmottagare och användare får e-post hemliga kopior av alla e-postmeddelanden skickas till utvecklare.
    * **Nytt ärende eller kommentar** - de angivna e-postmottagare och användare får e-postmeddelanden när ett nytt ärende eller kommentar skickas på developer-portalen.
    * **Avsluta konto meddelandet** -de angivna e-postmottagare och användare får e-postmeddelanden när ett konto har stängts.
    * **Närmar sig kvotgränsen för prenumerationen** -följande e-postmottagare och användare får e-postaviseringar när prenumerationsanvändning hämtar nära kvot.

    Du kan ange e-postmottagare med textrutan e-postadress för varje händelse eller kan du välja användare från en lista.

3. Ange e-postadresser som ska meddelas ange dem i textrutan e-postadress. Om du har flera e-postadresser avgränsade med kommatecken.

    ![Meddelandemottagare][api-management-email-addresses]
4. Tryck på **Lägg till**.

## <a name="email-templates"> </a>Konfigurera aviseringsmallar
API Management ger aviseringsmallar för e-postmeddelanden som skickas under administration och med hjälp av tjänsten. Följande e-mallar finns.

* Application gallery skicka godkända
* Utvecklare farewell bokstav
* Kvot för utvecklare som närmar sig meddelande
* Bjud in användare
* Ny kommentar som läggs till ett problem
* Nya problemet som tagits emot
* Ny prenumeration som aktiverats
* Prenumerationen förnyas bekräftelse
* Prenumerationsbegäran avböjer
* Prenumerationsbegäran mottagen

Dessa mallar kan ändras enligt önskemål.

Om du vill visa och konfigurera e-mallar för API Management-instans, klickar du på **meddelanden mallar**.

![E-postmallar][api-management-email-templates]

Varje e-postmall har ett ämne med oformaterad text och en brödtext definition i HTML-format. Varje objekt kan anpassas enligt önskemål.

![Redigeraren för mallen för e-post][api-management-email-template]

Den **parametrar** listan innehåller en lista över parametrar, som under infogas i ämnet eller texten, kommer att ersätta det avsedda värdet när e-postmeddelandet skickas. Om du vill infoga en parameter, placera markören där du vill att parametern för att gå och klickar du på pilen till vänster om parameternamnet.

> [!NOTE] 
> Parametrarna är inte ersättas med verkliga värden när Förhandsgranska eller skickar ett test.

Om du vill spara ändringarna till e-postmall klickar du på **spara**, eller om du vill avbryta ändringarna Klicka **Ignorera**.
 

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
