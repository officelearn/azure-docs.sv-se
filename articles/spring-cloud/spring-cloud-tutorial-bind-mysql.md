---
title: Självstudiekurs - Så här binder du en Azure-databas för MySQL-instans till ditt Azure Spring Cloud-program
description: Den här självstudien visar hur du binder en Azure Database for MySQL-instans till ditt Azure Spring Cloud-program
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 657aa70d77fd1af9fd2121a3e98ea3aca7773642
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277546"
---
# <a name="tutorial-bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Självstudiekurs: Binda en Azure-databas för MySQL-instans till ditt Azure Spring Cloud-program 

Med Azure Spring Cloud kan du binda utvalda Azure-tjänster till dina program automatiskt, i stället för att behöva konfigurera ditt Spring Boot-program manuellt. Den här självstudien visar hur du binder ditt program till din Azure-databas för MySQL-instans.

## <a name="prerequisites"></a>Krav

* En distribuerad Azure Spring Cloud-instans
* Ett Azure-databas för MySQL-konto
* Azure CLI

Om du inte har en distribuerad Azure Spring Cloud-instans följer du instruktionerna i [Snabbstart: Starta ett Azure Spring Cloud-program med hjälp av Azure-portalen](spring-cloud-quickstart-launch-app-portal.md) för att distribuera din första Spring Cloud-app.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Binda din app till Din Azure-databas för MySQL-instans

1. Observera administratörsanvändarnamnet och lösenordet för ditt Azure-databas för MySQL-konto. 

1. Anslut till servern, skapa en databas med namnet **testdb** från en MySQL-klient och skapa sedan ett nytt icke-administratörskonto.

1. Lägg till följande beroende i projektets *pom.xml-fil:*

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Ta bort alla egenskaper i `spring.datasource.*` filen *application.properties.*

1. Uppdatera den aktuella `az spring-cloud app update`distributionen genom att köra eller `az spring-cloud app deployment create`skapa en ny distribution för den här ändringen genom att köra .  Dessa kommandon antingen uppdatera eller skapa programmet med det nya beroendet.

1. På din Azure Spring Cloud-tjänstsida i **Azure-portalen** letar du efter **programinstrumentpanelen**och väljer sedan programmet som ska bindas till din Azure-databas för MySQL-instans.  Det här är samma program som du har uppdaterat eller distribuerat i föregående steg. 

1. Välj **Tjänstbindning**och välj sedan knappen **Skapa tjänstbindning.** 

1. Fyll i formuläret, välj **Azure MySQL** som **bindningstyp**, med samma databasnamn som du använde tidigare och använda samma användarnamn och lösenord som du noterade i det första steget.

1. Starta om appen, och den här bindningen ska nu fungera.

1. Om du vill vara säkra på att tjänstbindningen är korrekt markerar du bindningsnamnet och kontrollerar sedan dess detaljer. Fältet `property` ska se ut så här:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du binder ditt Azure Spring Cloud-program till en Azure-databas för MySQL-instans.  Mer information om hur du hanterar din Azure Spring Cloud-tjänst finns i artikeln om identifiering och registrering av tjänster.

> [!div class="nextstepaction"]
> [Aktivera identifiering och registrering av tjänsten med hjälp av Spring Cloud Service-registret](spring-cloud-service-registration.md)
