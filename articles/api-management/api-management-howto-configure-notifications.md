---
title: Konfigurera aviseringar och e-mallar i Azure API Management | Microsoft Docs
description: "Lär dig mer om att konfigurera aviseringar och e-mallar i Azure API Management."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: ec560bbab3caf4cde090ed3c9a47ccc0afcb2492
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Konfigurera meddelanden och e-postmallar i Azure API Management
API Management gör möjligheten att konfigurera aviseringar för specifika händelser och konfigurera e-postmallar som används för kommunikation med administratörer och utvecklare av en API Management-instans. Det här avsnittet visar hur du konfigurerar aviseringar för händelserna som är tillgängliga och ger en översikt över hur du konfigurerar e postmallar som används för dessa händelser.

## <a name="publisher-notifications"></a>Konfigurera publisher meddelanden
Om du vill konfigurera meddelanden, klickar du på **Publisher portal** i Azure Portal för API Management-tjänsten. När du gör det kommer du till utgivarportalen för API Management.

![Utgivarportalen][api-management-management-console]

> [!NOTE] 
> Om du inte har skapat en instans för API Management-tjänsten finns [skapa en instans för API Management-tjänsten][Create an API Management service instance].

Klicka på **meddelanden** från den **API Management** menyn till vänster om du vill visa tillgängliga meddelanden.

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

Ange e-postadresser som ska meddelas ange dem i textrutan e-postadress. Om du har flera e-postadresser avgränsade med kommatecken.

![Meddelandemottagare][api-management-email-addresses]

Klicka på Ange användare ska meddelas **Lägg till mottagare**, markera kryssrutan bredvid användarna du vill meddelas och klicka på **OK**.

> [!NOTE] 
> Endast administratörer visas i listan.


När du har konfigurerat meddelandemottagare klickar du på **spara** att tillämpa de uppdaterade meddelandemottagare.

> [!NOTE] 
> Om du navigerar bort från den **Publisher meddelanden** fliken publisher portal varnar dig om det finns osparade ändringar.


## <a name="email-templates"></a>Konfigurera e-mallar
API Management innehåller mallar för e-postmeddelanden som skickas under administration och med hjälp av tjänsten. Följande e-mallar finns.

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

Om du vill visa och konfigurera e-mallar för API Management-instans, klickar du på **meddelanden** från den **API Management** menyn till vänster och välj den **e-postmallar** fliken.

![E-postmallar][api-management-email-templates]

Om du vill visa eller ändra en mall, välja den från den **mallar** listrutan.

![Lista över mallar för e-post][api-management-email-templates-list]

Varje e-postmall har ett ämne med oformaterad text och en brödtext definition i HTML-format. Varje objekt kan anpassas enligt önskemål.

![Redigeraren för mallen för e-post][api-management-email-template]

Den **parametrar** listan innehåller en lista över parametrar, som under infogas i ämnet eller texten, kommer att ersätta det avsedda värdet när e-postmeddelandet skickas. Om du vill infoga en parameter, placera markören där du vill att parametern för att gå och klickar du på pilen till vänster om parameternamnet.

Klicka på **Preview** eller **skicka ett test** att se hur e-postmeddelandet ser ut eller skicka ett test-e-post.

> [!NOTE] 
> Parametrarna är inte ersättas med verkliga värden när Förhandsgranska eller skickar ett test.

Om du vill spara ändringarna till e-postmall klickar du på **spara**, eller om du vill avbryta ändringarna Klicka **Avbryt**.
 

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
