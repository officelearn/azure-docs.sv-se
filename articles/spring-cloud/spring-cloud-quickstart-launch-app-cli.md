---
title: 'Snabb start: starta ett Java våren-program med Azure CLI'
description: I den här snabb starten distribuerar du ett exempel program till Azure våren Cloud på Azure CLI.
author: jpconnock
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 41801fce41cd79898e9ebd12f9ca970f17b017bb
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75891705"
---
# <a name="quickstart-launch-a-java-spring-application-using-the-azure-cli"></a>Snabb start: starta ett Java våren-program med Azure CLI

Med Azures våren Cloud kan du enkelt köra ett "våren Boot-baserat" mikrotjänster-program på Azure.

Den här snabb starten visar hur du distribuerar ett befintligt Java våren Cloud-program till Azure. När du är klar kan du fortsätta att hantera programmet via Azure CLI eller växla till att använda Azure Portal.

Efter den här snabb starten får du lära dig att:

> [!div class="checklist"]
> * Etablera en tjänst instans
> * Ange en konfigurations Server för en instans
> * Bygg ett program för mikrotjänster lokalt
> * Distribuera varje mikrotjänst
> * Tilldela en offentlig slut punkt för ditt program

## <a name="prerequisites"></a>Krav

>[!Note]
> Azure våren Cloud erbjuds för närvarande som en offentlig för hands version. Med den offentliga för hands versionen kan kunder experimentera med nya funktioner före den officiella versionen.  Funktioner och tjänster för offentliga för hands versioner är inte avsedda för användning i produktion.  Om du vill ha mer information om support under för hands versionerna kan du läsa [vanliga frågor och svar](https://azure.microsoft.com/support/faq/) eller arkiv en [supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) .

>[!TIP]
> Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln.  Den har ett förinstallerat vanligt Azure-verktyg, inklusive de senaste versionerna av Git, JDK, Maven och Azure CLI. Om du är inloggad på din Azure-prenumeration startar du [Azure Cloud Shell](https://shell.azure.com) från Shell.Azure.com.  Du kan lära dig mer om Azure Cloud Shell genom att [läsa vår dokumentation](../cloud-shell/overview.md)

För att slutföra den här snabbstarten behöver du:

1. [Installera Git](https://git-scm.com/)
2. [Installera JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Installera maven 3,0 eller senare](https://maven.apache.org/download.cgi)
4. [Installera Azure CLI-versionen 2.0.67 eller högre](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Installera Azure CLI-tillägget

Installera Azure våren Cloud-tillägget för Azure CLI med hjälp av följande kommando

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-cli"></a>Etablera en tjänst instans på Azure CLI

1. Logga in på Azure CLI och välj din aktiva prenumeration. Se till att välja den aktiva prenumeration som är vit listas för Azure våren Cloud

    ```azurecli
        az login
        az account list -o table
        az account set --subscription
    ```

2. Förbered ett namn för din Azure våren Cloud-tjänst.  Namnet måste vara mellan 4 och 32 tecken långt och får bara innehålla gemena bokstäver, siffror och bindestreck.  Det första tecknet i tjänst namnet måste vara en bokstav och det sista tecknet måste vara en bokstav eller en siffra.

3. Skapa en resurs grupp som innehåller din Azure våren Cloud-tjänst.

    ```azurecli
        az group create --location eastus --name <resource group name>
    ```
    Läs mer om [Azure-resursgrupper](../azure-resource-manager/management/overview.md).

4. Öppna ett Azure CLI-fönster och kör följande kommandon för att etablera en instans av Azure våren Cloud.

    ```azurecli
        az spring-cloud create -n <service name> -g <resource group name>
    ```

    Tjänst instansen tar cirka fem minuter att distribuera.

5. Ange standard resurs grupps namnet och kluster namnet med hjälp av följande kommandon:

    ```azurecli
        az configure --defaults group=<service group name>
        az configure --defaults spring-cloud=<service instance name>
    ```

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=provision)

## <a name="setup-your-configuration-server"></a>Konfigurera konfigurations servern

Uppdatera konfigurations servern med platsen för git-lagringsplatsen för vårt projekt:

```git
az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/Azure-Samples/piggymetrics --label config
```

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=config-server)

## <a name="build-the-microservices-applications-locally"></a>Bygg appar för mikrotjänster lokalt

1. Skapa en ny mapp och klona exempel App-lagringsplatsen till ditt Azure Cloud-konto.  

    ```azurecli
        mkdir source-code
        git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Ändra katalog och bygg projektet.

    ```azurecli
        cd piggymetrics
        mvn clean package -D skipTests
    ```

Kompilering av projektet tar cirka 5 minuter.  När du är klar bör du ha enskilda JAR-filer för varje tjänst i respektive mapp.

## <a name="create-the-microservices"></a>Skapa mikrotjänster

Skapa vår moln mikrotjänster med hjälp av JAR-filerna som skapats i föregående steg. Du kommer att skapa tre mikrotjänster: **Gateway**, **auth-service**och **Account-service**.

```azurecli
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

## <a name="deploy-applications-and-set-environment-variables"></a>Distribuera program och ange miljövariabler

Vi måste faktiskt distribuera våra program till Azure. Använd följande kommandon för att distribuera alla tre programmen:

```azurecli
az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
```

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=deploy)

## <a name="assign-public-endpoint-to-gateway"></a>Tilldela offentlig slut punkt till gateway

Vi behöver ett sätt att komma åt programmet via en webbläsare. Vårt Gateway-program behöver en offentlig slut punkt som kan tilldelas med följande kommando:

```azurecli
az spring-cloud app update -n gateway --is-public true
```

Till sist kan du fråga **Gateway** -appen efter dess offentliga IP-adress så att du kan kontrol lera att programmet körs:

```azurecli
az spring-cloud app show --name gateway | grep url
```

Gå till URL: en som tillhandahålls av föregående kommando för att köra PiggyMetrics-programmet.
    ![skärm bild av PiggyMetrics som kör](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Du kan också navigera Azure Portal för att hitta URL: en. 
1. Navigera till tjänsten
1. Välj **appar**
1. Välj **Gateway**

    ![Skärm bild av PiggyMetrics som körs](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
1. Hitta URL: en på sidan **Gateway-översikt** ![skärm bild av PiggyMetrics som kör](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Efterföljande moment

I den här snabb starten har du distribuerat ett fjäder moln program från Azure CLI.  Om du vill veta mer om Azure våren Cloud fortsätter du till självstudien om att förbereda din app för distribution.

> [!div class="nextstepaction"]
> [Förbered ditt Azure våren Cloud-program för distribution](spring-cloud-tutorial-prepare-app-deployment.md)

Fler exempel finns på GitHub: [Azure våren Cloud-exempel](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
