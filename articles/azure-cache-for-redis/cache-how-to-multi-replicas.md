---
title: Lägga till repliker i Azure cache för Redis (för hands version)
description: Lär dig hur du lägger till fler repliker till din Premium-nivå i Azure-cache för Redis-instanser
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: a747cf8e1713eb905aee02af95c568a448b47f05
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91347025"
---
# <a name="add-replicas-to-azure-cache-for-redis-preview"></a>Lägga till repliker i Azure cache för Redis (för hands version)
I den här artikeln får du lära dig hur du konfigurerar en Azure cache-instans med ytterligare repliker med hjälp av Azure Portal.

Azure cache för Redis standard-och Premium-nivåer erbjuder redundans genom att vara värd för varje cache på två dedikerade virtuella datorer (VM). Dessa virtuella datorer är konfigurerade som primära och repliker. När den primära virtuella datorn blir otillgänglig identifierar repliken och tar över som den nya primära servern automatiskt. Du kan nu öka antalet repliker i en Premium-cache upp till tre, vilket ger dig totalt fyra virtuella datorer som säkerhetskopierar en cache. Att ha flera repliker resulterar i högre återhämtning än vad en enskild replik kan ge.

> [!IMPORTANT]
> Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i kompletterande användnings [villkor för Microsoft Azure för hands versionerna.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 
> 

## <a name="prerequisites"></a>Förutsättningar
* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)

> [!NOTE]
> Den här funktionen är för närvarande i för hands version – [kontakta oss](mailto:azurecache@microsoft.com) om du är intresse rad av.
>

## <a name="create-a-cache"></a>Skapa en cache
Följ dessa steg om du vill skapa en cache:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **Skapa en resurs**.
  
1. Välj **databaser** på sidan **nytt** och välj sedan **Azure cache för Redis**.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Välj Azure-cache för Redis.":::
   
1. På sidan **ny Redis cache** konfigurerar du inställningarna för din nya cache.
   
    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS-namn** | Ange ett globalt unikt namn. | Cache-namnet måste vara en sträng mellan 1 och 63 tecken som bara innehåller siffror, bokstäver eller bindestreck. Namnet måste börja och sluta med en siffra eller en bokstav och får inte innehålla flera bindestreck i rad. Din cacheposts *värdnamn* är * \<DNS name> . Redis.cache.Windows.net*. | 
    | **Prenumeration** | List rutan och välj din prenumeration. | Den prenumeration som du vill skapa den här nya Azure-cache för Redis-instansen för. | 
    | **Resursgrupp** | List rutan och välj en resurs grupp, eller Välj **Skapa ny** och ange ett nytt resurs grupp namn. | Namnet på resurs gruppen där du vill skapa cachen och andra resurser. Genom att lägga till alla dina app-resurser i en resurs grupp kan du enkelt hantera eller ta bort dem tillsammans. | 
    | **Plats** | List rutan och välj en plats. | Välj en [region](https://azure.microsoft.com/regions/) nära andra tjänster som ska använda din cache. |
    | **Prisnivå** | List rutan och välj ett cacheminne för [Premium-nivån](https://azure.microsoft.com/pricing/details/cache/) . |  Prisnivån avgör storlek, prestanda och funktioner som är tillgängliga för cacheminnet. Mer information finns i [Översikt över Azure Cache for Redis](cache-overview.md). |
    | **Antal repliker** | Bild för att välja antalet repliker. | Standardvärdet är 1. |
   
1. När du har valt ett cacheminne för Premium-nivån får du en fråga om du vill aktivera Redis-klustring eller inte. Lämna **klustring** som *inaktiverat*. 
   
    :::image type="content" source="media/cache-how-to-premium-clustering/redis-clustering-disabled.png" alt-text="Konfigurera Redis-kluster.":::

    > [!NOTE]
    > Stöd för flera repliker fungerar för närvarande endast med icke-klustrade cacheminnen.
    >

1. Klicka på **Skapa**. 
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Skapa Azure-cache för Redis.":::
   
    Det tar en stund innan cacheminnet skulle skapas. Du kan övervaka förloppet på **översikts** sidan för Azure-cache för Redis. När **statusen** är **igång**är cacheminnet redo att användas.

    > [!NOTE]
    > Antalet repliker i en cache kan inte ändras efter att det har skapats.
    >

## <a name="next-steps"></a>Efterföljande moment
Läs mer om Azure cache för Redis-funktioner.

> [!div class="nextstepaction"]
> [Azure cache för Redis Premium service-nivåer](cache-overview.md#service-tiers)
