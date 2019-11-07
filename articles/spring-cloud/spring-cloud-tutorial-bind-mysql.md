---
title: Så här binder du Azure Database for MySQL till ditt Azure våren Cloud-program | Microsoft Docs
description: I den här artikeln visas hur du binder Azure MySQL till ditt Azure våren Cloud-program
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: b6de5bb3b25c111d1b7775ea9570a4ae2cf45042
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607593"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>Självstudie: bind Azure-tjänster till ditt Azure våren Cloud-program: Azure Database for MySQL

Med Azure våren Cloud kan du binda utvalda Azure-tjänster till dina program automatiskt, i stället för att manuellt konfigurera ditt våren Boot-program. I den här självstudien visas hur du binder ditt program till Azure MySQL.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En distribuerad Azure våren Cloud-instans
* Ett Azure Database for MySQL konto
* Azure CLI

Om du inte har en distribuerad Azure våren-moln instans följer du stegen i den här [snabb](spring-cloud-quickstart-launch-app-portal.md) starten för att distribuera din första våren Cloud-App.

## <a name="bind-azure-database-for-mysql"></a>Bind Azure Database for MySQL

1. Anteckna administratörens användar namn och lösen ord för ditt Azure MySQL-konto. Anslut till servern och skapa en databas med namnet `testdb` från en MySQL-klient. Skapa ett nytt konto som inte är administratör.

1. Lägg till följande beroende i projektets `pom.xml`

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Ta bort `spring.datasource.*`-egenskaper, om det finns, i filen `application.properties`.

1. Uppdatera den aktuella distributionen med `az spring-cloud app update` eller skapa en ny distribution för den här ändringen med `az spring-cloud app deployment create`.  Dessa kommandon antingen uppdaterar eller skapar programmet med det nya beroendet.

1. Gå till sidan Azure våren Cloud service i Azure Portal. Hitta **program instrument panelen** och välj det program som ska bindas till Azure MySQL.  Det här är samma program som du uppdaterade eller distribuerade i föregående steg. Välj sedan `Service binding` och välj knappen `Create service binding`. Fyll i formuläret, se till att välja **bindnings typ** `Azure MySQL`, samma databas namn som du använde tidigare och samma användar namn och lösen ord som du antecknade i det första steget.

1. Starta om appen så fungerar den här bindningen nu.

1. För att säkerställa att tjänst bindningen är korrekt väljer du bindnings namnet och kontrollerar dess information. Fältet `property` bör se ut så här:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig att binda ditt Azure våren Cloud-program till en MySQL-databas.  Mer information om hur du hanterar din Azure våren Cloud-tjänst finns i om du vill veta mer om tjänst identifiering och registrering.

> [!div class="nextstepaction"]
> [Lär dig hur du aktiverar tjänst identifiering och-registreringar med hjälp av vår moln tjänst register](spring-cloud-service-registration.md).

