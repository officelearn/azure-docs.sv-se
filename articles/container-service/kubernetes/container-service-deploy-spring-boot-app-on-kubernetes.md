---
title: "Distribuera en App för start av värdet på Kubernetes i Azure Container Service | Microsoft Docs"
description: "Den här kursen får du om stegen för att distribuera en källan startprogrammet i en Kubernetes kluster på Microsoft Azure."
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
ms.openlocfilehash: 7f726436b2d459b8c16abb02e07de099abfd8974
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="deploy-a-spring-boot-application-on-a-kubernetes-cluster-in-the-azure-container-service"></a>Distribuera ett Spring Boot-program på ett Kubernetes-kluster i Azure Container Service

Den  **[Vårversionen Framework]**  ett populära ramverk för öppen källkod som gör att Java-utvecklare skapa webb-, mobil- och API-program. Den här kursen använder en exempelapp som skapats med hjälp av [Vårversionen Start], en konvention inriktat tillvägagångssätt för att komma igång snabbt med källan.

**[Kubernetes]**  och  **[Docker]**  är öppen källkod som hjälper utvecklare att automatisera distribution, skalning och hantering av de program som körs i behållare.

Den här vägledningen visar dig om att kombinera dessa två populära, öppen källkod tekniker för att utveckla och distribuera en startprogrammet för källan till Microsoft Azure. Mer specifikt du använder  *[Vårversionen Start]*  för programutveckling,  *[Kubernetes]*  för distribution av behållaren och [Azure Container Service (ACS)] som värd för ditt program.

### <a name="prerequisites"></a>Krav

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

Följande steg beskriver hur du skapar ett webbprogram i vår start och testa dem lokalt.

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

1. Klona den [Vårversionen Start på Docker komma igång] exempelprojektet till katalogen.
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Ändra katalogen till projektet slutfördes.
   ```
   cd gs-spring-boot-docker
   cd complete
   ```

1. Använd Maven för att skapa och köra sample-appen.
   ```
   mvn package spring-boot:run
   ```

1. Testa webbappen genom att bläddra till http://localhost: 8080 eller med följande `curl` kommando:
   ```
   curl http://localhost:8080
   ```

1. Du bör se följande meddelande visas: **Hello Docker World**

   ![Bläddra Sample-appen lokalt][SB01]

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Skapa ett Azure Container registret med hjälp av Azure CLI

1. Öppna en kommandotolk.

1. Logga in på ditt Azure-konto:
   ```azurecli
   az login
   ```

1. Skapa en resursgrupp för Azure-resurser används i den här kursen.
   ```azurecli
   az group create --name=wingtiptoys-kubernetes --location=eastus
   ```

1. Skapa ett register för privat Azure-behållaren i resursgruppen. Självstudiekursen skickar sample-appen som en Docker-avbildning till den här registernyckeln i senare steg. Ersätt `wingtiptoysregistry` med ett unikt namn för registret.
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoys-kubernetes--location eastus \
    --name wingtiptoysregistry --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry"></a>Push-appen till behållaren registret

1. Navigera till konfigurationskatalogen för Maven-installationen (standard ~/.m2/ eller C:\Users\username\.m2) och öppna den *settings.xml* fil med en textredigerare.

1. Hämta lösenordet för behållaren registret från Azure CLI.
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```

   ```json
   {
  "name": "password",
  "value": "AbCdEfGhIjKlMnOpQrStUvWxYz"
   }
   ```

1. Lägg till ditt Azure-behållare registret id och lösenord till en ny `<server>` samling i den *settings.xml* fil.
Den `id` och `username` är namnet på registret. Använd den `password` värde från det föregående kommandot (utan citattecken).

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

1. Navigera till slutförda projektkatalogen för tillämpningsprogrammet källan start (till exempel ”*C:\SpringBoot\gs-spring-boot-docker\complete*” eller ”*/users/robert/SpringBoot/gs-spring-boot-docker/complete*”), och öppna den *pom.xml* fil med en textredigerare.

1. Uppdatering av `<properties>` samling i den *pom.xml* fil med inloggningen servervärdet för registernyckeln din Azure-behållare.

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Uppdatera den `<plugins>` samling i den *pom.xml* filen så att den `<plugin>` innehåller inloggningen adress och registret servernamnet för din Azure-behållare registernyckeln.

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

1. Navigera till slutförda projektkatalogen för tillämpningsprogrammet källan start och kör följande kommando för att skapa Docker-behållaren och push-avbildningen i registret:

   ```
   mvn package docker:build -DpushImage
   ```

> [!NOTE]
>
>  Du får ett felmeddelande som liknar något av följande när Maven skickar avbildningen till Azure:
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: no basic auth credentials`
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: Incomplete Docker registry authorization credentials. Please provide all of username, password, and email or none.`
>
> Om du får det här felet kan logga in Azure från Docker-kommandoraden.
>
> `docker login -u wingtiptoysregistry -p "AbCdEfGhIjKlMnOpQrStUvWxYz" wingtiptoysregistry.azurecr.io`
>
> Sedan push din behållare:
>
> `docker push wingtiptoysregistry.azurecr.io/gs-spring-boot-docker`

## <a name="create-a-kubernetes-cluster-on-acs-using-the-azure-cli"></a>Skapa ett Kubernetes kluster på ACS med hjälp av Azure CLI

1. Skapa ett Kubernetes-kluster i Azure Container Service. Följande kommando skapar en *kubernetes* klustret i den *VingspetsLeksaker kubernetes* resursen med *VingspetsLeksaker containerservice* som klusternamnet och *VingspetsLeksaker kubernetes* som DNS-prefix:
   ```azurecli
   az acs create --orchestrator-type=kubernetes --resource-group=wingtiptoys-kubernetes \ 
    --name=wingtiptoys-containerservice --dns-prefix=wingtiptoys-kubernetes
   ```
   Det här kommandot kan ta en stund att slutföra.

1. Installera `kubectl` med hjälp av Azure CLI. Linux-användare kan ha som prefix i det här kommandot med `sudo` eftersom den distribuerar Kubernetes CLI till `/usr/local/bin`.
   ```azurecli
   az acs kubernetes install-cli
   ```

1. Hämta konfigurationsinformation för klustret så att du kan hantera klustret från webbgränssnittet Kubernetes och `kubectl`. 
   ```azurecli
   az acs kubernetes get-credentials --resource-group=wingtiptoys-kubernetes  \ 
    --name=wingtiptoys-containerservice
   ```

## <a name="deploy-the-image-to-your-kubernetes-cluster"></a>Distribuera avbildningen till Kubernetes-kluster

Den här kursen distribuerar appen med `kubectl`, kan du utforska distributionens tillstånd via webbgränssnittet Kubernetes.

### <a name="deploy-with-the-kubernetes-web-interface"></a>Distribuera med webbgränssnitt Kubernetes

1. Öppna en kommandotolk.

1. Öppna webbplatsen konfiguration för klustret Kubernetes i standardwebbläsaren:
   ```
   az acs kubernetes browse --resource-group=wingtiptoys-kubernetes --name=wingtiptoys-containerservice
   ```

1. När webbplatsen Kubernetes configuration öppnas i webbläsaren, klicka på länken till **distribuera en behållare app**:

   ![Kubernetes Configuration webbplats][KB01]

1. När den **distribuera en behållare app** visas, anger du följande alternativ:

   a. Välj **ange information om appar nedan**.

   b. Ange programnamnet källan Start för den **appnamn**, till exempel ”:*gs-källan-Start-docker*”.

   c. Ange din inloggning server och en behållare avbildningen från tidigare för den **behållaren image**, till exempel ”:*wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest*”.

   d. Välj **externa** för den **tjänsten**.

   e. Ange din externa och interna portar i den **Port** och **mål port** textrutor.

   ![Kubernetes Configuration webbplats][KB02]


1. Klicka på **distribuera** att distribuera behållaren.

   ![Distribuera behållare][KB05]

1. När programmet har distribuerats visas källan Start programmet visas under **Services**.

   ![Kubernetes tjänster][KB06]

1. Om du klickar på länken för **externa slutpunkter**, du kan se din källan Start-program som körs på Azure.

   ![Kubernetes tjänster][KB07]

   ![Bläddra Sample-appen på Azure][SB02]


### <a name="deploy-with-kubectl"></a>Distribuera med kubectl

1. Öppna en kommandotolk.

1. Kör ditt behållare i Kubernetes klustret genom att använda den `kubectl run` kommando. Ange ett namn för din app i Kubernetes och fullständig avbildningens namn. Exempel:
   ```
   kubectl run gs-spring-boot-docker --image=wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest
   ```
   I det här kommandot:

   * Behållarens namn `gs-spring-boot-docker` anges omedelbart efter den `run` kommando

   * Den `--image` parametern anger kombinerade server och image inloggningsnamnet som`wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest`

1. Exponera Kubernetes klustret externt med hjälp av den `kubectl expose` kommando. Ange tjänstens namn och offentliga TCP-port som används för att komma åt appen interna målporten appen lyssnar på. Exempel:
   ```
   kubectl expose deployment gs-spring-boot-docker --type=LoadBalancer --port=80 --target-port=8080
   ```
   I det här kommandot:

   * Behållarens namn `gs-spring-boot-docker` anges omedelbart efter den `expose deployment` kommando

   * Den `--type` parametern anger att klustret använder belastningsutjämnare

   * Den `--port` parametern anger offentliga TCP-port 80. Du har åtkomst till appen på den här porten.

   * Den `--target-port` parametern anger internt TCP port 8080. Belastningsutjämnaren vidarebefordrar begäranden till din app på denna port.

1. När appen har distribuerats till klustret, fråga extern IP-adress och öppna den i webbläsaren:

   ```
   kubectl get services -o jsonpath={.items[*].status.loadBalancer.ingress[0].ip} --namespace=${namespace}
   ```

   ![Bläddra Sample-appen på Azure][SB02]


## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder värdet Start på Azure finns i följande artiklar:

* [Distribuera ett program för start av källan till Azure App Service](../../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)
* [Distribuera en källan startprogrammet på Linux i Azure Container Service](container-service-deploy-spring-boot-app-on-linux.md)

Mer information om hur du använder Azure med Java finns på [Azure Java Developer Center] och i [Java Tools för Visual Studio Team Services].

Mer information om källan Start på Docker exempelprojektet finns [Vårversionen Start på Docker komma igång].

Följande länkar innehåller ytterligare information om hur du skapar källan startprogram:

* Mer information om hur du skapar ett enkelt källan Start-program finns i vår Initializr på https://start.spring.io/.

Följande länkar innehåller ytterligare information om hur du använder Kubernetes med Azure:

* [Kom igång med ett Kubernetes kluster i Container Service](https://docs.microsoft.com/azure/container-service/container-service-kubernetes-walkthrough)
* [Med Azure Container Service Kubernetes webbgränssnittet](https://docs.microsoft.com/azure/container-service/container-service-kubernetes-ui)

Mer information om hur du använder Kubernetes kommandoradsgränssnittet finns i den **kubectl** användarhandboken på <https://kubernetes.io/docs/user-guide/kubectl/>.

Webbplatsen Kubernetes har flera artiklar som handlar om med bilder i privata register:

* [Konfigurera Service-konton för skida]
* [Namnområden]
* [Dra en bild från ett privat register]

Ytterligare exempel för hur du använder anpassade Docker-avbildningar med Azure finns [med hjälp av en anpassad Docker-avbildning för Azure Web App på Linux].

<!-- URL List -->

[Azure-kommandoradsgränssnittet (CLI)]: /cli/azure/overview
[Azure Container Service (ACS)]: https://azure.microsoft.com/services/container-service/
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Azure portal]: https://portal.azure.com/
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[med hjälp av en anpassad Docker-avbildning för Azure Web App på Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[kostnadsfritt Azure-konto]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools för Visual Studio Team Services]: https://java.visualstudio.com/
[Kubernetes]: https://kubernetes.io/
[Kubernetes Command-Line Interface (kubectl)]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[Maven]: http://maven.apache.org/
[MSDN-prenumerantförmåner]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Vårversionen Start]: http://projects.spring.io/spring-boot/
[Vårversionen Start på Docker komma igång]: https://github.com/spring-guides/gs-spring-boot-docker
[Vårversionen Framework]: https://spring.io/
[Konfigurera Service-konton för skida]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/
[Namnområden]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[Dra en bild från ett privat register]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/SB02.png

[AR01]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR01.png
[AR02]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR02.png
[AR03]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR03.png
[AR04]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR04.png

[KB01]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB01.png
[KB02]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB02.png
[KB03]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB03.png
[KB04]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB04.png
[KB05]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB05.png
[KB06]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB06.png
[KB07]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB07.png
