---
title: Starta ett Azure våren Cloud-program med hjälp av Azure Portal
description: Distribuera ett exempel program till Azure våren-molnet med hjälp av Azure Portal.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/04/2019
ms.author: v-vasuke
ms.openlocfilehash: 1f996f18a41a4c09b35097252ec51701cbe23d41
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038967"
---
# <a name="quickstart-launch-an-azure-spring-cloud-application-using-the-azure-portal"></a>Snabbstart: Starta ett Azure våren Cloud-program med hjälp av Azure Portal

Med Azures våren Cloud kan du enkelt köra vår molnbaserade mikrotjänstprogram på Azure.

Den här snabb starten visar hur du distribuerar ett befintligt våren Cloud-program till Azure. [Här är en länk](https://github.com/xscript/PiggyMetrics) till exempel programmets kod som används i den här självstudien. När du är klar kommer det angivna exempel programmet att vara tillgängligt online och redo att hanteras via Azure Portal.

Efter den här snabb starten får du lära dig att:

> [!div class="checklist"]
> * Etablera en tjänst instans
> * Ange en konfigurations Server för en instans
> * Bygg ett program för mikrotjänster lokalt
> * Distribuera varje mikrotjänst
> * Tilldela en offentlig slut punkt för ditt program

## <a name="prerequisites"></a>Förutsättningar

>[!Note]
> Innan du påbörjar den här snabb starten ska du se till att din Azure-prenumeration har åtkomst till Azure våren Cloud.  Som förhands gransknings tjänst ber vi kunderna att kontakta oss så att vi kan lägga till din prenumeration i vår lista över tillåtna.  Om du vill utforska funktionerna i Azure våren Cloud kan du kontakta oss via e-post: azure-spring-cloud@service.microsoft.com.

>[!TIP]
> Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln.  Den har ett förinstallerat vanligt Azure-verktyg, inklusive de senaste versionerna av Git, JDK, Maven och Azure CLI. Om du är inloggad på din Azure-prenumeration startar du [Azure Cloud Shell](https://shell.azure.com) från Shell.Azure.com.  Du kan lära dig mer om Azure Cloud Shell genom att [läsa vår dokumentation](../cloud-shell/overview.md)

För att slutföra den här snabbstarten behöver du:

1. [Installera Git](https://git-scm.com/)
2. [Installera JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Installera maven 3,0 eller senare](https://maven.apache.org/download.cgi)
4. [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Installera Azure CLI-tillägget

Installera Azure våren Cloud-tillägget för Azure CLI med hjälp av följande kommando

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Etablera en tjänst instans på Azure Portal

1. Öppna den [Azure Portal](https://portal.azure.com)i en webbläsare och logga in på ditt konto.

1. Sök efter **Azure våren-molnet** och välj att gå till översikts sidan. Välj knappen **skapa** för att komma igång.

1. Fyll i formuläret, med beaktande av följande rikt linjer:
    - Tjänstnamn: Ange namnet på din tjänst instans.  Namnet måste vara mellan 4 och 32 tecken långt och får bara innehålla gemena bokstäver, siffror och bindestreck.  Det första tecknet i tjänst namnet måste vara en bokstav och det sista tecknet måste vara en bokstav eller en siffra.
    - Prenumeration: Välj den prenumeration som du vill fakturera för den här resursen.  Se till att den här prenumerationen har lagts till i vår lista över tillåtna för Azure våren-molnet.
    - Resursgrupp: Vi rekommenderar att du skapar nya resurs grupper för nya resurser.
    - Plats: Välj plats för tjänst instansen. De platser som stöds för närvarande är USA, västra USA 2, Västeuropa och Sydostasien.
    
Det tar ungefär 5 minuter för tjänsten att distribueras.  När den har distribuerats visas **översikts** sidan för tjänst instansen.

## <a name="set-up-your-configuration-server"></a>Konfigurera konfigurations servern

1. Gå till sidan tjänst **Översikt** och välj **konfigurations Server**.

1. I avsnittet **standard databas** ställer du in **URI** till "https://github.com/Azure-Samples/piggymetrics", anger **etikett** till "config" och väljer **tillämpa** för att spara ändringarna.

## <a name="build-and-deploy-microservice-applications"></a>Utveckla och distribuera program för mikrotjänster

1. Öppna ett kommando fönster och kör följande kommando för att klona exempel App-lagringsplatsen till den lokala datorn.

    ```cli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

1. Bygg kommandot projektet genom att köra nedan.

    ```cli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

1. Logga in på Azure CLI och ange din aktiva prenumeration.

    ```cli
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
    az configure --defaults asc=<service instance name>
    ```

1. Skapa programmet `gateway` och distribuera JAR-filen.

    ```azurecli
    az asc app create -n gateway
    az asc app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

1. Med samma mönster skapar du `account-service`-och `auth-service`-program och distribuerar deras JAR-filer.

    ```cli
    az asc app create -n account-service
    az asc app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az asc app create -n auth-service
    az asc app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

1. Det tar några minuter att slutföra distributionen av programmen. För att bekräfta att de har distribuerats går du till **program instrument panelen** i Azure Portal. Du bör se en rad vart och ett av de tre programmen.

## <a name="assign-a-public-endpoint-to-gateway"></a>Tilldela en offentlig slut punkt till gateway

1. Öppna sidan för **program instrument panelen** .
2. Välj programmet `gateway` för att visa sidan med **programinformation** .
3. Välj **tilldela domän** för att tilldela en offentlig slut punkt till gateway. Detta kan några minuter. 
4. Ange den tilldelade offentliga IP-adressen i webbläsaren för att visa det program som körs.


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

> [!div class="checklist"]
> * Etablera en tjänst instans
> * Ange en konfigurations Server för en instans
> * Bygg ett program för mikrotjänster lokalt
> * Distribuera varje mikrotjänst
> * Redigera miljövariabler för program
> * Tilldela en offentlig IP-adress för din Application Gateway

> [!div class="nextstepaction"]
> [Förbered ditt Azure våren Cloud-program för distribution](spring-cloud-tutorial-prepare-app-deployment.md)