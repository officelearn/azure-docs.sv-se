---
title: ta med fil
description: ta med fil
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 3c064aa8e57a77b96161da06847f543816be1217
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73719109"
---
1. Om du vill skapa en cache loggar du in på [Azure-portalen](https://portal.azure.com) och väljer **Skapa en resurs**. 
   
   ![Välj Skapa en resurs](media/redis-cache-create/create-a-resource.png)
   
1. På den **nya** sidan väljer du **Databaser** och väljer sedan **Azure Cache för Redis**.
   
   ![Välj Azure-cache för Redis](media/redis-cache-create/redis-cache-new-cache-menu.png)
   
1. Konfigurera inställningarna för den nya cachen på sidan **Ny Redis-cache.**
   
   | Inställning      | Föreslaget värde  | Beskrivning |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-namn** | Ange ett globalt unikt namn. | Cachenamnet måste vara en sträng mellan 1 och 63 tecken som bara innehåller siffror, bokstäver eller bindestreck. Namnet måste börja och sluta med ett tal eller en bokstav och får inte innehålla på varandra följande bindestreck. Cacheinstansens *värdnamn* är * \<DNS-namn>.redis.cache.windows.net*. | 
   | **Prenumeration** | Släpp ned och välj din prenumeration. | Prenumerationen som du vill skapa den här nya Azure-cachen för Redis-instansen under. | 
   | **Resursgrupp** | Listruta nedåt och välj en resursgrupp, eller välj **Skapa ny** och ange ett nytt resursgruppsnamn. | Namn på resursgruppen där cacheminnet och andra resurser ska skapas. Genom att placera alla appresurser i en resursgrupp kan du enkelt hantera eller ta bort dem tillsammans. | 
   | **Location** | Släpp ned och välj en plats. | Välj en [region](https://azure.microsoft.com/regions/) nära andra tjänster som ska använda cacheminnet. |
   | **Prisnivå** | Släpp ned och välj en [prisnivå](https://azure.microsoft.com/pricing/details/cache/). |  Prisnivån avgör storlek, prestanda och funktioner som är tillgängliga för cacheminnet. Mer information finns i [Översikt över Azure Cache for Redis](../articles/azure-cache-for-redis/cache-overview.md). |
   
1. Välj **Skapa**. 
   
   ![ Skapa Azure Cache for Redis](media/redis-cache-create/redis-cache-cache-create.png) 
   
   Det tar ett tag innan cachen skapas. Du kan övervaka förloppet på sidan Azure Cache for Redis **Overview.** När **Status** visas som **Kör**är cachen klar att användas.
   
   ![Azure Cache för Redis skapas](media/redis-cache-create/redis-cache-cache-created.png)

