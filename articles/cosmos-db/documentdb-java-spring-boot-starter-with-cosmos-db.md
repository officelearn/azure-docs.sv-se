---
title: "Hur du använder värdet Start Starter med en Azure Cosmos DB DocumentDB-API"
description: "Lär dig hur du konfigurerar ett program som skapats med vår Start initieraren med Azure Cosmos DB DocumentDB API."
services: cosmos-db
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
keywords: "Källan källan Start Starter Cosmos DB"
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/08/2017
ms.author: robmcm;yungez;kevinzha
ms.openlocfilehash: 273cc750857c5e466882060a38ac0f3475811e98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-spring-boot-starter-with-azure-cosmos-db-documentdb-api"></a>Hur du använder värdet Start Starter med Azure Cosmos DB DocumentDB API

## <a name="overview"></a>Översikt

Den  **[Vårversionen Framework]**  är en öppen källkod som hjälper till att Java-utvecklare som skapar program på företagsnivå. En av de mer populära projekt som bygger som plattform är [Vårversionen Start], vilket ger en förenklad metod för att skapa fristående Java-program. Om du vill hjälpa utvecklare att komma igång med vår Start flera exempel källan Start paket finns på <https://github.com/spring-guides/>. Förutom att välja från listan över grundläggande källan Start-projekt i  **[Vårversionen Initializr]**  gör att utvecklare kan komma igång med att skapa anpassade källan startprogram.

Azure Cosmos-DB är en databastjänst för globalt distribuerad som gör att utvecklare att arbeta med data med ett antal olika standard API: er, till exempel DocumentDB, MongoDB, diagram och tabell-API: er. Microsofts källan Start Starter gör att utvecklare kan använda vår startprogram som lätt kan integrerar med Azure Cosmos DB med DocumentDB APIs.

Den här artikeln visar hur du skapar en Azure Cosmos-databas med Azure-portalen och sedan använda den **Vårversionen Initializr** att skapa ett anpassat java-program och sedan lägga till funktionen källan Start Starter anpassade program att lagra data i och hämta data från Azure Cosmos-DB med DocumentDB-API.

## <a name="prerequisites"></a>Krav

Följande krav är nödvändiga för att följa stegen i den här artikeln:

* En Azure-prenumeration; Om du inte redan har en Azure-prenumeration, kan du aktivera din [MSDN-prenumerantförmåner] eller registrera dig för en [kostnadsfritt Azure-konto].

* En [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/), 1,7 eller senare.

* [Apache Maven](http://maven.apache.org/), version 3.0 eller senare.

## <a name="create-an-azure-cosmos-db-by-using-the-azure-portal"></a>Skapa en Azure-Cosmos-databas med hjälp av Azure portal

1. Bläddra till den Azure-portalen på <https://portal.azure.com/> och på **+ ny**.

   ![Azure Portal][AZ01]

1. Klicka på **databaser**, och klicka sedan på **Azure Cosmos DB**.

   ![Azure Portal][AZ02]

1. På den **Azure Cosmos DB** anger du följande information:

   * Ange ett unikt **ID**, som du vill använda som URI: N för din databas. Till exempel: *wingtiptoysdata.documents.azure.com*.
   * Välj **SQL (dokumentet DB)** för API: et.
   * Välj den **prenumeration** du vill använda för din databas.
   * Ange om du vill skapa en ny **resursgruppen** för databasen, eller välj en befintlig resursgrupp.
   * Ange den **plats** för din databas.
   
   När du har angett dessa alternativ, klickar du på **skapa** att skapa databasen.

   ![Azure Portal][AZ03]

1. När databasen har skapats kan den visas på din Azure **instrumentpanelen**, samt enligt den **alla resurser** och **Azure Cosmos DB** sidor. Du kan klicka på din databas på någon av dessa platser för att öppna egenskapssidan för ditt cacheminne.

   ![Azure Portal][AZ04]

1. När egenskapssidan för din databas visas, klickar på **åtkomstnycklar** och kopiera URI och åtkomst-nycklar för din databas, dessa värden används i tillämpningsprogrammet källan start.

   ![Azure Portal][AZ05]

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Skapa ett enkelt källan Start-program med vår Initializr

1. Bläddra till <https://start.spring.io/>.

1. Ange att du vill skapa en **Maven** projektet med **Java**, ange den **grupp** och **artefakt** namn för ditt program och klicka sedan på knappen för att **generera projekt**.

   ![Basic-källan Initializr alternativ][SI01]

   > [!NOTE]
   >
   > Källan Initializr använder den **grupp** och **artefakt** namn för att skapa paketnamn, till exempel: *com.example.wintiptoys*.
   >

1. När du uppmanas, hämta projektet till en sökväg på den lokala datorn.

   ![Hämta anpassade källan Start-projekt][SI02]

1. När du har extraherat filerna på den lokala datorn, vara enkla källan Start programmet klar för redigering.

   ![Projektfiler för anpassade källan Start][SI03]

## <a name="configure-your-spring-boot-app-to-use-the-azure-spring-boot-starter"></a>Konfigurera appen för att använda Azure källan Start Starter källan Start

1. Leta upp den *pom.xml* filen i katalogen för din app, till exempel:

   `C:\SpringBoot\wingtiptoys\pom.xml`

   ELLER

   `/users/example/home/wingtiptoys/pom.xml`

   ![Leta upp filen pom.xml][PM01]

1. Öppna den *pom.xml* filen i en textredigerare och Lägg till följande rader i listan över `<dependencies>`:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-documentdb-spring-boot-starter</artifactId>
      <version>0.1.4</version>
   </dependency>
   ```

   ![Redigera filen pom.xml][PM02]

1. Spara och Stäng den *pom.xml* fil.

## <a name="configure-your-spring-boot-app-to-use-your-azure-cosmos-db"></a>Konfigurera källan Start appen om du vill använda Azure Cosmos-DB

1. Leta upp den *application.properties* filen i den *resurser* katalogen för din app, till exempel:

   `C:\SpringBoot\wingtiptoys\src\main\resources\application.properties`

   ELLER

   `/users/example/home/wingtiptoys/src/main/resources/application.properties`

   ![Leta reda på filen application.properties][RE01]

1. Öppna den *application.properties* filen i en textredigerare och Lägg till följande rader i filen och Ersätt exempelvärdena med lämpliga egenskaper för databasen:

   ```yaml
   # Specify the DNS URI of your Azure Cosmos DB.
   azure.documentdb.uri=https://wingtiptoys.documents.azure.com:443/

   # Specify the access key for your database.
   azure.documentdb.key=57686f6120447564652c20426f6220526f636b73==

   # Specify the name of your database.
   azure.documentdb.database=wingtiptoysdata
   ```

   ![Redigera filen application.properties][RE02]

1. Spara och Stäng den *application.properties* fil.

## <a name="add-sample-code-to-implement-basic-database-functionality"></a>Lägg till exempelkod för att implementera grundläggande databasfunktioner

I det här avsnittet skapar du två Java-klasser för lagring av användardata och ändra programmets klassen för att skapa en instans av användarklassen och spara den på din databas.

### <a name="define-a-basic-class-for-storing-user-data"></a>Definiera en grundläggande klass för lagring av användardata

1. Skapa en ny fil med namnet *User.java* i samma katalog som programmets Java-huvudfil.

1. Öppna den *User.java* filen i en textredigerare och Lägg till följande rader i filen för att definiera en allmän användarklass som lagrar och hämtar värden i databasen:

   ```java
   package com.example.wingtiptoys;

   public class User {
      private String id;
      private String firstName;
      private String lastName;
 
      public User(String id, String firstName, String lastName) {
         this.id = id;
         this.firstName = firstName;
         this.lastName = lastName;
      }
   
      public String getId() {
         return this.id;
      }

      public void setId(String id) {
         this.id = id;
      }

      public String getFirstName() {
         return firstName;
      }

      public void setFirstName(String firstName) {
         this.firstName = firstName;
      }

      public String getLastName() {
         return lastName;
      }

      public void setLastName(String lastName) {
         this.lastName = lastName;
      }

      @Override
      public String toString() {
         return String.format("User: %s %s", firstName, lastName);
      }
   }
   ```

1. Spara och Stäng den *User.java* fil.

### <a name="define-a-data-repository-interface"></a>Definiera ett gränssnitt för databasen

1. Skapa en ny fil med namnet *UserRepository.java* i samma katalog som programmets Java-huvudfil.

1. Öppna den *UserRepository.java* filen i en textredigerare och Lägg till följande rader i filen för att definiera ett användargränssnitt för databasen som utökar standardgränssnittet för DocumentDB-databasen:

   ```java
   package com.example.wingtiptoys;

   import com.microsoft.azure.spring.data.documentdb.repository.DocumentDbRepository;
   import org.springframework.stereotype.Repository;

   @Repository
   public interface UserRepository extends DocumentDbRepository<User, String> {}   
   ```

1. Spara och Stäng den *UserRepository.java* fil.

### <a name="modify-the-main-application-class"></a>Ändra programmets-klass

1. Leta reda på programmets Java-filen i paketkatalogen för din app; Exempel:

   `C:\SpringBoot\wingtiptoys\src\main\java\com\example\wingtiptoys\WingtiptoysApplication.java`

   ELLER

   `/users/example/home/wingtiptoys/src/main/java/com/example/wingtiptoys/WingtiptoysApplication.java`

   ![Leta upp filen med programpaketet Java][JV01]

1. Öppna programmets Java-filen i en textredigerare och Lägg till följande rader i filen:

   ```java
   package com.example.wingtiptoys;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.CommandLineRunner;

   @SpringBootApplication
   public class WingtiptoysApplication implements CommandLineRunner {

      @Autowired
      private UserRepository repository;
    
      public static void main(String[] args) {
         SpringApplication.run(WingtiptoysApplication.class, args);
      }

      public void run(String... var1) throws Exception {
         final User testUser = new User("testId", "testFirstName", "testLastName");

         repository.deleteAll();
         repository.save(testUser);

         final User result = repository.findOne(testUser.getId());

         System.out.printf("\n\n%s\n\n",result.toString());
      }
   }
   ```

1. Spara och stäng filen programmets Java.

## <a name="build-and-test-your-app"></a>Skapa och testa din app

1. Öppna en kommandotolk och ändra katalogen till mappen där din *pom.xml* finns, till exempel:

   `cd C:\SpringBoot\wingtiptoys`

   ELLER

   `cd /users/example/home/wingtiptoys`

1. Skapa din källan startprogrammet med Maven och kör den. Exempel:

   ```shell
   mvn package
   java -jar target/wingtiptoys-0.0.1-SNAPSHOT.jar
   ```

1. Programmet visas flera runtime-meddelanden och du bör se meddelandet `User: testFirstName testLastName` visas som indikerar att värden har lagras och hämtas från databasen.

   ![Lyckad utdata från programmet][JV02]

1. Valfritt: Du kan använda Azure-portalen för att visa innehållet i din Azure-Cosmos-DB från egenskapssidan för din databas genom att klicka på **Dokumentutforskaren**, och sedan välja och objekt från listan att visa innehållet.

   ![Använda dokumentet Explorer för att visa dina data][JV03]

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Azure Cosmos DB och Java finns i följande artiklar:

* [Dokumentation för Azure Cosmos DB].

* [Azure Cosmos DB: Skapa en DocumentDB-API-app med Java och Azure portal][Build a DocumentDB API app with Java]

Mer information om hur du använder värdet startprogram i Azure finns i följande artiklar:

* [Källan Start DocumenDB Starter för Azure](https://github.com/Microsoft/azure-spring-boot-starters/tree/master/azure-documentdb-spring-boot-starter-sample)

* [Distribuera ett program för start av källan till Azure App Service](../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)

* [Kör ett program för start av värdet på ett Kubernetes kluster i Azure Container Service](../container-service/container-service-deploy-spring-boot-app-on-kubernetes.md)

Mer information om hur du använder Azure med Java finns på [Azure Java Developer Center] och i [Java Tools för Visual Studio Team Services].

<!-- URL List -->

[Dokumentation för Azure Cosmos DB]: /azure/cosmos-db/
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Build a DocumentDB API app with Java]: https://docs.microsoft.com/azure/cosmos-db/create-documentdb-java
[kostnadsfritt Azure-konto]: https://azure.microsoft.com/pricing/free-trial/
[Java Tools för Visual Studio Team Services]: https://java.visualstudio.com/
[MSDN-prenumerantförmåner]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Vårversionen Start]: http://projects.spring.io/spring-boot/
[Vårversionen Initializr]: https://start.spring.io/
[Vårversionen Framework]: https://spring.io/

<!-- IMG List -->

[AZ01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ01.png
[AZ02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ02.png
[AZ03]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ03.png
[AZ04]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ04.png
[AZ05]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/AZ05.png

[SI01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/SI01.png
[SI02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/SI02.png
[SI03]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/SI03.png

[RE01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/RE01.png
[RE02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/RE02.png

[PM01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/PM01.png
[PM02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/PM02.png

[JV01]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/JV01.png
[JV02]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/JV02.png
[JV03]: ./media/documentdb-java-spring-boot-starter-with-cosmos-db/JV03.png
