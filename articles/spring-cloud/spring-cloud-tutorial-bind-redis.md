---
title: Självstudie – så här binder du Azure cache för Redis till ditt Azure våren Cloud-program
description: Den här självstudien visar hur du binder Azure cache för Redis till ditt Azure våren Cloud-program
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 1653db3619fd569238872ca1fcfd6d0c439e84c9
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708786"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>Självstudie: bind Azure-tjänster till ditt Azure våren Cloud-program: Azure cache för Redis

Med Azure våren Cloud kan du binda utvalda Azure-tjänster till dina program automatiskt, i stället för att manuellt konfigurera ditt våren Boot-program. Den här artikeln visar hur du binder ditt program till Azure cache för Redis.

## <a name="prerequisites"></a>Krav

* En distribuerad Azure våren Cloud-instans
* En Azure-cache för Redis-tjänstinstans
* Azure våren Cloud-tillägg för Azure CLI

Om du inte har en distribuerad Azure våren-moln instans följer du stegen i den här [snabb](spring-cloud-quickstart-launch-app-portal.md) starten för att distribuera din första våren Cloud-App.

## <a name="bind-azure-cache-for-redis"></a>Bind Azure-cache för Redis

1. Lägg till följande beroende i projektets `pom.xml`

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Ta bort `spring.redis.*` egenskaper i `application.properties`-filen

1. Uppdatera den aktuella distributionen med `az spring-cloud app update` eller skapa en ny distribution med hjälp av `az spring-cloud app deployment create`.

1. Gå till sidan Azure våren Cloud service i Azure Portal. Hitta **program instrument panelen** och välj det program som ska bindas till Azure-cachen för Redis.  Det här är samma program som du uppdaterade eller distribuerade i föregående steg. Välj sedan `Service binding` och välj knappen `Create service binding`. Fyll i formuläret och var noga med att välja **bindnings typ** `Azure Cache for Redis`, din redis-server och alternativet primär nyckel. 

1. Starta om appen och den här bindningen bör fungera nu.

1. För att säkerställa att tjänst bindningen är korrekt väljer du bindnings namnet och kontrollerar dess information. Fältet `property` bör se ut så här:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du binder ditt Azure våren Cloud-program till en Azure Redis cache.  Om du vill veta mer om att binda tjänster till programmet fortsätter du till självstudien för att binda ett program till en MySQL-databas.

> [!div class="nextstepaction"]
> [Lär dig hur du binder en Azure MySQL-tjänst till din Azure våren Cloud-tjänst](spring-cloud-tutorial-bind-mysql.md).