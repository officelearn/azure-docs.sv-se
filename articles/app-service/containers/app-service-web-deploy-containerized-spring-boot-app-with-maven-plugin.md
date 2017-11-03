---
title: "Hur du använder Maven plugin-programmet för webbprogrammet för behållare för att distribuera en behållare källan Start-app till Azure"
description: "Lär dig hur du använder Maven plugin-programmet för webbprogrammet för behållare för att distribuera en start av vår app till Azure."
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/07/2017
ms.author: robmcm;kevinzha
ms.openlocfilehash: 0329aa9b88c7542ab3235a104a0652cd217ff872
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="how-to-use-the-maven-plugin-for-web-app-for-containers-to-deploy-a-containerized-spring-boot-app-to-azure"></a>Hur du använder Maven plugin-programmet för webbprogrammet för behållare för att distribuera en behållare källan Start-app till Azure

Den [Maven-plugin-programmet för webbprogrammet för behållare](https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin) för [Apache Maven](http://maven.apache.org/) ger sömlös integration av Azure App Service till Maven-projekt och förenklar processen för utvecklare att distribuera webbappar till Azure Apptjänst.

Den här artikeln visar använder Maven plugin-programmet för webbprogrammet för behållare för att distribuera ett exempelprogram källan Start i en dockerbehållare till webbprogram för behållare.

> [!NOTE]
>
> Plugin-programmet för Maven för webbprogrammet för behållare är tillgänglig som en förhandsgranskning. För närvarande stöds endast FTP-publicering, även om ytterligare funktioner som är tänkta för framtiden.
>

## <a name="prerequisites"></a>Krav

För att kunna slutföra stegen i den här självstudiekursen, måste du ha följande krav:

* En Azure-prenumeration; Om du inte redan har en Azure-prenumeration, kan du aktivera din [MSDN-prenumerantförmåner] eller registrera dig för en [kostnadsfritt Azure-konto].
* Den [Azure-kommandoradsgränssnittet (CLI)].
* En uppdaterad [Java Development Kit (JDK)], version 1.7 eller senare.
* Apache's [Maven] skapa verktyget (Version 3).
* En [Git] klienten.
* En [Docker] klienten.

> [!NOTE]
>
> På grund av virtualisering kraven i den här självstudiekursen, kan du följa stegen i den här artikeln på en virtuell dator; Du måste använda en fysisk dator med aktiverat funktionerna för virtualisering.
>

## <a name="clone-the-sample-spring-boot-application"></a>Klona källan Start exempelprogrammet

I det här avsnittet klona en container startprogrammet för källan och testa den lokalt.

1. Öppna en kommandotolk eller ett terminalfönster och skapa en lokal katalog för att hålla tillämpningsprogrammet källan start och ändra till katalogen; Exempel:
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   -- eller--
   ```shell
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. Klona exempelprojektet [källan start med Docker] till den katalog som du skapade; Exempel:
   ```shell
   git clone https://github.com/microsoft/gs-spring-boot-docker
   ```

1. Ändra katalogen till slutförda projektet. Exempel:
   ```shell
   cd gs-spring-boot-docker/complete
   ```

1. Skapa JAR-filen med Maven; Exempel:
   ```shell
   mvn clean package
   ```

1. När webbappen har skapats, starta webbprogram med Maven; Exempel:
   ```shell
   mvn spring-boot:run
   ```

1. Testa webbappen genom att bläddra till den lokalt med hjälp av en webbläsare. Du kan exempelvis använda följande kommando om du har curl som är tillgängliga:
   ```shell
   curl http://localhost:8080
   ```

1. Du bör se följande meddelande visas: **Hello Docker World**

## <a name="create-an-azure-service-principal"></a>Skapa en Azure huvudnamn för tjänsten

I det här avsnittet skapar du en Azure tjänstens huvudnamn som Maven plugin-programmet använder när du distribuerar din behållare till Azure.

1. Öppna en kommandotolk.

1. Logga in på ditt Azure-konto med hjälp av Azure CLI:
   ```shell
   az login
   ```
   Följ instruktionerna för att slutföra inloggningen.

1. Skapa en Azure-tjänstens huvudnamn:
   ```shell
   az ad sp create-for-rbac --name "uuuuuuuu" --password "pppppppp"
   ```
   Där `uuuuuuuu` är användarnamnet och `pppppppp` är lösenordet för tjänstens huvudnamn.

1. Azure svarar med JSON som liknar följande exempel:
   ```json
   {
      "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "displayName": "uuuuuuuu",
      "name": "http://uuuuuuuu",
      "password": "pppppppp",
      "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
   }
   ```

   > [!NOTE]
   >
   > Du kommer använda värden från den här JSON-svar när du konfigurerar Maven-plugin-programmet för att distribuera din behållare till Azure. Den `aaaaaaaa`, `uuuuuuuu`, `pppppppp`, och `tttttttt` är för platshållarvärden som används i det här exemplet för att göra det enklare att mappa värdena till deras respektive element när du konfigurerar din Maven `settings.xml` filen i nästa avsnitt .
   >
   >

## <a name="configure-maven-to-use-your-azure-service-principal"></a>Konfigurera Maven för att använda din Azure-tjänstens huvudnamn

I det här avsnittet använder du värdena från din Azure tjänstens huvudnamn konfigurera autentisering som Maven ska använda när du distribuerar din behållare till Azure.

1. Öppna din Maven `settings.xml` filen i en textredigerare; den här filen kan vara en sökväg som i följande exempel:
   * `/etc/maven/settings.xml`
   * `%ProgramFiles%\apache-maven\3.5.0\conf\settings.xml`
   * `$HOME/.m2/settings.xml`

1. Lägg till Azure-tjänstens huvudnamn inställningarna från föregående avsnitt i den här kursen och den `<servers>` samling i den *settings.xml* filen, till exempel:

   ```xml
   <servers>
      <server>
        <id>azure-auth</id>
         <configuration>
            <client>aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa</client>
            <tenant>tttttttt-tttt-tttt-tttt-tttttttttttt</tenant>
            <key>pppppppp</key>
            <environment>AZURE</environment>
         </configuration>
      </server>
   </servers>
   ```
   Där:
   Element | Beskrivning
   ---|---|---
   `<id>` | Anger ett unikt namn som Maven använder för att leta upp dina säkerhetsinställningar när du distribuerar ditt webbprogram till Azure.
   `<client>` | Innehåller den `appId` värde från tjänstens huvudnamn.
   `<tenant>` | Innehåller den `tenant` värde från tjänstens huvudnamn.
   `<key>` | Innehåller den `password` värde från tjänstens huvudnamn.
   `<environment>` | Definierar målmiljön Azure-molnet, vilket är `AZURE` i det här exemplet. (En fullständig lista över miljöer finns i den [Maven-plugin-programmet för webbprogrammet för behållare] dokumentation)

1. Spara och Stäng den *settings.xml* fil.

## <a name="optional-deploy-your-local-docker-file-to-docker-hub"></a>Valfritt: Distribuera din lokala Docker-fil till Docker-hubb

Om du har en Docker-konto du push till Docker-NAV och skapa din Docker behållaren image lokalt. Om du vill göra det använder du följande steg.

1. Öppna den `pom.xml` filen för tillämpningsprogrammet källan Start i en textredigerare.

1. Leta upp den `<imageName>` underordnat element för den `<containerSettings>` element.

1. Uppdatering av `${docker.image.prefix}` värdet med namnet på ditt Docker:
   ```xml
   <containerSettings>
      <imageName>mydockeraccountname/${project.artifactId}</imageName>
   </containerSettings>
   ```

1. Välj något av följande distributionsmetoder:

   * Skapa behållaren avbildningen lokalt med Maven, och sedan använda Docker för att skicka din behållare till Docker-hubb:
      ```shell
      mvn clean package docker:build
      docker push
      ```

   * Om du har den [Docker-plugin för Maven] installerat, kan du automatiskt bygga och din behållaren bild till Docker-hubben med hjälp av den `-DpushImage` parameter:
      ```shell
      mvn clean package docker:build -DpushImage
      ```

## <a name="optional-customize-your-pomxml-before-deploying-your-container-to-azure"></a>Valfritt: Anpassa din pom.xml innan du distribuerar din behållare till Azure

Öppna den `pom.xml` filen för tillämpningsprogrammet källan Start i en textredigerare och leta upp den `<plugin>` element för `azure-webapp-maven-plugin`. Det här elementet bör likna följande exempel:

   ```xml
   <plugin>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-webapp-maven-plugin</artifactId>
      <version>0.1.3</version>
      <configuration>
         <authentication>
            <serverId>azure-auth</serverId>
         </authentication>
         <resourceGroup>maven-plugin</resourceGroup>
         <appName>maven-linux-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <containerSettings>
            <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         </containerSettings>
         <appSettings>
            <property>
               <name>PORT</name>
               <value>8080</value>
            </property>
         </appSettings>
      </configuration>
   </plugin>
   ```

Det finns flera värden som du kan ändra för Maven plugin-programmet och en utförlig beskrivning för var och en av dessa element är tillgänglig i den [Maven-plugin-programmet för webbprogrammet för behållare] dokumentation. Som som säger att det finns flera värden som är värda markering i den här artikeln:

Element | Beskrivning
---|---|---
`<version>` | Anger versionen av den [Maven-plugin-programmet för webbprogrammet för behållare]. Du bör kontrollera versionen som anges i den [Maven centrala lagringsplatsen](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-webapp-maven-plugin%22) så att du använder den senaste versionen.
`<authentication>` | Anger autentiseringsinformationen för Azure, som i det här exemplet innehåller en `<serverId>` element som innehåller `azure-auth`; Maven används värdet för att slå upp Azure tjänstens huvudnamn värden i din Maven *settings.xml* fil som du har definierat i en tidigare i den här artikeln.
`<resourceGroup>` | Anger målresursgruppen som är `maven-plugin` i det här exemplet. Resursgruppens namn kommer att skapas under distributionen om det inte redan finns.
`<appName>` | Anger målnamn för ditt webbprogram. I det här exemplet är målnamnet `maven-linux-app-${maven.build.timestamp}`, där den `${maven.build.timestamp}` suffix läggs till i det här exemplet för att undvika konflikter. (Tidsstämpeln är valfria och du kan ange en unik sträng för namnet på appen.)
`<region>` | Anger målregionen, som i det här exemplet är `westus`. (En fullständig lista finns i den [Maven-plugin-programmet för webbprogrammet för behållare] dokumentation.)
`<appSettings>` | Anger alla unika inställningar för Maven ska användas när du distribuerar ditt webbprogram till Azure. I det här exemplet en `<property>` elementet innehåller ett namn/värde-par för underordnade element som anger porten för din app.

> [!NOTE]
>
> Inställningar för att ändra portnumret i det här exemplet är bara nödvändigt när du ändrar porten från standard.
>

## <a name="build-and-deploy-your-container-to-azure"></a>Skapa och distribuera din behållare till Azure

När du har konfigurerat alla inställningar i föregående avsnitt i den här artikeln, är du redo att distribuera din behållare till Azure. Så här loggar du in:

1. Från Kommandotolken eller terminalfönster som du använde tidigare, återskapa JAR-filen med Maven om du har gjort ändringar i *pom.xml* filen, till exempel:
   ```shell
   mvn clean package
   ```

1. Distribuera webbappen till Azure med hjälp av Maven; Exempel:
   ```shell
   mvn azure-webapp:deploy
   ```

Maven ska distribuera webbappen till Azure; Om webbappen inte redan finns, skapas.

> [!NOTE]
>
> Om den region som du anger i den `<region>` element i din *pom.xml* filen har inte tillräckligt många servrar som är tillgängliga när du börjar din distribution, kan uppstå ett fel som liknar följande exempel:
>
> ```bash
> [INFO] Start deploying to Web App maven-linux-app-20170804...
> [INFO] ------------------------------------------------------------------------
> [INFO] BUILD FAILURE
> [INFO] ------------------------------------------------------------------------
> [INFO] Total time: 31.059 s
> [INFO] Finished at: 2017-08-04T12:15:47-07:00
> [INFO] Final Memory: 51M/279M
> [INFO] ------------------------------------------------------------------------
> [ERROR] Failed to execute goal com.microsoft.azure:azure-webapp-maven-plugin:0.1.3:deploy (default-cli) on project gs-spring-boot-docker: null: MojoExecutionException: CloudException: OnError while emitting onNext value: retrofit2.Response.class
> ```
>
> Om det händer kan du ange en annan region och kör igen Maven-kommando för att distribuera ditt program.
>
>

När webbplatsen har distribuerats, kommer du att kunna hantera den med hjälp av den [Azure-portalen].

* Ditt webbprogram visas i **Apptjänster**:

   ![Webbprogram som anges i Azure portal Apptjänster][AP01]

* Och URL: en för ditt webbprogram kommer att visas i den **översikt** för ditt webbprogram:

   ![Bestämma URL: en för webbappen][AP02]

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

Mer information om de olika tekniker som beskrivs i den här artikeln finns i följande artiklar:

* [Maven-plugin-programmet för webbprogrammet för behållare]

* [Logga in till Azure från Azure CLI](/azure/xplat-cli-connect)

* [Hur du använder Maven plugin-programmet för webbprogrammet för behållare för att distribuera en start av vår app till Azure App Service på Linux](../app-service-web-deploy-spring-boot-app-with-maven-plugin.md)

* [Skapa en Azure tjänstens huvudnamn med Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Referens för maven](https://maven.apache.org/settings.html)

* [Docker-plugin för Maven]

<!-- URL List -->

[Azure-kommandoradsgränssnittet (CLI)]: /cli/azure/overview
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Azure-portalen]: https://portal.azure.com/
[Docker]: https://www.docker.com/
[Docker-plugin för Maven]: https://github.com/spotify/docker-maven-plugin
[kostnadsfritt Azure-konto]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[MSDN-prenumerantförmåner]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker Getting Started]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/
[Maven-plugin-programmet för webbprogrammet för behållare]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin

<!-- IMG List -->

[AP01]: ./media/app-service-web-deploy-containerized-spring-boot-app-with-maven-plugin/AP01.png
[AP02]: ./media/app-service-web-deploy-containerized-spring-boot-app-with-maven-plugin/AP02.png
