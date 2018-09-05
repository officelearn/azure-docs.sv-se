---
title: ta med fil
description: ta med fil
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 07/03/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 377ea6629223c365ef3d00c756929bf23ddffde5
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702655"
---
För att komma igång med Service Bus-meddelandeentiteter i Azure måste du först skapa ett namnområde med ett namn som är unikt i Azure. Ett namnområde innehåller en omfattningscontainer för adressering av Service Bus-resurser i ditt program.

Så här skapar du ett namnområde:

1. Logga in på [Azure Portal][Azure portal].
2. I det vänstra navigeringsfältet i portalen klickar du på **+ Skapa en resurs**, därefter klickar du på **Integration** och sedan på **Service Bus**.
3. I dialogrutan **Skapa namnområde** anger du ett namn för namnområdet. Systemet kontrollerar omedelbart om namnet är tillgängligt.
4. När du har kontrollerat att namnet för namnområdet är tillgängligt, väljer du prisnivå (Basic, Standard eller Premium). Om du vill använda [ämnen och prenumerationer](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) ska du välja antingen Standard eller Premium. Ämnen/prenumerationer stöds inte på Basic-prisnivån.
5. I fältet **Prenumeration** väljer du en Azure-prenumeration för vilken du vill skapa namnområdet.
6. I fältet **Resursgrupp** väljer du en befintlig resursgrupp där namnområdet ska finnas eller skapar en ny.      
7. I **Plats** väljer du land eller region där namnområdet ska finnas.
   
    ![Skapa namnområde][create-namespace]
8. Klicka på **Skapa**. Systemet skapar namnområdet och aktiverar det. Du kan behöva vänta några minuter medan systemet tilldelar resurser till ditt konto.

### <a name="obtain-the-management-credentials"></a>Hämta autentiseringsuppgifterna för hantering

Om du skapar ett nytt namnområde så genereras en automatiskt en ursprunglig regel för signatur för delad åtkomst (SAS) med ett kopplat par med primära och sekundära nycklar som ger fullständig kontroll över alla namnområdets aspekter. Se [Service Bus – autentisering och auktorisering](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) för mer information om hur du skapar ytterligare regler med mer begränsade rättigheter för regelbundna sändare och mottagare. Gör så här om du vill kopiera den ursprungliga regeln: 

1. Klicka på **Alla resurser**, sedan klickar du på den nyligen skapade namnrymden.
2. I namnområdesfönstret, klickar du på **Principer för delad åtkomst**.
3. I fönstret **Principer för delad åtkomst**, klickar du på **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. I fönstret **Princip: RootManageSharedAccessKey** klickar du på knappen Kopiera bredvid **Primär anslutningssträng** för att kopiera anslutningssträngen till dina urklipp för senare användning. Klistra in det här värdet i Anteckningar eller på en tillfällig plats.
   
    ![connection-string][connection-string]

5. Upprepa föregående steg, kopiera och klistra in värdet för **primärnyckeln** till en tillfällig plats för senare användning.

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
