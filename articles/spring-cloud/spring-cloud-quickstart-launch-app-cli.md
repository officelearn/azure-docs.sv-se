---
title: 'Snabbstart: Starta ett Java Spring-program med Azure CLI'
description: I den här snabbstarten distribuerar du ett exempelprogram till Azure Spring Cloud på Azure CLI.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.openlocfilehash: c05e53bd8ad8ade8c1e42729f46c99a0059c4dce
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79470868"
---
# <a name="quickstart-launch-a-java-spring-application-using-the-azure-cli"></a>Snabbstart: Starta ett Java Spring-program med Azure CLI

Med Azure Spring Cloud kan du enkelt köra ett Spring Boot-baserat mikrotjänstprogram på Azure.

Den här snabbstarten visar hur du distribuerar ett befintligt Java Spring Cloud-program till Azure. När du är klar kan du fortsätta att hantera programmet via Azure CLI eller växla till att använda Azure-portalen.

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

>[!TIP]
> Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln.  Den har vanliga Azure-verktyg förinstallerade, inklusive de senaste versionerna av Git, JDK, Maven och Azure CLI. Om du är inloggad på din Azure-prenumeration startar du ditt [Azure Cloud Shell](https://shell.azure.com) från shell.azure.com.  Du kan läsa mer om Azure Cloud Shell genom [att läsa vår dokumentation](../cloud-shell/overview.md)

För att slutföra den här snabbstarten behöver du:

1. [Installera Git](https://git-scm.com/)
2. [Installera JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Installera Maven 3.0 eller högre](https://maven.apache.org/download.cgi)
4. [Installera Azure CLI version 2.0.67 eller senare](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Installera Azure CLI-tillägget

Installera Azure Spring Cloud-tillägget för Azure CLI med följande kommando

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-cli"></a>Etablera en tjänstinstans på Azure CLI

1. Logga in på Azure CLI och välj din aktiva prenumeration. Var noga med att välja den aktiva prenumerationen som är vitlistad för Azure Spring Cloud

    ```azurecli
        az login
        az account list -o table
        az account set --subscription <Name or ID of subscription from the last step>
    ```

2. Förbered ett namn för din Azure Spring Cloud-tjänst.  Namnet måste vara mellan 4 och 32 tecken långt och får bara innehålla gemener, siffror och bindestreck.  Det första tecknet i tjänstnamnet måste vara en bokstav och det sista tecknet måste vara antingen en bokstav eller ett nummer.

3. Skapa en resursgrupp som innehåller din Azure Spring Cloud-tjänst.

    ```azurecli
        az group create --location eastus --name <resource group name>
    ```

    Läs mer om [Azure Resource Groups](../azure-resource-manager/management/overview.md).

4. Öppna ett Azure CLI-fönster och kör följande kommandon för att etablera en instans av Azure Spring Cloud.

    ```azurecli
        az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    Tjänstinstansen tar cirka fem minuter att distribuera.

5. Ange standardnamn och klusternamn för resursgrupper med hjälp av följande kommandon:

    ```azurecli
        az configure --defaults group=<resource group name>
        az configure --defaults spring-cloud=<service instance name>
    ```

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=provision)

## <a name="setup-your-configuration-server"></a>Konfigurera konfigurationsservern

Uppdatera din konfigurationsserver med platsen för git-arkivet för vårt projekt:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=config-server)

## <a name="build-the-microservices-applications-locally"></a>Skapa mikrotjänstprogrammen lokalt

1. Skapa en ny mapp och klona exempelappdatabasen till ditt Azure Cloud-konto.  

    ```console
        mkdir source-code
        git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Ändra katalogen och bygg projektet.

    ```console
        cd piggymetrics
        mvn clean package -D skipTests
    ```

Det tar cirka 5 minuter att sammanställa projektet.  När du är klar bör du ha enskilda JAR-filer för varje tjänst i sina respektive mappar.

## <a name="create-the-microservices"></a>Skapa mikrotjänster

Skapa mikrotjänster i Spring Cloud med hjälp av JAR-filer som skapats i föregående steg. Du skapar tre mikrotjänster: **gateway,** **auth-service**och **kontotjänst**.

```azurecli
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

## <a name="deploy-applications-and-set-environment-variables"></a>Distribuera program och ange miljövariabler

Vi måste distribuera våra program till Azure. Använd följande kommandon för att distribuera alla tre programmen:

```azurecli
az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
```

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=deploy)

## <a name="assign-public-endpoint-to-gateway"></a>Tilldela offentlig slutpunkt till gateway

Vi behöver ett sätt att komma åt programmet via en webbläsare. Vårt gateway-program behöver en offentlig slutpunkt.

1. Tilldela slutpunkten med följande kommando:

```azurecli
az spring-cloud app update -n gateway --is-public true
```

2. Fråga **gateway-programmet** för dess offentliga IP så att du kan kontrollera att programmet körs:

Linux:

```azurecli
az spring-cloud app show --name gateway | grep url
```

Windows:

```azurecli
az spring-cloud app show -s <service name> -g <resource group> -n gateway -o table
```

3. Navigera till url:en som angavs av föregående kommando för att köra PiggyMetrics-programmet.
    ![Skärmbild av PiggyMetrics som körs](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Du kan också navigera i Azure-portalen för att hitta WEBBADRESSEN. 
1. Navigera till tjänsten
2. Välj **appar**
3. Välj **gateway**

    ![Skärmbild av PiggyMetrics som körs](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Hitta url:en på ![sidan **Översikt över gatewayen** Skärmbild av PiggyMetrics som körs](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Efterföljande moment

I den här snabbstarten har du distribuerat ett Spring Cloud-program från Azure CLI.  Om du vill veta mer om Azure Spring Cloud fortsätter du till självstudien om hur du förbereder appen för distribution.

> [!div class="nextstepaction"]
> [Förbered ditt Azure Spring Cloud-program för distribution](spring-cloud-tutorial-prepare-app-deployment.md)

Fler exempel finns på GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
