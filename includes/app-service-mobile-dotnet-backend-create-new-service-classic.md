---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: e087a1db008422aeec8fd4e073a7476eebe4d54b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67188025"
---
1. Logga in på [Azure Portal].
2. Välj **+NY** > **webb +** > **mobilapp**och ange sedan ett namn för din mobilappars baksida.
3. För **Resursgrupp**väljer du en befintlig resursgrupp eller skapar en ny (med samma namn som appen). 
4. För **App Service-abonnemang**väljs standardplanen (på [standardnivån).](https://azure.microsoft.com/pricing/details/app-service/) Du kan också välja en annan plan eller [skapa en ny](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   Inställningarna för App Service-planen avgör vilken [plats, vilka funktioner, kostnader och beräkningsresurser](https://azure.microsoft.com/pricing/details/app-service/) som är kopplade till appen. Mer information om App Service-planer och hur du skapar en ny plan på en annan prisnivå och på önskad plats finns [i Azure App Service-planer på djupet](../articles/app-service/overview-hosting-plans.md).
   
5. Välj **Skapa**. Det här steget skapar den bakre delen av mobilapparna. 
6. I fönstret **Inställningar** för den nya backdelen för mobilappar väljer du **Snabbstart** > klientappplattformen > **Anslut en databas**. 
   
   ![Val för anslutning av en databas](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. Välj **SQL Database** > **Skapa en ny databas**i fönstret Lägg till **dataanslutning** . Ange databasnamnet, välj en prisnivå och välj sedan **Server**. Du kan återanvända den nya databasen. Om du redan har en databas på samma plats kan du istället välja **Använd en befintlig databas**. Vi rekommenderar inte användning av en databas på en annan plats, på grund av bandbreddskostnader och högre svarstid.
   
   ![Välja en databas](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. I fönstret **Nytt server** anger du ett unikt servernamn i rutan **Servernamn,** anger en inloggning och ett lösenord, väljer **Tillåt Azure-tjänster att komma åt servern**och välj **OK**. Det här steget skapar den nya databasen.
9. Tillbaka i **fönstret Lägg till dataanslutning** väljer du **Anslutningssträng,** anger inloggnings- och lösenordsvärdena för databasen och väljer **OK**. 

   Vänta några minuter innan databasen har distribuerats innan du fortsätter.

<!-- URLs. -->
[Azure-portal]: https://portal.azure.com/
