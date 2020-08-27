---
title: Snabb start – bygga och distribuera appar till Azure våren Cloud
description: Beskriver app-distribution till Azure våren Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 770dd021a09e3ba0b1c2c6742ded3a73424b042f
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951752"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>Snabb start: bygga och distribuera appar till Azure våren Cloud

Det här dokumentet beskriver hur du skapar och distribuerar program för mikrotjänster till Azure våren Cloud med:
* Azure CLI
* Maven-plugin
* IntelliJ

Innan du distribuerar med Azure CLI eller Maven ska du slutföra exemplen som [etablerar en instans av Azure våren Cloud](spring-cloud-quickstart-provision-service-instance.md) och [konfigurerar konfigurations servern](spring-cloud-quickstart-setup-config-server.md).

## <a name="prerequisites"></a>Förutsättningar

* [Installera JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)
* Valfritt [Installera Azure CLI-versionen 2.0.67 eller högre](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) och installera Azure våren Cloud-tillägget med kommandot: `az extension add --name spring-cloud`
* Valfritt [Installera Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) och [inloggning](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="deployment-procedures"></a>Distributions procedurer

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

### <a name="build-the-microservices-applications-locally"></a>Bygg appar för mikrotjänster lokalt

1. Klona exempel App-lagringsplatsen till ditt Azure Cloud-konto.  

    ```azurecli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Ändra katalog och bygg projektet.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

Kompilering av projektet tar cirka 5 minuter. När du är klar bör du ha enskilda JAR-filer för varje tjänst i respektive mapp.

### <a name="create-and-deploy-the-apps"></a>Skapa och distribuera apparna

1. Ange standard resurs grupps namnet och kluster namnet med hjälp av följande kommandon:

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. Skapa Azure våren Cloud-mikrotjänster med hjälp av JAR-filerna som skapats i föregående steg. Du kommer att skapa tre appar: **Gateway**, **auth-service**och **Account-service**.

    ```azurecli
    az spring-cloud app create --name gateway
    az spring-cloud app create --name auth-service
    az spring-cloud app create --name account-service
    ```

1. Vi måste faktiskt distribuera våra program till Azure. Använd följande kommandon för att distribuera alla tre programmen:

    ```azurecli
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

### <a name="assign-public-endpoint-to-gateway"></a>Tilldela offentlig slut punkt till gateway

Vi behöver ett sätt att komma åt programmet via en webbläsare. Vårt Gateway-program behöver en offentlig slut punkt.

1. Tilldela slut punkten med följande kommando:

    ```azurecli
    az spring-cloud app update -n gateway --is-public true
    ```

2. Fråga **Gateway** -appen efter dess offentliga IP-adress så att du kan kontrol lera att programmet körs:

    ```azurecli
    az spring-cloud app show --name gateway --query properties.url
    ```

#### <a name="maven"></a>[Maven](#tab/Maven)

### <a name="clone-and-build-the-sample-application-repository"></a>Klona och bygga exempel program databasen

1. Klona git-lagringsplatsen genom att köra följande kommando:

    ```
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Ändra katalog och bygg projektet genom att köra följande kommando:

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Generera konfigurationer och distribuera till Azure våren-molnet

1. Generera konfigurationer genom att köra följande kommando i rotmappen för PiggyMetrics som innehåller den överordnade POM. Om du redan har loggat in med Azure CLI kommer kommandot automatiskt att hämta autentiseringsuppgifterna. Annars loggas du in med anvisningarna. Se vår [wiki-sida](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) för mer information.

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```
    
    Du uppmanas att välja:
    * **Moduler:** Select `gateway` , `auth-service` och "konto-service" ")
    * **Prenumeration:** din prenumeration med Azure våren Cloud-instansen har skapats
    * **Tjänst instans:** namnet på den skapade Azure våren Cloud-instansen
    * **Offentlig slut punkt:** I listan med tillhandahållna projekt anger du det nummer som motsvarar `gateway` för att ge IT-offentlig åtkomst.

1. POM innehåller nu plugin-beroenden och konfigurationer. Distribuera apparna med hjälp av följande kommando. 

    ```
    mvn azure-spring-cloud:deploy
    ```

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

### <a name="import-sample-project-in-intellij"></a>Importera exempel projekt i IntelliJ

1. Ladda ned och zippa upp käll databasen för den här själv studie kursen eller klona den med git: `git clone https://github.com/Azure-Samples/piggymetrics` 

1. Öppna dialog rutan **Välkommen** IntelliJ, Välj **Importera projekt** för att öppna import guiden.

1. Välj `piggymetric` mapp.

    ![Importera projekt](media/spring-cloud-intellij-howto/revision-import-project-1.png)

### <a name="deploy-gateway-app-to-azure-spring-cloud"></a>Distribuera Gateway-appen till Azure våren Cloud
För att kunna distribuera till Azure måste du logga in med ditt Azure-konto med Azure Toolkit for IntelliJ och välja din prenumeration. Information om inloggning finns i [installation och inloggning](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Högerklicka på projektet i IntelliJ Project Explorer och välj **Azure**  ->  **Deploy till Azure våren Cloud**.

    ![Distribuera till Azure 1](media/spring-cloud-intellij-howto/revision-deploy-to-azure-1.png)

1. I fältet **namn** lägger du till *: Gateway* till det befintliga **namnet** refererar till konfigurationen.
1. I text rutan **artefakt** väljer du *com. piggymetrics: Gateway: 1.0-Snapshot*.
1. I text rutan **prenumeration** verifierar du din prenumeration.
1. I text rutan **fjäder moln** väljer du instansen av Azure våren-molnet som du skapade i [etablera Azure våren Cloud-instansen](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance).
1. Ange den **offentliga slut punkten** som ska *aktive ras*.
1. I text rutan **app:** väljer du **skapa app...**.
1. Ange *Gateway*och klicka sedan på **OK**.

    ![Distribuera till Azure OK](media/spring-cloud-intellij-howto/revision-deploy-to-azure-2.png)

1. I avsnittet **före start** i dialog rutan dubbelklickar du på * Kör maven mål * *.
1. I text rutan **arbets katalog** navigerar du till mappen *piggymetrics/Gateway* .
1. I text rutan **kommando rad** anger du *Package-DskipTests*. Klicka på **OK**.
1. Starta distributionen genom att klicka på knappen **Kör** längst ned i dialog rutan **Distribuera Azure våren Cloud App** . Plugin-programmet kör kommandot `mvn package` på `gateway` appen och distribuerar jar som genereras av `package` kommandot.

### <a name="deploy-auth-service-and-account-service-apps-to-azure-spring-cloud"></a>Distribuera auth-service-och konto-service-appar till Azure våren Cloud
Du kan upprepa stegen ovan för att distribuera `auth-service` och `account-service` apparna till Azure våren Cloud. Förvissa sig:

1. Ändra **namnet** och **artefakten** för att identifiera `auth-service` appen.
1. I text rutan **app:** väljer du **skapa app...** för att skapa `auth-service` appar.
1. Kontrol lera att alternativet **offentlig slut punkt** är inställt på *inaktive rad*.
1. I avsnittet **före start** i dialog rutan byter du **arbets katalog** till mappen *piggymetrics/auth-service* .
1. Starta distributionen genom att klicka på knappen **Kör** längst ned i dialog rutan **Distribuera Azure våren Cloud App** . 
1. Upprepa dessa procedurer för att konfigurera och distribuera `account-service` .
---

Navigera till URL: en som anges i de föregående stegen för att komma åt PiggyMetrics-programmet. otillräcklig. `https://<service instance name>-gateway.azuremicroservices.io`

![Åtkomst PiggyMetrics](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Du kan också navigera Azure Portal för att hitta URL: en. 
1. Navigera till tjänsten
2. Välj **appar**
3. Välj **Gateway**

    ![Navigera i app](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Hitta URL: en på **gatewayen | Översikts** sida

    ![Navigera i app Second](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

## <a name="clean-up-resources"></a>Rensa resurser
I de föregående stegen skapade du Azure-resurser i en resursgrupp. Om du inte tror att du behöver dessa resurser i framtiden tar du bort resurs gruppen från portalen eller genom att köra följande kommando i Cloud Shell:
```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```
I föregående steg anger du också standard resurs gruppens namn. Om du vill ta bort standardvärdet kör du följande kommando i Cloud Shell:
```azurecli
az configure --defaults group=
```
## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Loggar, statistik och spårning](spring-cloud-quickstart-logs-metrics-tracing.md)
