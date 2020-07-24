---
title: Snabb start – starta ett befintligt Azure våren Cloud-program med hjälp av Azure Portal
description: I den här snabb starten ska du distribuera ett fjäder moln program till Azure våren-molnet med hjälp av Azure Portal.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b227c17095d1a5aa6e7e8e7ec9f918d9cac2062e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87071229"
---
# <a name="quickstart-launch-an-existing-azure-spring-cloud-application-using-the-azure-portal"></a>Snabb start: starta ett befintligt Azure våren Cloud-program med hjälp av Azure Portal

Den här snabb starten visar hur du distribuerar ett befintligt våren Cloud-program till Azure. Med Azures våren Cloud kan du enkelt köra vår molnbaserade mikrotjänstprogram på Azure. 

Du hittar exempel koden som används i den här självstudien i vår [GitHub-exempel databas](https://github.com/Azure-Samples/PiggyMetrics). När du är klar kommer det angivna exempel programmet att vara tillgängligt online och redo att hanteras via Azure Portal.

Efter den här snabb starten får du lära dig att:

> [!div class="checklist"]
> * Etablera en tjänst instans
> * Ange en konfigurations Server för en instans
> * Bygg ett program för mikrotjänster lokalt
> * Distribuera varje mikrotjänst
> * Tilldela en offentlig slut punkt för ditt program

## <a name="prerequisites"></a>Förutsättningar

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

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Etablera en tjänst instans på Azure Portal

1. Öppna [Azure Portal](https://ms.portal.azure.com/)på en ny flik. 

2. Sök efter **Azure våren Cloud**i den översta sökrutan.

3. Välj **Azure våren Cloud** från resultaten.

 ![ASC-ikon](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. På sidan Azure våren Cloud klickar du på **+ Lägg till**.

 ![ASC-ikon](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Fyll i formuläret på sidan Azure våren Cloud **create** .  Beakta följande rikt linjer:
    - **Prenumeration**: Välj den prenumeration som du vill fakturera för den här resursen.  Se till att den här prenumerationen har lagts till i vår lista över tillåtna för Azure våren-molnet.
    - **Resurs grupp**: skapa nya resurs grupper för nya resurser är en bra metod.
    - **Tjänst information/namn**: Ange namnet på din tjänst instans.  Namnet måste vara mellan 4 och 32 tecken långt och får bara innehålla gemena bokstäver, siffror och bindestreck.  Det första tecknet i tjänst namnet måste vara en bokstav och det sista tecknet måste vara en bokstav eller en siffra.
    - **Plats**: Välj platsen för din tjänst instans. De platser som stöds för närvarande är USA, västra USA 2, Västeuropa och Sydostasien.

    ![ASC-portalen börjar](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Klicka på fliken **diagnostisk inställning** för att öppna följande dialog ruta.

7. Du kan ställa in **Aktivera loggar** på *Ja* eller *Nej* enligt dina krav.

    ![Aktivera loggar](media/spring-cloud-quickstart-launch-app-portal/diagnostic-setting.png)

8. Klicka på fliken **spårning** .

9. Du kan ställa in **Aktivera spårning** till *Ja* eller *Nej* enligt dina krav.  Om du ställer in **Aktivera spårning** till Ja väljer du också en befintlig inblick i programmet eller skapar en ny. Utan den **Application Insights** specifikationen visas ett verifierings fel.


    ![Spårning](media/spring-cloud-quickstart-launch-app-portal/tracing.png)

10. Klicka på **Granska och skapa**.

11. Kontrol lera dina specifikationer och klicka på **skapa**.

Det tar ungefär 5 minuter för tjänsten att distribueras.  När den har distribuerats visas **översikts** sidan för tjänst instansen.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=provision)


## <a name="set-up-your-configuration-server"></a>Konfigurera konfigurations servern

1. Gå till sidan tjänst **Översikt** och välj **konfigurations Server**.

2. I avsnittet **standard plats** ställer du in **URI** till " https://github.com/Azure-Samples/piggymetrics-config ".

3. Tryck på **Apply** (Verkställ) för att spara ändringarna.

    ![Skärm bild av ASC-portalen](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=config-server)

## <a name="build-and-deploy-microservice-applications"></a>Utveckla och distribuera program för mikrotjänster

1. Öppna en [Azure Cloud Shell](https://shell.azure.com) eller ditt lokala gränssnitt med Azure CLI installerat. Här skapar vi först en tillfällig katalog som kallas `source-code` innan du klonar exempel appen.

    ```console
    mkdir source-code
    cd source-code
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Bygg det klonade paketet.

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

3. Installera Azure våren Cloud-tillägget för Azure CLI med hjälp av följande kommando

    ```azurecli
    az extension add --name spring-cloud
    ```

4. Tilldela namn till din resurs grupp och din tjänst. Se till att ersätta plats hållarna nedan med resurs grupps namnet och tjänst namnet som du etablerade tidigare i den här självstudien.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

5. Skapa `gateway` programmet och distribuera jar-filen.

    Skapa appen med hjälp av våren Cloud extension:

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

6. Skapa `account-service` och `auth-service` distribuera sina jar-filer efter samma mönster.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

7. Det tar några minuter att slutföra distributionen av programmen. För att bekräfta att de har distribuerats går du till bladet **appar** i Azure Portal. Du bör se en rad vart och ett av de tre programmen.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Tilldela en offentlig slut punkt till gateway

1. Öppna fliken **appar** på menyn till vänster.

2. Välj `gateway` programmet för att visa **översikts** sidan.

3. Välj **tilldela slut punkt** för att tilldela en offentlig slut punkt till gateway. Det kan ta några minuter.

    ![Skärm bild av ASC-portalen](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

4. Ange den tilldelade offentliga slut punkten (med etiketten **URL**) i webbläsaren för att visa programmet som körs.

    ![Skärm bild av ASC-portalen](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=public-endpoint)

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

Fler exempel finns på GitHub: [Azure våren Cloud-exempel](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
