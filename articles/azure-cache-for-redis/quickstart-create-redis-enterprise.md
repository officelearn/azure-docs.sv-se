---
title: 'Snabb start: skapa en cacheminne för företags nivå'
description: I den här snabb starten lär du dig hur du skapar en instans av Azure cache för Redis Enterprise-nivå
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/12/2020
ms.openlocfilehash: 3df6cb1afe8a6249eadbbd3f61619e66be2d2478
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402496"
---
# <a name="quickstart-create-an-enterprise-tier-cache-preview"></a>Snabb start: skapa en cacheminne för företags nivå (för hands version)

Azure cache för Redis "företags nivåer ger fullständigt integrerat och hanterade [Redis Enterprise](https://redislabs.com/redis-enterprise/) på Azure. De är för närvarande tillgängliga som för hands version. Det finns två nya nivåer i den här förhands granskningen:
* Enterprise, som använder flyktigt minne (DRAM) på en virtuell dator för att lagra data
* Enterprise SSD, som använder både flyktigt och icke-flyktigt minne (NVMe) för att lagra data.

Det kostar inget att delta i förhands granskningen. Registrera dig via [Azure Marketplace](https://aka.ms/redispreviewsignup/) **kontakta mig** om du är intresse rad av. Vi har mycket begränsat antal deltagar punkter och kan inte garantera att du kommer att accepteras i för hands versionen.

## <a name="prerequisites"></a>Krav

Du behöver en Azure-prenumeration innan du börjar. Om du inte har ett konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) först.

## <a name="create-a-cache"></a>Skapa en cache
1. Om du vill skapa en cache loggar du in på Azure Portal med hjälp av länken i din förhands gransknings inbjudan och väljer **skapa en resurs**.

   > [!IMPORTANT] 
   > Prenumerera inte på *Azure cache för Redis, företags nivåer* på Marketplace direkt.
   > Det här steget utförs av gränssnittet Azure cache för Redis-portalen automatiskt.
   >
   
1. Välj **databaser** på sidan **nytt** och välj sedan **Azure cache för Redis**.
   
   ![Välj Azure-cache för Redis](media/cache-create/new-cache-menu.png)
   
1. På sidan **ny Redis cache** konfigurerar du inställningarna för din nya cache.
   
   | Inställningen      | Föreslaget värde  | Beskrivning |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Prenumeration** | List rutan och välj din prenumeration. | Den prenumeration som du vill skapa den här nya Azure-cache för Redis-instansen för. | 
   | **Resursgrupp** | List rutan och välj en resurs grupp, eller Välj **Skapa ny** och ange ett nytt resurs grupp namn. | Namnet på resurs gruppen där du vill skapa cachen och andra resurser. Genom att lägga till alla dina app-resurser i en resurs grupp kan du enkelt hantera eller ta bort dem tillsammans. | 
   | **DNS-namn** | Ange ett globalt unikt namn. | Cache-namnet måste vara en sträng mellan 1 och 63 tecken som bara innehåller siffror, bokstäver eller bindestreck. Namnet måste börja och sluta med en siffra eller en bokstav och får inte innehålla flera bindestreck i rad. Den cachelagrade instansens *värdnamn* kommer att vara ett * \< DNS-namn>. <Azure region> . redisenterprise.cache.azure.net*. | 
   | **Position** | List rutan och välj en plats. | Företags nivåer är tillgängliga i västra USA, östra USA 2 och Västeuropa. |
   | **Cache-nivå** | List rutan och välj en *företags-dram* -eller *Enterprise SSD* -nivå och en storlek. |  Nivån avgör storlek, prestanda och funktioner som är tillgängliga för cachen. |
   
   ![Grundläggande information om företags nivå](media/cache-create/enterprise-tier-basics.png) 

1. Välj **Nästa: nätverk** och hoppa över.

   > [!NOTE] 
   > Stöd för privat länk kommer att komma senare.
   >

1. Välj **Nästa: Avancerat**.
   
   Du kan behålla standardinställningarna eller ändra dem efter behov. När du aktiverar **Tillåt endast åtkomst via TLS**måste du använda TLS för att komma åt den nya cachen från ditt program.

   ![Avancerad företags nivå](media/cache-create/enterprise-tier-advanced.png) 

   > [!NOTE] 
   > Redis-moduler stöds inte i Enterprise SSD-nivån ännu. Om du planerar att använda en Redis-modul ser du till att välja en cacheminne för företags nivå.
   >
   
1. Välj **Nästa: Taggar** och hoppa över.

1. Välj **Nästa: Sammanfattning**.

   ![Sammanfattning för företags nivå](media/cache-create/enterprise-tier-summary.png) 

1. Klicka på kryss rutan under **villkor**, granska inställningarna och välj sedan **Granska + skapa**.
   
   Det tar lite tid för cacheminnet att skapas. Du kan övervaka förloppet på **översikts** sidan för Azure-cache för Redis. När **statusen** är **igång**är cacheminnet redo att användas.

   > [!NOTE] 
   > En stund efter att cacheminnet för företags nivån har skapats får du en **åtgärd som kräver** e-post från Azure Marketplace för att konfigurera *Azure cache för Redis, företags nivåer*. Den här åtgärden behövs inte. Du kan bortse från e-postmeddelandet.
   >

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en Azure-instans av Azure cache för Redis.

> [!div class="nextstepaction"]
> [Skapa en ASP.NET-webbapp som använder en Azure Cache for Redis.](./cache-web-app-howto.md)

