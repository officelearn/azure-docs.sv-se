---
title: "Hur du använder Maven-plugin-program för Azure Web Apps för att distribuera en källan Start-app i Azure Container registret till Azure App Service"
description: "Den här självstudiekursen vägleder dig genom stegen för att distribuera en källan startprogrammet i registret för Azure-behållare till Azure till Azure App Service med hjälp av ett Maven plugin-program."
services: 
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/07/2017
ms.author: robmcm;kevinzha
ms.openlocfilehash: b087003b3a1e236e4a306678904107b8bf99395e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-maven-plugin-for-azure-web-apps-to-deploy-a-spring-boot-app-in-azure-container-registry-to-azure-app-service"></a>Hur du använder Maven-plugin-program för Azure Web Apps för att distribuera en källan Start-app i Azure Container registret till Azure App Service

Den  **[Vårversionen Framework]**  ett populära ramverk för öppen källkod som gör att Java-utvecklare skapa webb-, mobil- och API-program. Den här kursen använder en exempelapp som skapats med hjälp av [Vårversionen Start], en konvention inriktat tillvägagångssätt för att komma igång snabbt med källan.

Den här artikeln visar hur du distribuerar ett exempelprogram källan Start till registret för Azure-behållaren och sedan distribuera programmet till Azure App Service med hjälp av plugin-program för Maven för Azure Web Apps.

> [!NOTE]
>
> Maven-plugin-program för Azure Web Apps är tillgänglig som en förhandsgranskning. För närvarande stöds endast FTP-publicering, även om ytterligare funktioner som är tänkta för framtiden.
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

## <a name="clone-the-sample-spring-boot-on-docker-web-app"></a>Klona exempel källan Start på Docker-webbprogram

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

1. Klona den [Vårversionen Start på Docker komma igång] exempelprojektet till den katalog som du har skapat, till exempel:
   ```shell
   git clone -b private-registry https://github.com/Microsoft/gs-spring-boot-docker
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

   ![Bläddra Sample-appen lokalt][SB01]

## <a name="create-an-azure-service-principal"></a>Skapa en Azure huvudnamn för tjänsten

I det här avsnittet skapar du en Azure tjänstens huvudnamn som Maven plugin-programmet använder när du distribuerar din behållare till Azure.

1. Öppna en kommandotolk.

1. Logga in på ditt Azure-konto med hjälp av Azure CLI:
   ```azurecli
   az login
   ```
   Följ instruktionerna för att slutföra inloggningen.

1. Skapa en Azure-tjänstens huvudnamn:
   ```azurecli
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

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Skapa ett Azure Container registret med hjälp av Azure CLI

1. Öppna en kommandotolk.

1. Logga in på ditt Azure-konto:
   ```azurecli
   az login
   ```

1. Skapa en resursgrupp för Azure-resurser du vill använda i den här artikeln:
   ```azurecli
   az group create --name=wingtiptoysresources --location=westus
   ```
   Ersätt `wingtiptoysresources` i det här exemplet med ett unikt namn för resursgruppen.

1. Skapa ett register för privat Azure-behållaren i resursgruppen för vår Start appen: 
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoysresources --location westus --name wingtiptoysregistry --sku Basic
   ```
   Ersätt `wingtiptoysregistry` i det här exemplet med ett unikt namn för behållaren registret.

1. Hämta lösenordet för behållaren registret:
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```
   Azure kommer att svara med ditt lösenord. Exempel:
   ```json
   {
      "name": "password",
      "value": "xxxxxxxxxx"
   }
   ```

## <a name="add-your-azure-container-registry-and-azure-service-principal-to-your-maven-settings"></a>Lägg till Azure-behållaren registret och Azure tjänstens huvudnamn Maven-inställningar

1. Öppna din Maven `settings.xml` filen i en textredigerare; den här filen kan vara en sökväg som i följande exempel:
   * `/etc/maven/settings.xml`
   * `%ProgramFiles%\apache-maven\3.5.0\conf\settings.xml`
   * `$HOME/.m2/settings.xml`

1. Lägga till din Azure-behållare åtkomst registerinställningar från föregående avsnitt i den här artikeln i `<servers>` samling i den *settings.xml* filen, till exempel:

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>xxxxxxxxxx</password>
      </server>
   </servers>
   ```
   Där:
   Element | Beskrivning
   ---|---|---
   `<id>` | Innehåller namnet på behållaren för privat Azure-registret.
   `<username>` | Innehåller namnet på behållaren för privat Azure-registret.
   `<password>` | Innehåller det lösenord som du hämtade i föregående avsnitt i den här artikeln.

1. Lägg till Azure-tjänstens huvudnamn inställningarna från ett tidigare avsnitt i den här artikeln i `<servers>` samling i den *settings.xml* filen, till exempel:

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
   `<environment>` | Definierar målmiljön Azure-molnet, vilket är `AZURE` i det här exemplet. (En fullständig lista över miljöer finns i den [Maven plugin-program för Azure Web Apps] dokumentation)

1. Spara och Stäng den *settings.xml* fil.

## <a name="build-your-docker-container-image-and-push-it-to-your-azure-container-registry"></a>Skapa din Docker behållaren image och push-installera den Azure-behållaren registret

1. Navigera till slutförda projektkatalogen för tillämpningsprogrammet källan start (t.ex.) ”*C:\SpringBoot\gs-spring-boot-docker\complete*” eller ”*/users/robert/SpringBoot/gs-spring-boot-docker/complete*”), och öppna den *pom.xml* fil med en text redigeraren.

1. Uppdatering av `<properties>` samling i den *pom.xml* fil med inloggningen servervärdet för registernyckeln din Azure-behållare från föregående avsnitt i den här kursen; till exempel:

   ```xml
   <properties>
      <azure.containerRegistry>wingtiptoysregistry</azure.containerRegistry>
      <docker.image.prefix>${azure.containerRegistry}.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <maven.build.timestamp.format>yyyyMMddHHmmssSSS</maven.build.timestamp.format>
   </properties>
   ```
   Där:
   Element | Beskrivning
   ---|---|---
   `<azure.containerRegistry>` | Anger namnet på behållaren för privat Azure-registret.
   `<docker.image.prefix>` | Anger Webbadressen för privat Azure-behållaren registret, som hämtas genom att lägga till ”. azurecr.io” till namnet på din privata behållare registret.

1. Kontrollera att `<plugin>` för Docker-plugin-programmet i din *pom.xml* filen innehåller rätt egenskaper för server-adress och registret inloggningsnamnet från föregående steg i den här kursen. Exempel:

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <registryUrl>https://${docker.image.prefix}</registryUrl>
         <serverId>${azure.containerRegistry}</serverId>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
      </configuration>
   </plugin>
   ```
   Där:
   Element | Beskrivning
   ---|---|---
   `<serverId>` | Anger den egenskap som innehåller namnet på behållaren för privat Azure-registret.
   `<registryUrl>` | Anger den egenskap som innehåller URL för privat Azure-behållaren registret.

1. Navigera till slutförda projektkatalogen för tillämpningsprogrammet källan start och kör följande kommando för att återskapa programmet och push-behållaren i Azure-behållaren registret:

   ```
   mvn package docker:build -DpushImage 
   ```

1. Valfritt: Bläddra till den [Azure-portalen] och kontrollera att det finns Docker behållare bild med namnet **gs-källan-Start-docker** i behållaren-registret.

   ![Kontrollera behållare i Azure-portalen][CR01]

## <a name="customize-your-pomxml-then-build-and-deploy-your-container-to-azure"></a>Anpassa din pom.xml och sedan skapa och distribuera din behållare till Azure

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
         <resourceGroup>wingtiptoysresources</resourceGroup>
         <appName>maven-linux-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <containerSettings>
            <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
            <registryUrl>https://${docker.image.prefix}</registryUrl>
            <serverId>${azure.containerRegistry}</serverId>
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

Det finns flera värden som du kan ändra för Maven plugin-programmet och en utförlig beskrivning för var och en av dessa element är tillgänglig i den [Maven plugin-program för Azure Web Apps] dokumentation. Som som säger att det finns flera värden som är värda markering i den här artikeln:

Element | Beskrivning
---|---|---
`<version>` | Anger versionen av den [Maven plugin-program för Azure Web Apps]. Du bör kontrollera versionen som anges i den [Maven centrala lagringsplatsen](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-webapp-maven-plugin%22) så att du använder den senaste versionen.
`<authentication>` | Anger autentiseringsinformationen för Azure, som i det här exemplet innehåller en `<serverId>` element som innehåller `azure-auth`; Maven används värdet för att slå upp Azure tjänstens huvudnamn värden i din Maven *settings.xml* fil som du har definierat i en tidigare i den här artikeln.
`<resourceGroup>` | Anger målresursgruppen som är `wingtiptoysresources` i det här exemplet. Resursgruppens namn kommer att skapas under distributionen om det inte redan finns.
`<appName>` | Anger målnamn för ditt webbprogram. I det här exemplet är målnamnet `maven-linux-app-${maven.build.timestamp}`, där den `${maven.build.timestamp}` suffix läggs till i det här exemplet för att undvika konflikter. (Tidsstämpeln är valfria och du kan ange en unik sträng för namnet på appen.)
`<region>` | Anger målregionen, som i det här exemplet är `westus`. (En fullständig lista finns i den [Maven plugin-program för Azure Web Apps] dokumentation.)
`<containerSettings>` | Anger de egenskaper som innehåller namn och URL för din behållare.
`<appSettings>` | Anger alla unika inställningar för Maven ska användas när du distribuerar ditt webbprogram till Azure. I det här exemplet en `<property>` elementet innehåller ett namn/värde-par för underordnade element som anger porten för din app.

> [!NOTE]
>
> Inställningar för att ändra portnumret i det här exemplet är bara nödvändigt när du ändrar porten från standard.
>

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
> ```
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

## <a name="next-steps"></a>Nästa steg

Mer information om de olika tekniker som beskrivs i den här artikeln finns i följande artiklar:

* [Maven plugin-program för Azure Web Apps]

* [Logga in till Azure från Azure CLI](/azure/xplat-cli-connect)

* [Skapa en Azure tjänstens huvudnamn med Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Referens för maven](https://maven.apache.org/settings.html)

* [Docker-plugin för Maven]

<!-- URL List -->

[Azure-kommandoradsgränssnittet (CLI)]: /cli/azure/overview
[Azure Container Service (ACS)]: https://azure.microsoft.com/services/container-service/
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Azure-portalen]: https://portal.azure.com/
[Maven plugin-program för Azure Web Apps]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Using a custom Docker image for Azure Web App on Linux]: tutorial-custom-docker-image.md
[Docker]: https://www.docker.com/
[Docker-plugin för Maven]: https://github.com/spotify/docker-maven-plugin
[kostnadsfritt Azure-konto]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[MSDN-prenumerantförmåner]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Vårversionen Start]: http://projects.spring.io/spring-boot/
[Vårversionen Start på Docker komma igång]: https://github.com/spring-guides/gs-spring-boot-docker
[Vårversionen Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/SB01.png
[CR01]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/CR01.png
[AP01]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/AP01.png
[AP02]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/AP02.png
