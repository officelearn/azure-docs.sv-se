---
title: 'Snabb start: starta ett Azure våren Cloud-program med hjälp av Azure Portal'
description: Distribuera ett exempel program till Azure våren-molnet med hjälp av Azure Portal.
author: jpconnock
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/4/2019
ms.author: jeconnoc
ms.openlocfilehash: ab2950ed85d2409cd81ea562ec977c0eee2f6bab
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693131"
---
# <a name="quickstart-launch-an-azure-spring-cloud-application-using-the-azure-portal"></a>Snabb start: starta ett Azure våren Cloud-program med hjälp av Azure Portal

Med Azures våren Cloud kan du enkelt köra vår molnbaserade mikrotjänstprogram på Azure.

Den här snabb starten visar hur du distribuerar ett befintligt våren Cloud-program till Azure.  Du hittar exempel koden som används i den här självstudien i vår [GitHub-exempel databas](https://github.com/Azure-Samples/PiggyMetrics). När du är klar kommer det angivna exempel programmet att vara tillgängligt online och redo att hanteras via Azure Portal.

Efter den här snabb starten får du lära dig att:

> [!div class="checklist"]
> * Etablera en tjänst instans
> * Ange en konfigurations Server för en instans
> * Bygg ett program för mikrotjänster lokalt
> * Distribuera varje mikrotjänst
> * Tilldela en offentlig slut punkt för ditt program

## <a name="prerequisites"></a>Nödvändiga komponenter

>[!Note]
> Azure våren Cloud erbjuds för närvarande som en offentlig för hands version. Med den offentliga för hands versionen kan kunder experimentera med nya funktioner före den officiella versionen.  Funktioner och tjänster för offentliga för hands versioner är inte avsedda för användning i produktion.  Om du vill ha mer information om stöd under för hands versionerna kan du skicka en [supportbegäran](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

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

```Azure CLI
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Etablera en tjänst instans på Azure Portal

1. Öppna [den här länken till Azure våren Cloud i den Azure Portal](https://ms.portal.azure.com/#create/Microsoft.AppPlatform)i en webbläsare.

1. Välj **Azure våren Cloud** för att gå till översikts sidan. Välj knappen **skapa** för att komma igång.

1. Fyll i formuläret, med beaktande av följande rikt linjer:
    - Tjänst namn: Ange namnet på din tjänst instans.  Namnet måste vara mellan 4 och 32 tecken långt och får bara innehålla gemena bokstäver, siffror och bindestreck.  Det första tecknet i tjänst namnet måste vara en bokstav och det sista tecknet måste vara en bokstav eller en siffra.
    - Prenumeration: Välj den prenumeration som du vill fakturera för den här resursen.  Se till att den här prenumerationen har lagts till i vår lista över tillåtna för Azure våren-molnet.
    - Resurs grupp: skapa nya resurs grupper för nya resurser är en bra metod.
    - Plats: Välj platsen för din tjänst instans. De platser som stöds för närvarande är USA, västra USA 2, Västeuropa och Sydostasien.
    
Det tar ungefär 5 minuter för tjänsten att distribueras.  När den har distribuerats visas **översikts** sidan för tjänst instansen.

## <a name="set-up-your-configuration-server"></a>Konfigurera konfigurations servern

1. Gå till sidan tjänst **Översikt** och välj **konfigurations Server**.

1. I avsnittet **standard databas** ställer du in **URI** till "https\://GitHub.com/Azure-samples/piggymetrics", anger **etikett** till "config" och väljer **tillämpa** för att spara ändringarna.

    ![Skärm bild av ASC-portalen](media/spring-cloud-tutorial-config-server/portal-config-server.png)

## <a name="build-and-deploy-microservice-applications"></a>Utveckla och distribuera program för mikrotjänster

1. Öppna en [Azure-Cloudshell](https://shell.azure.com) och klona exempel App-lagringsplatsen till den lokala datorn.  Här skapar vi först en tillfällig katalog med namnet `source-code` innan appen klonas.

    ```azurecli
    mkdir source-code
    cd source-code
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

1. Bygg det klonade paketet.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

1. Logga in på Azure CLI och ange din aktiva prenumeration.

    ```azurecli
    # Login to Azure CLI
    az login

    # List all subscriptions
    az account list -o table

    # Set active subscription
    az account set --subscription <target subscription ID>
    ```

1. Tilldela namn till din resurs grupp och din tjänst. Se till att ersätta plats hållarna nedan med resurs grupps namnet och tjänst namnet som du etablerade tidigare i den här självstudien.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. Skapa programmet `gateway` och distribuera JAR-filen.

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

1. Med samma mönster skapar du `account-service`-och `auth-service`-program och distribuerar deras JAR-filer.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

1. Det tar några minuter att slutföra distributionen av programmen. För att bekräfta att de har distribuerats går du till bladet **appar** i Azure Portal. Du bör se en rad vart och ett av de tre programmen.

## <a name="assign-a-public-endpoint-to-gateway"></a>Tilldela en offentlig slut punkt till gateway

1. Öppna fliken **appar** på menyn till vänster.

1. Välj `gateway` programmet för att visa **översikts** sidan.

1. Välj **tilldela domän** för att tilldela en offentlig slut punkt till gateway. Detta kan några minuter.

    ![Skärm bild av ASC-portalen](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

1. Ange den tilldelade offentliga slut punkten (med etiketten **URL**) i webbläsaren för att visa programmet som körs.

    ![Skärm bild av ASC-portalen](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

> [!div class="checklist"]
> * Etablera en tjänst instans
> * Ange en konfigurations Server för en instans
> * Bygg ett program för mikrotjänster lokalt
> * Distribuera varje mikrotjänst
> * Tilldela en offentlig slut punkt för din Application Gateway

> [!div class="nextstepaction"]
> [Förbered ditt Azure våren Cloud-program för distribution](spring-cloud-tutorial-prepare-app-deployment.md)
