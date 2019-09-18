---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: ddeb46a2c7bc7f24f55c22f446926529cee7b598
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059828"
---
När posterna för ditt domän namn har spridits bör du kunna använda webbläsaren för att kontrol lera att ditt anpassade domän namn kan användas för att komma åt din webbapp i Azure App Service.

> [!NOTE]
> Det kan ta lite tid för din CNAME att spridas via DNS-systemet. Du kan använda en tjänst, till <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> exempel för att kontrol lera att CNAME är tillgängligt.
> 
> 

Om du inte redan har lagt till din webbapp som en Traffic Manager slut punkt måste du göra detta innan namn matchningen fungerar, som det anpassade domän namnet dirigeras till Traffic Manager. Traffic Manager dirigerar sedan till din webbapp. Använd informationen i [Lägg till eller ta bort slut punkter](../articles/traffic-manager/traffic-manager-endpoints.md) för att lägga till din webbapp som en slut punkt i Traffic Manager profilen.

> [!NOTE]
> Om din webbapp inte visas när du lägger till en slut punkt, kontrollerar du att den har kon figurer ATS för **Standard** App Service plan läge. Du måste använda **standard** läget för din webbapp för att kunna arbeta med Traffic Manager.
> 
> 

1. Öppna [Azure Portal](https://portal.azure.com)i webbläsaren.
2. På fliken **Web Apps** klickar du på namnet på din webbapp, väljer **Inställningar**och väljer sedan **anpassade domäner**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. I bladet **anpassade domäner** klickar du på **Lägg till värdnamn**.
4. Använd text rutorna **hostname** för att ange det anpassade domän namn som ska associeras med den här webbappen.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. Klicka på **Verifiera** för att spara domän namns konfigurationen.
6. När du klickar på **Verifiera** Azure startas arbets flödet för domän verifiering. Detta kommer att kontrol lera om det finns någon domän ägarskaps domän och om värd namns tillgänglighet och rapporter om lyckade eller detaljerade fel med hjälp av vägledning om hur du åtgärdar felet.    
7. När du har slutfört verifierings knappen för att **lägga till värdnamn** aktive ras den och du kan tilldela värd namnet. Navigera nu till ditt anpassade domän namn i en webbläsare. Nu bör du se din app som körs med ditt anpassade domän namn. 
   
   När konfigurationen är klar visas det anpassade domän namnet i avsnittet **domän namn** i din webbapp.

Nu bör du kunna ange namnet på Traffic Manager domän i webbläsaren och se att det har tagit dig till din webbapp.

