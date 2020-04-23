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
2. Välj **+ ny** > **webb och mobilt** > **mobilapp**och ange sedan ett namn för Mobile Apps Server delen.
3. För **resurs grupp**väljer du en befintlig resurs grupp eller skapar en ny (med hjälp av samma namn som din app). 
4. För **App Service plan**är standard planen (på [Standard nivån](https://azure.microsoft.com/pricing/details/app-service/)) vald. Du kan också välja en annan plan eller [skapa en ny](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   Inställningarna för App Service plan avgör [plats, funktioner, kostnad och beräknings resurser](https://azure.microsoft.com/pricing/details/app-service/) som är associerade med din app. Mer information om App Service-planer och hur du skapar en ny plan på en annan pris nivå och på önskad plats finns [i Översikt över Azure App Service planer i djupet](../articles/app-service/overview-hosting-plans.md).
   
5. Välj **Skapa**. I det här steget skapas Mobile Apps Server delen. 
6. I fönstret **Inställningar** för den nya Mobile Apps Server delen väljer du **snabb start** > din klient program plattform > **ansluter en databas**. 
   
   ![Alternativ för att ansluta en databas](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. I fönstret **Lägg till data anslutning** väljer du **SQL Database** > **skapa en ny databas**. Ange databasens namn, Välj en pris nivå och välj sedan **Server**. Du kan återanvända den nya databasen. Om du redan har en databas på samma plats kan du istället välja **Använd en befintlig databas**. Vi rekommenderar inte att du använder en databas på en annan plats, på grund av bandbredds kostnader och högre latens.
   
   ![Välja en databas](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. I fönstret **ny server** anger du ett unikt server namn i rutan **Server namn** , anger ett inloggnings namn och lösen ord, väljer **Tillåt att Azure-tjänster får åtkomst till servern**och väljer **OK**. Det här steget skapar den nya databasen.
9. Tillbaka i fönstret **Lägg till data anslutning** väljer du **anslutnings sträng**, anger inloggnings-och lösen ords värden för din databas och väljer **OK**. 

   Vänta några minuter tills databasen har distribuerats innan du fortsätter.

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/
