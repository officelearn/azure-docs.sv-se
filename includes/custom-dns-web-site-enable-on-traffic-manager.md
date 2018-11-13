---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: 2e6697b07f305a78ef4cc3f462c76804160a9d12
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572807"
---
När posterna för domännamnet har distribuerats, bör du kunna använda webbläsaren för att verifiera att ditt domännamn kan användas för att få åtkomst till din webbapp i Azure App Service.

> [!NOTE]
> Det kan ta lite tid för din CNAME ska spridas genom DNS-systemet. Du kan använda en tjänst som <a href="http://www.digwebinterface.com/"> http://www.digwebinterface.com/ </a> att verifiera att CNAME är tillgänglig.
> 
> 

Om du inte redan har lagt webbappen som en Traffic Manager-slutpunkt måste du göra detta innan namnmatchning fungerar som de anpassade domänen namn vägarna till Traffic Manager. Traffic Manager dirigerar sedan till din webbapp. Använd informationen i [Lägg till eller ta bort slutpunkter](../articles/traffic-manager/traffic-manager-endpoints.md) lägger du till din webbapp som en slutpunkt i Traffic Manager-profilen.

> [!NOTE]
> Om din webbapp inte visas när du lägger till en slutpunkt, kontrollerar du att den är konfigurerad för **Standard** läge för App Service-plan. Du måste använda **Standard** läge för webbappen för att fungera med Traffic Manager.
> 
> 

1. I webbläsaren, öppna den [Azure-portalen](https://portal.azure.com).
2. I den **Webbappar** klickar du på namnet på din webbapp, Välj **inställningar**, och välj sedan **anpassade domäner**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. I den **anpassade domäner** bladet klickar du på **Lägg till värddatornamn**.
4. Använd den **värdnamn** textrutor för att ange domännamnet för Traffic Manager ska associeras med det här webbprogrammet.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. Klicka på **verifiera** att spara konfigurationen av domain name.
6. När du klickar på **verifiera** Azure startar domänverifiering arbetsflöde. Detta kommer att kontrollera för domänägarskap samt värdnamn tillgänglighet och rapporten slutfört eller detaljerade fel med vägledning om hur du åtgärdar felet.    
7. Vid lyckad validering **Lägg till värddatornamn** knappen blir aktiv och du kommer att kunna tilldela värdnamnet. Navigera till ditt eget domännamn i en webbläsare. Du bör nu se appen som körs med hjälp av ditt anpassade domännamn. 
   
   När konfigurationen är klar visas det anpassade domännamnet i den **domännamn** delen av din webbapp.

Du bör nu att kunna ange domännamnet för Traffic Manager i din webbläsare och se att det har tar dig till din webbapp.

