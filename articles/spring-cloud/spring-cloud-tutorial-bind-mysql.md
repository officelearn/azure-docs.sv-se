---
title: Självstudie – så här binder du en Azure Database for MySQL-instans till ditt Azure våren Cloud-program
description: I den här självstudien får du se hur du binder en Azure Database for MySQL-instans till ditt Azure våren Cloud-program
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: a9911798e42db55d5aaae90c933cfb64945b244c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708815"
---
# <a name="tutorial-bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Självstudie: bind en Azure Database for MySQL-instans till ditt Azure våren Cloud-program 

Med Azure våren Cloud kan du binda utvalda Azure-tjänster till dina program automatiskt, i stället för att behöva konfigurera ditt våren Boot-program manuellt. Den här självstudien visar hur du binder ditt program till din Azure Database for MySQL-instans.

## <a name="prerequisites"></a>Krav

* En distribuerad Azure våren Cloud-instans
* Ett Azure Database for MySQL konto
* Azure CLI

Om du inte har en distribuerad Azure våren-moln instans följer du anvisningarna i [snabb start: starta ett Azure våren Cloud-program genom att använda Azure Portal](spring-cloud-quickstart-launch-app-portal.md) för att distribuera din första våren Cloud-App.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Bind din app till din Azure Database for MySQL-instans

1. Anteckna administratörens användar namn och lösen ord för ditt Azure Database for MySQL konto. 

1. Anslut till servern, skapa en databas med namnet **testdb** från en MySQL-klient och skapa sedan ett nytt konto som inte är administratör.

1. Lägg till följande beroende i projektets *Pom. XML-* fil:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. I filen *Application. Properties* tar du bort alla `spring.datasource.*` egenskaper.

1. Uppdatera den aktuella distributionen genom att köra `az spring-cloud app update`eller skapa en ny distribution för den här ändringen genom att köra `az spring-cloud app deployment create`.  Dessa kommandon antingen uppdaterar eller skapar programmet med det nya beroendet.

1. På sidan Azure Portal på sidan **Azure våren Cloud** Service letar du reda på **program instrument panelen**och väljer sedan det program som du vill binda till Azure Database for MySQL instansen.  Detta är samma program som du uppdaterade eller distribuerade i föregående steg. 

1. Välj **tjänst bindning**och välj sedan knappen **skapa bindning för tjänst** . 

1. Fyll i formuläret, Välj **Azure MySQL** som **bindnings typ**, med samma databas namn som du använde tidigare och Använd samma användar namn och lösen ord som du antecknade i det första steget.

1. Starta om appen så fungerar den här bindningen nu.

1. För att säkerställa att tjänst bindningen är korrekt väljer du bindnings namnet och kontrollerar sedan dess information. Fältet `property` bör se ut så här:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du binder ditt Azure våren Cloud-program till en Azure Database for MySQL-instans.  Mer information om hur du hanterar Azure våren Cloud service finns i artikeln om tjänst identifiering och registrering.

> [!div class="nextstepaction"]
> [Aktivera tjänst identifiering och registrering genom att använda våren Cloud Service-registret](spring-cloud-service-registration.md)
