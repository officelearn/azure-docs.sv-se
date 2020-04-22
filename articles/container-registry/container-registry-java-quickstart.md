---
title: Snabbstart - Skapa och skicka Java-behållaravbildningar till Azure Container Registry med Maven och Jib
description: Skapa en behållarad Java-app och skicka den till Azure Container Registry med hjälp av Maven Jib-insticksprogrammet.
author: KarlErickson
ms.author: karler
ms.topic: quickstart
ms.date: 02/26/2020
ms.openlocfilehash: ef933ae9a6b0a34529c7ec145b13c023728a3457
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731859"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Snabbstart: Skapa och skicka Java-behållaravbildningar till Azure Container Registry

Den här snabbstarten visar hur du skapar en behållarad Java-app och skickar den till Azure Container Registry med hjälp av Maven Jib-insticksprogrammet. Användningen av Maven och Jib är ett exempel på att använda utvecklarverktyg för att interagera med ett Azure-behållarregister.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte redan har en Azure-prenumeration kan du aktivera din [MSDN-prenumerantförmån](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/pricing/free-trial).
* [Azures kommandoradsgränssnitt (CLI)](/cli/azure/overview).
* Ett Java Development Kit (JDK) som stöds. Mer information om vilka JDK:s som är tillgängliga för användning när du utvecklar på Azure finns i <https://aka.ms/azure-jdks>.
* Apaches [Maven](http://maven.apache.org) bygga verktyg (Version 3 eller högre).
* En [Git](https://git-scm.com)-klient.
* En [Docker](https://www.docker.com)-klient.
* [Hjälpen för ACR Docker-autentiseringsuppgifter](https://github.com/Azure/acr-docker-credential-helper).

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Skapa webbappen Spring Boot on Docker Getting Started

Med följande steg skapar du en Spring Boot-webbapp och testar den lokalt.

1. Från kommandotolken använder du följande kommando för att klona [exempelprojektet Spring Boot on Docker Komma igång.](https://github.com/spring-guides/gs-spring-boot-docker)

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Byt katalog till det slutförda projektet.

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Använd Maven till att kompilera och köra exempelappen.

   ```bash
   mvn package spring-boot:run
   ```

1. Testa webbappen genom att gå till `http://localhost:8080`, eller med följande `curl`-kommando:

   ```bash
   curl http://localhost:8080
   ```

Du bör se följande meddelande visas: **Hello Docker World**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Skapa ett Azure-containerregister med hjälp av Azure CLI

Därefter ska du skapa en Azure-resursgrupp och din ACR med hjälp av följande steg:

1. Logga in på ditt Azure-konto med följande kommando:

   ```azurecli
   az login
   ```

1. Ange vilken Azure-prenumeration som ska användas:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Skapa en resursgrupp för de Azure-resurser som används i den här självstudien. I följande kommando måste du ersätta platshållarna med ditt eget resursnamn och en plats som `eastus`.

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Skapa ett privat Azure-behållarregister i resursgruppen med hjälp av följande kommando. Var noga med att ersätta platshållarna med faktiska värden. Självstudien skickar exempelappen som en Docker-avbildning till det här registret under senare steg.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Skicka appen till containerregistret via Jib

Slutligen ska du uppdatera projektkonfigurationen och använda kommandotolken för att skapa och distribuera avbildningen.

1. Logga in på ditt Azure Container-register från Azure CLI med följande kommando. Var noga med att ersätta platshållaren med ditt eget registernamn.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   Kommandot `az configure` anger det standardregisternamn som `az acr` ska användas med kommandon.

1. Gå till det slutförda projektet för ditt Spring Boot-program (till exempel, "*C:\SpringBoot\gs-spring-boot-docker\complete*" eller "*/users/robert/SpringBoot/gs-spring-boot-docker/complete*") och öppna *pom.xml*-filen med en textredigerare.

1. Uppdatera `<properties>` samlingen i *filen pom.xml* med följande XML. Ersätt platshållaren med ditt registernamn `<jib-maven-plugin.version>` och uppdatera värdet med den senaste versionen av [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin).

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <jib-maven-plugin.version>1.8.0</jib-maven-plugin.version>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Uppdatera `<plugins>` samlingen i *filen pom.xml* `<plugin>` så att elementet `jib-maven-plugin`innehåller och post för , som visas i följande exempel. Observera att vi använder en basavbildning från MCR `mcr.microsoft.com/java/jdk:8-zulu-alpine`(Microsoft Container Registry): , som innehåller en officiellt stödd JDK för Azure. För andra MCR-basbilder med officiellt stödda JDK: er, se [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre), [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless)och [Java SE JDK och Maven](https://hub.docker.com/_/microsoft-java-maven).

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Navigera till den slutförda projektkatalogen för ditt Spring Boot-program och kör följande kommando för att skapa avbildningen och push-överföra den till registret:

   ```bash
   mvn compile jib:build
   ```

> [!NOTE]
>
> Av säkerhetsskäl är den autentiseringsdag som skapats av `az acr login` endast giltig i 1 timme. Om du får ett *401 Obehörigt* `az acr login -n <your registry name>` fel kan du köra kommandot igen för att återauktorisera.

## <a name="verify-your-container-image"></a>Verifiera behållaravbildningen

Grattis! Nu har du din containerized Java App bygga på Azure stöds JDK skjuts till din ACR. Du kan nu testa avbildningen genom att distribuera den till Azure App Service, eller dra den till lokal med kommandot (ersätter platshållaren):

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker:v1
```

## <a name="next-steps"></a>Nästa steg

Andra versioner av de officiella Java-basavbildningarna som stöds av Microsoft finns i:

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE Huvudlös JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK och Maven](https://hub.docker.com/_/microsoft-java-maven)

Om du vill veta mer om Spring och Azure kan du fortsätta till dokumentationscentret för Spring i Azure.

> [!div class="nextstepaction"]
> [Spring på Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i följande resurser:

* [Azure för Java-utvecklare](/azure/java)
* [Arbeta med Azure DevOps och Java](/azure/devops/java)
* [Spring Boot on Docker Getting Started](https://spring.io/guides/gs/spring-boot-docker)
* [Spring Initializr](https://start.spring.io)
* [Distribuera ett Spring Boot-program till Azure App Service](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin)
* [Använda en anpassad Docker-avbildning för Azure Web App i Linux](/azure/app-service-web/app-service-linux-using-custom-docker-image)
