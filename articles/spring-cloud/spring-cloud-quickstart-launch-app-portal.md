---
title: Snabbstart – starta ett befintligt Azure Spring Cloud-program med Azure-portalen
description: I den här snabbstarten distribuerar du ett Spring Cloud-program till Azure Spring Cloud med Azure-portalen.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.openlocfilehash: 9cd59fdf81e9b5d56872d20c76e8ea177b3c8577
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79470902"
---
# <a name="quickstart-launch-an-existing-azure-spring-cloud-application-using-the-azure-portal"></a>Snabbstart: Starta ett befintligt Azure Spring Cloud-program med Azure-portalen

Den här snabbstarten visar hur du distribuerar ett befintligt Spring Cloud-program till Azure. Med Azure Spring Cloud kan du enkelt köra Spring Cloud-baserade mikrotjänstprogram på Azure. 

Du hittar exempelprogramkoden som används i den här självstudien i vår [GitHub-exempeldatabas](https://github.com/Azure-Samples/PiggyMetrics). När du är klar kommer det medföljande exempelprogrammet att vara tillgängligt online och redo att hanteras via Azure-portalen.

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

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Etablera en tjänstinstans på Azure-portalen

1. Öppna [Azure-portalen](https://ms.portal.azure.com/)på en ny flik . 

2. Sök efter Azure Spring **Cloud**i den övre sökrutan .

3. Välj **Azure Spring Cloud** från resultaten.

 ![Ikon för ASC](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Klicka på + Lägg **till**på sidan Azure Spring Cloud.

 ![Ikon för ASC](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Fyll i formuläret på sidan Azure Spring Cloud **Create.**  Tänk på följande riktlinjer:
    - **Prenumeration:** Välj den prenumeration som du vill faktureras för den här resursen.  Se till att den här prenumerationen har lagts till i vår tillåt-lista för Azure Spring Cloud.
    - **Resursgrupp**: Att skapa nya resursgrupper för nya resurser är en bra idé.
    - **Serviceinformation/namn**: Ange namnet på din tjänstinstans.  Namnet måste vara mellan 4 och 32 tecken långt och får bara innehålla gemener, siffror och bindestreck.  Det första tecknet i tjänstnamnet måste vara en bokstav och det sista tecknet måste vara antingen en bokstav eller ett nummer.
    - **Plats**: Välj plats för din tjänstinstans. Platser som stöds är östra USA, västra USA 2, Västeuropa och Sydostasien.

    ![ASC-portalen startar](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Klicka på fliken **Diagnostikinställning** för att öppna följande dialogruta.

7. Du kan ställa in **Aktivera loggar** till *ja* eller *nej* enligt dina krav.

    ![Aktivera loggar](media/spring-cloud-quickstart-launch-app-portal/diagnostic-setting.png)

8. Klicka på fliken **Kalkering.**

9. Du kan ställa in **Aktivera spårning** till *ja* eller *nej* enligt dina krav.  Om du anger **Aktivera spårning** till ja väljer du även en befintlig programinsikt eller skapar en ny. Utan **application insights-specifikationen** kommer det att finnas ett valideringsfel.


    ![Spårning](media/spring-cloud-quickstart-launch-app-portal/tracing.png)

10. Klicka på **Granska och skapa**.

11. Kontrollera dina specifikationer och klicka på **Skapa**.

Det tar cirka 5 minuter för tjänsten att distribuera.  När den har distribuerats visas **sidan Översikt** för tjänstinstansen.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=provision)


## <a name="set-up-your-configuration-server"></a>Konfigurera konfigurationsservern

1. Gå till sidan Översikt **över** tjänsten och välj **Config Server**.

2. Ange **Default repository** **URI till** "https://github.com/Azure-Samples/piggymetrics-config".

3. Tryck på **Apply** (Verkställ) för att spara ändringarna.

    ![Skärmbild av ASC-portalen](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=config-server)

## <a name="build-and-deploy-microservice-applications"></a>Skapa och distribuera mikrotjänstprogram

1. Öppna ett [Azure Cloud Shell](https://shell.azure.com) och klona exempelappdatabasen till din lokala dator.  Här skapar vi först en `source-code` tillfällig katalog som heter innan du klonar appen.

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

3. Tilldela namn till resursgruppen och din tjänst. Var noga med att ersätta platshållarna nedan med resursgruppens namn och tjänstnamn som du etablerade tidigare i den här självstudien.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

4. Skapa `gateway` programmet och distribuera JAR-filen.

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

5. Efter samma mönster skapar `account-service` `auth-service` du programmen och distribuerar deras JAR-filer.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

6. Det tar några minuter att slutföra distributionen av programmen. Om du vill bekräfta att de har distribuerats går du till bladet **Appar** i Azure-portalen. Du bör se en rad var och en av de tre programmen.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Tilldela en offentlig slutpunkt till gatewayen

1. Öppna fliken **Appar** på menyn till vänster.

2. Välj `gateway` det program som ska visas på sidan **Översikt.**

3. Välj **Tilldela slutpunkt** om du vill tilldela en offentlig slutpunkt till gatewayen. Det kan ta några minuter.

    ![Skärmbild av ASC-portalen](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

4. Ange den tilldelade offentliga slutpunkten (märkt **URL)** i webbläsaren för att visa ditt program som körs.

    ![Skärmbild av ASC-portalen](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

> [!div class="checklist"]
> * Etablera en tjänstinstans
> * Ange en konfigurationsserver för en instans
> * Skapa ett mikrotjänstprogram lokalt
> * Distribuera varje mikrotjänst
> * Tilldela offentlig slutpunkt för programgatewayen

> [!div class="nextstepaction"]
> [Förbered ditt Azure Spring Cloud-program för distribution](spring-cloud-tutorial-prepare-app-deployment.md)

Fler exempel finns på GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
