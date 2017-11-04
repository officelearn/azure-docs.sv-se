---
title: "Så här konfigurerar du en källan Start initieraren app om du vill använda Redis-Cache"
description: "Lär dig hur du konfigurerar en källan startprogrammet skapats med vår Initializr att använda Azure Redis-Cache."
services: redis-cache
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
keywords: "Källan källan Start Starter Redis-Cache"
ms.assetid: 
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: java
ms.topic: article
ms.date: 08/31/2017
ms.author: robmcm;zhijzhao;yidon
ms.openlocfilehash: 7a6ec549654d00975494bac8594a6777af5ec415
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-a-spring-boot-initializer-app-to-use-redis-cache"></a>Så här konfigurerar du en källan Start initieraren app om du vill använda Redis-Cache

## <a name="overview"></a>Översikt

Den  **[Vårversionen Framework]**  är en öppen källkod som hjälper till att Java-utvecklare som skapar program på företagsnivå. En av de mer populära projekt som bygger som plattform är [Vårversionen Start], vilket ger en förenklad metod för att skapa fristående Java-program. Om du vill hjälpa utvecklare att komma igång med vår Start flera exempel källan Start paket finns på <https://github.com/spring-guides/>. Förutom att välja från listan över grundläggande källan Start-projekt i  **[Vårversionen Initializr]**  gör att utvecklare kan komma igång med att skapa anpassade källan startprogram.

Den här artikeln vägleder dig genom att skapa ett Redis-cache med hjälp av Azure-portalen med hjälp av den **Vårversionen Initializr** att skapa ett anpassat program och sedan skapa en Java-webbapp som lagrar och hämtar data med hjälp av din Redis cache.

## <a name="prerequisites"></a>Krav

Följande krav är nödvändiga för att följa stegen i den här artikeln:

* En Azure-prenumeration; Om du inte redan har en Azure-prenumeration, kan du aktivera din [MSDN-prenumerantförmåner] eller registrera dig för en [kostnadsfritt Azure-konto].

* En [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/), 1,7 eller senare.

* [Apache Maven](http://maven.apache.org/), version 3.0 eller senare.

## <a name="create-a-redis-cache-on-azure"></a>Skapa en Redis-cache på Azure

1. Bläddra till den Azure-portalen på <https://portal.azure.com/> och klicka på det **+ ny**.

   ![Azure Portal][AZ01]

1. Klicka på **databasen**, och klicka sedan på **Redis-Cache**.

   ![Azure Portal][AZ02]

1. På den **nytt Redis-Cache** anger du följande information:

   * Ange den **DNS-namnet** för ditt cacheminne.
   * Ange din **prenumeration**, **resursgruppen**, **plats**, och **prisnivå**.
   * Den här kursen väljer **avblockera port 6379**.

   > [!NOTE]
   >
   > Du kan använda SSL med Redis-cache, men du måste använda en annan Redis-klient som Jedis. Mer information finns i [hur du använder Azure Redis-Cache med Java][Redis Cache with Java].
   >

   När du har angett dessa alternativ, klickar du på **skapa** att skapa ditt cacheminne.

   ![Azure Portal][AZ03]

1. När ditt cacheminne har slutförts, visas den på din Azure **instrumentpanelen**, samt enligt den **alla resurser**, och **Redis-cache** sidor. Du kan klicka på ditt cacheminne på någon av dessa platser för att öppna egenskapssidan för ditt cacheminne.

   ![Azure Portal][AZ04]

1. När sidan som innehåller listan över egenskaper för ditt cacheminne visas, klickar på **åtkomstnycklar** och kopiera dina åtkomstnycklar för ditt cacheminne.

   ![Azure Portal][AZ05]

## <a name="create-a-custom-application-using-the-spring-initializr"></a>Skapa ett anpassat program med hjälp av vår Initializr

1. Bläddra till <https://start.spring.io/>.

1. Ange att du vill skapa en **Maven** projektet med **Java**, ange den **grupp** och **Aritifact** namn för ditt program och Klicka på länken till **växla till den fullständiga versionen** av vår Initializr.

   ![Basic-källan Initializr alternativ][SI01]

   > [!NOTE]
   >
   > Källan Initializr kommer att använda den **grupp** och **Aritifact** namn för att skapa paketnamn, till exempel: *com.contoso.myazuredemo*.
   >

1. Rulla ned till den **Web** avsnittet och markera kryssrutan för **Web**, bläddra till den **NoSQL** avsnittet och markera kryssrutan för **Redis**, sedan Bläddra längst ned på sidan och klicka på knappen för att **generera projekt**.

   ![Alternativ för fullständig Vårversionen Initializr][SI02]

1. När du uppmanas, hämta projektet till en sökväg på den lokala datorn.

   ![Hämta anpassade källan Start-projekt][SI03]

1. När du har extraherat filerna på den lokala datorn, vara anpassade källan Start program klar för redigering.

   ![Projektfiler för anpassade källan Start][SI04]

## <a name="configure-your-custom-spring-boot-to-use-your-redis-cache"></a>Konfigurera din anpassade källan Start om du vill använda Redis-Cache

1. Leta upp den *application.properties* filen i den *resurser* katalogen för din app, eller skapa filen om den inte redan finns.

   ![Leta reda på filen application.properties][RE01]

1. Öppna den *application.properties* filen i en textredigerare och Lägg till följande rader i filen och Ersätt exempelvärdena med lämpliga egenskaper från ditt cacheminne:

   ```yaml
   # Specify the DNS URI of your Redis cache.
   spring.redis.host=myspringbootcache.redis.cache.windows.net

   # Specify the port for your Redis cache.
   spring.redis.port=6379

   # Specify the access key for your Redis cache.
   spring.redis.password=57686f6120447564652c2049495320526f636b73=
   ```

   ![Redigera filen application.properties][RE02]

   > [!NOTE]
   >
   > Om du använder en annan Redis-klient som Jedis som möjliggör SSL, anger du porten 6380 i din *application.properties* fil. Mer information finns i [hur du använder Azure Redis-Cache med Java][Redis Cache with Java].
   >

1. Spara och Stäng den *application.properties* fil.

1. Skapa en mapp med namnet *domänkontrollant* under källmappen för paketet, till exempel:

   `C:\SpringBoot\myazuredemo\src\main\java\com\contoso\myazuredemo\controller`

   ELLER

   `/users/example/home/myazuredemo/src/main/java/com/contoso/myazuredemo/controller`

1. Skapa en ny fil med namnet *HelloController.java* i den *domänkontrollant* mapp. Öppna filen i en textredigerare och Lägg till följande kod:

   ```java
   package com.contoso.myazuredemo;

   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.data.redis.core.StringRedisTemplate;
   import org.springframework.data.redis.core.ValueOperations;

   @RestController
   public class HelloController {
   
      @Autowired
      private StringRedisTemplate template;

      @RequestMapping("/")
      // Define the Hello World controller.
      public String hello() {
      
         ValueOperations<String, String> ops = this.template.opsForValue();

         // Add a Hello World string to your cache.
         String key = "greeting";
         if (!this.template.hasKey(key)) {
             ops.set(key, "Hello World!");
         }

         // Return the string from your cache.
         return ops.get(key);
      }
   }
   ```
   
   Om du behöver ersätta `com.contoso.myazuredemo` med paketnamnet för projektet.

1. Spara och Stäng den *HelloController.java* fil.

1. Skapa din källan startprogrammet med Maven och kör den. Exempel:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. Testa webbappen genom att bläddra till http://localhost: 8080 i en webbläsare, eller Använd syntax som i följande exempel om du har curl som är tillgängliga:

   ```shell
   curl http://localhost:8080
   ```

   Du bör se ”Hello World”! meddelande från ditt exempel controller visas som hämtas dynamiskt från Redis-cache.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder värdet startprogram i Azure finns i följande artiklar:

* [Distribuera ett program för start av källan till Azure App Service](../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)

* [Kör ett program för start av värdet på ett Kubernetes kluster i Azure Container Service](../container-service/container-service-deploy-spring-boot-app-on-kubernetes.md)

Mer information om hur du använder Azure med Java finns på [Azure Java Developer Center] och i [Java Tools för Visual Studio Team Services].

Mer information om att komma igång med Redis-Cache med Java i Azure, finns [hur du använder Azure Redis-Cache med Java][Redis Cache with Java].

<!-- URL List -->

[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[kostnadsfritt Azure-konto]: https://azure.microsoft.com/pricing/free-trial/
[Java Tools för Visual Studio Team Services]: https://java.visualstudio.com/
[MSDN-prenumerantförmåner]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Vårversionen Start]: http://projects.spring.io/spring-boot/
[Vårversionen Initializr]: https://start.spring.io/
[Vårversionen Framework]: https://spring.io/
[Redis Cache with Java]: cache-java-get-started.md

<!-- IMG List -->

[AZ01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ01.png
[AZ02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ02.png
[AZ03]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ03.png
[AZ04]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ04.png
[AZ05]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ05.png

[SI01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI01.png
[SI02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI02.png
[SI03]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI03.png
[SI04]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI04.png

[RE01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/RE01.png
[RE02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/RE02.png
