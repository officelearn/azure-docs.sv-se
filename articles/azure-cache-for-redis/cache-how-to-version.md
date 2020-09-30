---
title: Ange Redis-version för Azure cache för Redis (för hands version)
description: Lär dig hur du konfigurerar Redis-versionen
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: ed0f486afe466d31388fa99b4ce5f5754210533f
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91571507"
---
# <a name="set-redis-version-for-azure-cache-for-redis-preview"></a>Ange Redis-version för Azure cache för Redis (för hands version)
I den här artikeln får du lära dig hur du konfigurerar Redis-programvaran som ska användas med din cache-instans. Azure cache för Redis erbjuder den senaste huvud versionen av Redis och minst en tidigare version. De här versionerna uppdateras regelbundet när nyare Redis-program lanseras. Du kan välja mellan de två tillgängliga versionerna. Tänk på att din cache kommer att uppgraderas till nästa version automatiskt om den version som den använder för närvarande inte längre stöds.

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
   
1. På sidan **grundläggande** inställningar konfigurerar du inställningarna för det nya cacheminnet.
   
    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Prenumeration** | Välj din prenumeration. | Den prenumeration som du vill skapa den här nya Azure-cache för Redis-instansen för. | 
    | **Resursgrupp** | Välj en resurs grupp eller Välj **Skapa ny** och ange ett nytt resurs grupp namn. | Namnet på resurs gruppen där du vill skapa cachen och andra resurser. Genom att lägga till alla dina app-resurser i en resurs grupp kan du enkelt hantera eller ta bort dem tillsammans. | 
    | **DNS-namn** | Ange ett globalt unikt namn. | Cache-namnet måste vara en sträng mellan 1 och 63 tecken som bara innehåller siffror, bokstäver eller bindestreck. Namnet måste börja och sluta med en siffra eller en bokstav och får inte innehålla flera bindestreck i rad. Din cacheposts *värdnamn* är * \<DNS name> . Redis.cache.Windows.net*. | 
    | **Plats** | Välj en plats. | Välj en [region](https://azure.microsoft.com/regions/) nära andra tjänster som ska använda din cache. |
    | **Cachestorlek** | Välj en [cache-nivå och-storlek](https://azure.microsoft.com/pricing/details/cache/). |  Prisnivån avgör storlek, prestanda och funktioner som är tillgängliga för cacheminnet. Mer information finns i [Översikt över Azure Cache for Redis](cache-overview.md). |
   
1. På sidan **Avancerat** väljer du en Redis-version som du vill använda.
   
    :::image type="content" source="media/cache-how-to-version/select-redis-version.png" alt-text="Välj Azure-cache för Redis.":::

1. Klicka på **Skapa**. 
   
    Det tar en stund innan cacheminnet skulle skapas. Du kan övervaka förloppet på **översikts** sidan för Azure-cache för Redis. När **statusen** är **igång**är cacheminnet redo att användas.

    > [!NOTE]
    > För närvarande går det inte att ändra Redis-versionen när en cache har skapats.
    >

## <a name="next-steps"></a>Efterföljande moment
Läs mer om Azure cache för Redis-funktioner.

> [!div class="nextstepaction"]
> [Azure cache för Redis Premium service-nivåer](cache-overview.md#service-tiers)
