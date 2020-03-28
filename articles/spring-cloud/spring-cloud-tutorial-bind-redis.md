---
title: Självstudiekurs - Bind Azure Cache för Redis till ditt Azure Spring Cloud-program
description: Den här självstudien visar hur du binder Azure Cache for Redis till ditt Azure Spring Cloud-program
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: 94f7b5a2363b7c53e0f70500e5a0a8cb6f64e611
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277509"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Bind Azure Cache för Redis till ditt Azure Spring Cloud-program 

I stället för att konfigurera dina Spring Boot-program manuellt kan du automatiskt binda utvalda Azure-tjänster till dina program med hjälp av Azure Spring Cloud. Den här artikeln visar hur du binder ditt program till Azure Cache för Redis.

## <a name="prerequisites"></a>Krav

* En distribuerad Azure Spring Cloud-instans
* En Azure-cache för Redis-tjänstinstans
* Azure Spring Cloud-tillägget för Azure CLI

Om du inte har en distribuerad Azure Spring Cloud-instans följer du stegen i [snabbstarten för distribution av en Azure Spring Cloud-app](spring-cloud-quickstart-launch-app-portal.md).

## <a name="bind-azure-cache-for-redis"></a>Bind Azure-cache för Redis

1. Lägg till följande beroende i projektets pom.xml-fil:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Ta `spring.redis.*` bort alla `application.properties` egenskaper från filen

1. Uppdatera den aktuella distributionen med eller `az spring-cloud app update` skapa en ny distribution med `az spring-cloud app deployment create`.

1. Gå till din Azure Spring Cloud-tjänstsida i Azure-portalen. Gå till **Programinstrumentpanelen** och välj programmet som ska bindas till Azure Cache för Redis. Det här programmet är samma som du har uppdaterat eller distribuerat i föregående steg.

1. Välj **Tjänstbindning** och välj **Skapa tjänstbindning**. Fyll i formuläret och se till att välja **värdet för bindningstypen** **Azure Cache för Redis,** din Azure-cache för Redis-server och alternativet **Primärnyckel.**

1. Starta om appen. Bindningen bör nu fungera.

1. Om du vill vara säkra på att tjänstbindningen är korrekt väljer du bindningsnamnet och verifierar dess uppgifter. Fältet `property` ska se ut så här:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du binder ditt Azure Spring Cloud-program till Azure Cache for Redis. Om du vill veta mer om bindningstjänster till ditt program fortsätter du till självstudien om hur du binder ett program till en Azure-databas för MySQL-instans.

> [!div class="nextstepaction"]
> [Lär dig hur du binder till en Azure-databas för MySQL-instans](spring-cloud-tutorial-bind-mysql.md)
