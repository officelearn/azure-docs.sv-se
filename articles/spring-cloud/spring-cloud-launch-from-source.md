---
title: Snabbstart - Starta ditt Spring Cloud-program från källkod
description: I den här snabbstarten kan du lära dig hur du startar ditt Azure Spring Cloud-program direkt från källkoden
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: brendm
ms.openlocfilehash: 3ab4b1729ea380671b72a9bb01740930a186d5c3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79470802"
---
# <a name="quickstart-launch-your-spring-cloud-application-from-source-code"></a>Snabbstart: Starta ditt Spring Cloud-program från källkoden

Med Azure Spring Cloud kan du enkelt köra Spring Cloud-baserade mikrotjänstprogram på Azure.

Med Azure Spring Cloud kan du starta ditt program direkt från din java-källkod eller från en färdigbyggd JAR. I den här artikeln får du hjälp med nödvändiga steg.

Efter den här snabbstarten får du lära dig hur du:

> [!div class="checklist"]
> * Etablera en tjänstinstans
> * Ange en konfigurationsserver för en instans
> * Skapa ett mikrotjänstprogram lokalt
> * Distribuera varje mikrotjänst
> * Tilldela offentlig slutpunkt för ditt program

## <a name="prerequisites"></a>Krav

>[!Note]
> Azure Spring Cloud erbjuds för närvarande som en offentlig förhandsversion. Offentliga förhandsversioner gör det möjligt för kunder att experimentera med nya funktioner innan de släpps officiellt.  Offentliga förhandsversionsfunktioner och tjänster är inte avsedda för produktionsanvändning.  Mer information om support under förhandsversioner finns i våra [vanliga frågor](https://azure.microsoft.com/support/faq/) och svar eller en [supportförfrågan för](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) att få veta mer.

Innan du börjar bör du se till att din Azure-prenumeration har de beroenden som krävs:

1. [Installera Git](https://git-scm.com/)
2. [Installera JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Installera Maven 3.0 eller högre](https://maven.apache.org/download.cgi)
4. [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln.  Den har vanliga Azure-verktyg förinstallerade, inklusive de senaste versionerna av Git, JDK, Maven och Azure CLI. Om du är inloggad på din Azure-prenumeration startar du ditt [Azure Cloud Shell](https://shell.azure.com) från shell.azure.com.  Du kan läsa mer om Azure Cloud Shell genom [att läsa vår dokumentation](../cloud-shell/overview.md)

## <a name="install-the-azure-cli-extension"></a>Installera Azure CLI-tillägget

Installera Azure Spring Cloud-tillägget för Azure CLI med följande kommando

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Etablera en tjänstinstans med Azure CLI

Logga in på Azure CLI och välj din aktiva prenumeration. Var noga med att välja den aktiva prenumerationen som är vitlistad för Azure Spring Cloud

```azurecli
az login
az account list -o table
az account set --subscription
```

Skapa en resursgrupp som innehåller din Azure Spring Cloud-tjänst. Du kan läsa mer om [Azure Resource Groups](../azure-resource-manager/management/overview.md).

```azurecli
az group create --location eastus --name <resource group name>
```

Kör följande kommandon för att etablera en instans av Azure Spring Cloud. Förbered ett namn för din Azure Spring Cloud-tjänst. Namnet måste vara mellan 4 och 32 tecken långt och får bara innehålla gemener, siffror och bindestreck. Det första tecknet i tjänstnamnet måste vara en bokstav och det sista tecknet måste vara antingen en bokstav eller ett nummer.

```azurecli
az spring-cloud create -n <resource name> -g <resource group name>
```

Tjänstinstansen tar cirka fem minuter att distribuera.

Ange standardnamn och klusternamn för resursgrupper med hjälp av följande kommandon:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=provision)

## <a name="create-the-spring-cloud-application"></a>Skapa springmoln-programmet

Följande kommando skapar ett Spring Cloud-program i din prenumeration.  Detta skapar en tom Spring Cloud-tjänst som vi kan ladda upp vår applikation till.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Distribuera ditt Spring Cloud-program

Du kan distribuera ditt program från en förbyggd JAR eller från en Gradle- eller Maven-databas.  Hitta instruktioner för varje ärende nedan.

### <a name="deploy-a-built-jar"></a>Distribuera en inbyggd JAR

Om du vill distribuera från en JAR som är byggd på din lokala dator, se till att din konstruktion ger en [fett-JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven).

Så här distribuerar du fett-JAR till en aktiv distribution

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

Så här distribuerar du fett-JAR till en viss distribution

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>Distribuera från källkod

Azure Spring Cloud använder [kpack](https://github.com/pivotal/kpack) för att skapa ditt projekt.  Du kan använda Azure CLI för att ladda upp källkoden, skapa ditt projekt med kpack och distribuera den till målprogrammet.

> [!WARNING]
> Projektet får bara producera en `main-class` JAR-fil `MANIFEST.MF` `target` med en post i `build/libs` (för Maven-distributioner eller (för Gradle-distributioner).  Flera JAR-filer med `main-class` poster kommer att orsaka distributionen misslyckas.

För enmodul Maven / Gradle projekt:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

För Maven / Gradle projekt med flera moduler, upprepa för varje modul:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Visa distributionsloggar

Granska kpack-byggloggarna med följande kommando:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> Kpack-loggarna visar bara den senaste distributionen om distributionen har skapats från källan med kpack.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Tilldela en offentlig slutpunkt till gatewayen

1. Öppna sidan **Programinstrumentpanel.**
2. Välj `gateway` det program som ska visas på sidan **Programinformation.**
3. Välj **Tilldela domän** om du vill tilldela en offentlig slutpunkt till gatewayen. Detta kan några minuter. 
4. Ange den tilldelade offentliga IP-adressen i webbläsaren för att visa ditt program som körs.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

> [!div class="checklist"]
> * Etablera en tjänstinstans
> * Ange en konfigurationsserver för en instans
> * Skapa ett mikrotjänstprogram lokalt
> * Distribuera varje mikrotjänst
> * Redigera miljövariabler för program
> * Tilldela offentlig IP för programgatewayen

> [!div class="nextstepaction"]
> [Förbered ditt Azure Spring Cloud-program för distribution](spring-cloud-tutorial-prepare-app-deployment.md)

Fler exempel finns på GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
