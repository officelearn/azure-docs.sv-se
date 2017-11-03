---
title: "Distribuera en Webbapp för vår Start på Linux i Azure Container Service | Microsoft Docs"
description: "Den här självstudiekursen vägleder dig genom stegen för att distribuera en källan startprogrammet som en Linux-webbapp i Microsoft Azure."
services: container-service
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: container-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/04/2017
ms.author: asirveda;robmcm
ms.custom: mvc
ms.openlocfilehash: daa0ed81a6b9f20e146698947099a991da42cd6d
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="deploy-a-spring-boot-application-on-linux-in-the-azure-container-service"></a>Distribuera en källan startprogrammet på Linux i Azure Container Service

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Den  **[Vårversionen Framework]**  är en öppen källkod som hjälper till att Java-utvecklare som skapar program på företagsnivå. En av de mer populära projekt som bygger som plattform är [Vårversionen Start], vilket ger en förenklad metod för att skapa fristående Java-program.

**[Docker]**  är öppen källkod som hjälper utvecklare att automatisera distribution, skalning och hanteringen av sina program som körs i behållare.

Den här självstudiekursen vägleder dig genom med Docker att utveckla och distribuera en startprogrammet för källan till en Linux-värd i den [Azure Container Service (ACS)].

## <a name="prerequisites"></a>Krav

För att kunna slutföra stegen i den här självstudiekursen, måste du ha följande krav:

* En Azure-prenumeration; Om du inte redan har en Azure-prenumeration, kan du aktivera din [MSDN-prenumerantförmåner] eller registrera dig för en [kostnadsfritt Azure-konto].
* Den [Azure-kommandoradsgränssnittet (CLI)].
* En uppdaterad [Java Developer Kit (JDK)].
* Apache's [Maven] skapa verktyget (Version 3).
* En [Git] klienten.
* En [Docker] klienten.

> [!NOTE]
>
> På grund av virtualisering kraven i den här självstudiekursen, kan du följa stegen i den här artikeln på en virtuell dator; Du måste använda en fysisk dator med aktiverat funktionerna för virtualisering.
>

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Skapa källan Start på Docker komma igång webbprogram

Följande steg vägleder dig igenom de steg som krävs för att skapa ett enkelt källan Start-webbprogram och testa den lokalt.

1. Öppna en kommandotolk och skapa en lokal katalog för att hålla ditt program och ändra till katalogen; Exempel:
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   -- eller--
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. Klona den [Vårversionen Start på Docker komma igång] exempelprojektet till den katalog som du har skapat, till exempel:
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Ändra katalogen till slutförda projektet. Exempel:
   ```
   cd gs-spring-boot-docker/complete
   ```

1. Skapa JAR-filen med Maven; Exempel:
   ```
   mvn package
   ```

1. När webbappen har skapats, ändra katalogen till den `target` katalogen där JAR-filen finns och starta webbprogram, till exempel:
   ```
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar
   ```

1. Testa webbappen genom att bläddra till den lokalt med hjälp av en webbläsare. Till exempel om du har konfigurerade curl som är tillgängliga och du Tomcat-servern körs på port 80:
   ```
   curl http://localhost
   ```

1. Du bör se följande meddelande visas: **Hello Docker World!**

   ![Bläddra Sample-appen lokalt][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Skapa ett Azure Container registret ska användas som ett privat Docker-register

Följande steg beskriver hur du använder Azure portal för att skapa ett Azure Container registernyckel.

> [!NOTE]
>
> Om du vill använda Azure CLI i stället för Azure-portalen, följer du stegen i [skapa en privat Docker behållare registret med hjälp av Azure CLI 2.0](../../container-registry/container-registry-get-started-azure-cli.md).
>

1. Bläddra till den [Azure-portalen] och logga in.

   När du har loggat in till ditt konto på Azure portal, du kan följa stegen i den [skapa en privat Docker behållare registret med hjälp av Azure portal] artikel som omskrivning i följande steg för sake av lämplighetsskäl.

1. Klicka på ikonen menyn för **+ ny**, klicka på **behållare**, och klicka sedan på **Azure Container registret**.
   
   ![Skapa en ny Azure-behållare registernyckel][AR01]

1. När sidan för Azure-behållare registret mallen visas, klickar på **skapa**. 

   ![Skapa en ny Azure-behållare registernyckel][AR02]

1. När den **skapa behållaren registret** visas, ange din **registrets** och **resursgruppen**, Välj **aktivera** för den **Administratörsanvändare**, och klicka sedan på **skapa**.

   ![Konfigurera registerinställningar för Azure-behållare][AR03]

1. När registret behållaren har skapats, navigera till behållaren registret i Azure-portalen och klicka sedan på **åtkomstnycklar**. Anteckna användarnamnet och lösenordet i nästa steg.

   ![Azure Container åtkomst registernycklar][AR04]

## <a name="configure-maven-to-use-your-azure-container-registry-access-keys"></a>Konfigurera Maven för att använda dina Azure-behållare åtkomst registernycklar

1. Navigera till konfigurationskatalogen för Maven-installation och öppna den *settings.xml* fil med en textredigerare.

1. Lägga till din Azure-behållare åtkomst registerinställningar från föregående avsnitt i den här kursen och den `<servers>` samling i den *settings.xml* filen, till exempel:

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

1. Navigera till slutförda projektkatalogen för tillämpningsprogrammet källan start (till exempel ”:*C:\SpringBoot\gs-spring-boot-docker\complete*” eller ”*/users/robert/SpringBoot/gs-spring-boot-docker/complete* ”), och öppna den *pom.xml* fil med en textredigerare.

1. Uppdatering av `<properties>` samling i den *pom.xml* fil med inloggningen servervärdet för registernyckeln din Azure-behållare från föregående avsnitt i den här kursen; till exempel:

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Uppdatering i `<plugins>` samling i den *pom.xml* filen så att den `<plugin>` innehåller inloggningen adress och registret servernamnet för din Azure-behållare registernyckeln från föregående avsnitt i den här kursen. Exempel:

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
         <serverId>wingtiptoysregistry</serverId>
         <registryUrl>https://wingtiptoysregistry.azurecr.io</registryUrl>
      </configuration>
   </plugin>
   ```

1. Navigera till slutförda projektkatalogen för tillämpningsprogrammet källan start och kör följande kommando för att återskapa programmet och push-behållaren i registret för Azure-behållare:

   ```
   mvn package docker:build -DpushImage 
   ```

> [!NOTE]
>
> När du trycker Docker-behållare till Azure, får du ett felmeddelande som liknar något av följande även om din dockerbehållare har skapats:
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: no basic auth credentials`
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: Incomplete Docker registry authorization credentials. Please provide all of username, password, and email or none.`
>
> Om det händer kan behöva du logga in på ditt Azure-konto från kommandoraden Docker; Exempel:
>
> `docker login -u wingtiptoysregistry -p "AbCdEfGhIjKlMnOpQrStUvWxYz" wingtiptoysregistry.azurecr.io`
>
> Du kan sedan överför din behållare från kommandoraden. Exempel:
>
> `docker push wingtiptoysregistry.azurecr.io/gs-spring-boot-docker`
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-using-your-container-image"></a>Skapa en webbapp i Linux på Azure App Service med behållaren avbildningen

1. Bläddra till den [Azure-portalen] och logga in.

1. Klicka på ikonen menyn för **+ ny**, klicka på **webb + mobilt**, och klicka sedan på **webbprogrammet på Linux**.
   
   ![Skapa en ny webbapp i Azure-portalen][LX01]

1. När den **webbprogrammet på Linux** visas, anger du följande information:

   a. Ange ett unikt namn för den **appnamn**, till exempel ”:*wingtiptoyslinux*”.

   b. Välj din **prenumeration** från den nedrullningsbara listan.

   c. Välj en befintlig **resursgruppen**, eller ange ett namn för att skapa en ny resursgrupp.

   d. Klicka på **konfigurera behållaren** och ange följande information:

      * Välj **privata registret**.

      * **Avbildningen och valfria taggen**: Ange din behållarnamn från tidigare, till exempel ”:*wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest*”

      * **Serveradress**: Ange registret URL: en från tidigare, till exempel ”:*https://wingtiptoysregistry.azurecr.io*”

      * **Användarnamn för inloggning** och **lösenord**: Ange dina inloggningsuppgifter från din **åtkomstnycklar** som du använde i föregående steg.
   
   e. När du har angett all information som ovan, klickar du på **OK**.

   ![Konfigurera inställningarna för webbappen][LX02]

1. Klicka på **Skapa**.

> [!NOTE]
>
> Azure mappas automatiskt Internet-förfrågningar till inbäddade Tomcat-server som kör på standardporten 80 eller 8080. Men om du har konfigurerat inbäddade Tomcat servern ska köras på en anpassad port, måste du lägga till en miljövariabel till ditt webbprogram som definierar port för inbäddade Tomcat-servern. Så här loggar du in:
>
> 1. Bläddra till den [Azure-portalen] och logga in.
> 
> 2. Klicka på ikonen för **Apptjänster**. (Se artikeln #1 i bilden nedan.)
>
> 3. Välj webbapp i listan. (Objektet #2 i bilden nedan.)
>
> 4. Klicka på **programinställningar**. (Post #3 i bilden nedan.)
>
> 5. I den **appinställningar** lägger du till en ny miljövariabel med namnet **PORT** och ange din anpassade portnummer för värdet. (Post #4 i bilden nedan.)
>
> 6. Klicka på **Spara**. (Artikel #5 i bilden nedan.)
>
> ![Om du sparar ett anpassat portnummer i Azure-portalen][LX03]
>

<!--
##  OPTIONAL: Configure the embedded Tomcat server to run on a different port

The embedded Tomcat server in the sample Spring Boot application is configured to run on port 8080 by default. However, if you want to run the embedded Tomcat server to run on a different port, such as port 80 for local testing, you can configure the port by using the following steps.

1. Go to the *resources* directory (or create the directory if it does not exist); for example:
   ```shell
   cd src/main/resources
   ```

1. Open the *application.yml* file in a text editor if it exists, or create a new YAML file if it does not exist.

1. Modify the **server** setting so that the server runs on port 80; for example:
   ```yaml
   server:
      port: 80
   ```

1. Save and close the *application.yml* file.
-->

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder värdet startprogram i Azure finns i följande artiklar:

* [Distribuera ett program för start av källan till Azure App Service](../../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)
* [Distribuera ett program för start av värdet på ett Kubernetes kluster i Azure Container Service](container-service-deploy-spring-boot-app-on-kubernetes.md)

Mer information om hur du använder Azure med Java finns på [Azure Java Developer Center] och i [Java Tools för Visual Studio Team Services].

Mer information om källan Start på Docker exempelprojektet finns [Vårversionen Start på Docker komma igång].

Hjälp med att komma igång med din egen källan startprogram, finns det **Vårversionen Initializr** på https://start.spring.io/.

Mer information om att komma igång med att skapa ett enkelt källan Start-program finns i vår Initializr på https://start.spring.io/.

Ytterligare exempel för hur du använder anpassade Docker-avbildningar med Azure finns [med hjälp av en anpassad Docker-avbildning för Azure Web App på Linux].

<!-- URL List -->

[Azure-kommandoradsgränssnittet (CLI)]: /cli/azure/overview
[Azure Container Service (ACS)]: https://azure.microsoft.com/services/container-service/
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Azure-portalen]: https://portal.azure.com/
[skapa en privat Docker behållare registret med hjälp av Azure portal]: /azure/container-registry/container-registry-get-started-portal
[med hjälp av en anpassad Docker-avbildning för Azure Web App på Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[kostnadsfritt Azure-konto]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools för Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[MSDN-prenumerantförmåner]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Vårversionen Start]: http://projects.spring.io/spring-boot/
[Vårversionen Start på Docker komma igång]: https://github.com/spring-guides/gs-spring-boot-docker
[Vårversionen Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-on-linux/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-on-linux/SB02.png

[AR01]: ./media/container-service-deploy-spring-boot-app-on-linux/AR01.png
[AR02]: ./media/container-service-deploy-spring-boot-app-on-linux/AR02.png
[AR03]: ./media/container-service-deploy-spring-boot-app-on-linux/AR03.png
[AR04]: ./media/container-service-deploy-spring-boot-app-on-linux/AR04.png

[LX01]: ./media/container-service-deploy-spring-boot-app-on-linux/LX01.png
[LX02]: ./media/container-service-deploy-spring-boot-app-on-linux/LX02.png
[LX03]: ./media/container-service-deploy-spring-boot-app-on-linux/LX03.png
