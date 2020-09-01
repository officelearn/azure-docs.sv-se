---
title: Snabbstart – distribuera ditt första Azure Spring Cloud-program
description: I den här snabb starten distribuerar vi ett våren Cloud-program till Azure våren-molnet.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 245516e0a54865d3a6097c4bb566b850cb738ad6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260558"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>Snabb start: Distribuera ditt första Azure våren Cloud-program

Den här snabb starten förklarar hur du distribuerar ett enkelt Azure våren Cloud-mikrotjänstprogram som kan köras på Azure. 

Program koden som används i den här självstudien är en enkel app som skapats med våren Initializr. När du har slutfört det här exemplet är programmet tillgängligt online och kan hanteras via Azure Portal.

Den här snabb starten förklarar hur du:

> [!div class="checklist"]
> * Generera ett grundläggande våren Cloud-projekt
> * Etablera en tjänst instans
> * Bygg och distribuera appen med en offentlig slut punkt
> * Stream-loggar i real tid

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

* [Installera JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)
* Valfritt [Installera Azure CLI-versionen 2.0.67 eller högre](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) och Azure våren Cloud-tillägget med kommando: `az extension add --name spring-cloud`
* Valfritt [Installera Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) och [inloggning](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="generate-a-spring-cloud-project"></a>Skapa ett våren Cloud-projekt

Börja med [våren Initializr](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin) för att generera ett exempel projekt med rekommenderade beroenden för Azure våren Cloud. Följande bild visar Initializr som kon figurer ATS för det här exempelprojektet.
```url
https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin
```

  ![Sidan Initializr](media/spring-cloud-quickstart-java/initializr-page.png)

1. Klicka på **generera** när alla beroenden har angetts. Ladda ned och packa upp paketet och skapa sedan en webb styrenhet för ett enkelt webb program genom att lägga till följande `src/main/java/com/example/hellospring/HelloController.java` :

    ```java
    package com.example.hellospring;
    
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    @RestController
    public class HelloController {
    
        @RequestMapping("/")
        public String index() {
            return "Greetings from Azure Spring Cloud!";
        }
    
    }
    ```
## <a name="provision-an-instance-of-azure-spring-cloud"></a>Etablera en instans av Azure våren Cloud

Följande procedur skapar en instans av Azure våren Cloud med hjälp av Azure Portal.

1. Öppna [Azure Portal](https://ms.portal.azure.com/)på en ny flik. 

2. Sök efter *Azure våren Cloud*i den översta sökrutan.

3. Välj *Azure våren Cloud* från resultaten.

    ![ASC-ikon start](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. På sidan Azure våren Cloud klickar du på **+ Lägg till**.

    ![ASC-ikon Lägg till](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Fyll i formuläret på sidan Azure våren Cloud **create** .  Beakta följande rikt linjer:
    - **Prenumeration**: Välj den prenumeration som du vill fakturera för den här resursen.
    - **Resurs grupp**: skapa nya resurs grupper för nya resurser är en bra metod. Detta kommer att användas i senare steg som **\<resource group name\>** .
    - **Tjänst information/namn**: ange **\<service instance name\>** .  Namnet måste vara mellan 4 och 32 tecken långt och får bara innehålla gemena bokstäver, siffror och bindestreck.  Det första tecknet i tjänst namnet måste vara en bokstav och det sista tecknet måste vara en bokstav eller en siffra.
    - **Plats**: Välj region för din tjänst instans.

    ![ASC-portalen börjar](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Klicka på **Granska och skapa**.

## <a name="build-and-deploy-the-app"></a>Skapa och distribuera appen
    
#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
Följande procedur skapar och distribuerar programmet med hjälp av Azure CLI. Kör följande kommando i projektets rot.

1. Bygg projektet med maven:

    ```console
    mvn clean package -DskipTests
    ```

1. (Om du inte redan har installerat den) Installera Azure våren Cloud-tillägget för Azure CLI:

    ```azurecli
    az extension add --name spring-cloud
    ```
    
1. Skapa appen med en offentlig slut punkt som tilldelats:

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g <resource group name> --is-public
    ```

1. Distribuera jar-filen för appen:

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g <resource group name> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
    ```
    
1. Det tar några minuter att slutföra distributionen av programmet. För att bekräfta att den har distribuerats går du till bladet **appar** i Azure Portal. Du bör se status för programmet.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

I följande procedur används IntelliJ-plugin-programmet för Azure våren Cloud för att distribuera exempel appen i IntelliJ-idén.  

### <a name="import-project"></a>Importera projekt

1. Öppna dialog rutan **Välkommen** till IntelliJ och välj **Importera projekt** för att öppna import guiden.
1. Välj `hellospring` mapp.

    ![Importera projekt](media/spring-cloud-quickstart-java/intellij-new-project.png)

### <a name="deploy-the-app"></a>Distribuera appen
För att kunna distribuera till Azure måste du logga in med ditt Azure-konto och välja din prenumeration.  Information om inloggning finns i [installation och inloggning](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Högerklicka på projektet i IntelliJ Project Explorer och välj **Azure**  ->  **Deploy till Azure våren Cloud**.

    [![Distribuera till Azure 1 ](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png)](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png#lightbox)

1. Godkänn namnet på appen i fältet **namn** . **Namnet** avser konfigurationen, inte appens namn. Användarna behöver vanligt vis inte ändra det.
1. I text rutan **artefakt** väljer du *hellospring-0.0.1-Snapshot. jar*.
1. I text rutan **prenumeration** verifierar du din prenumeration.
1. I text rutan **fjäder moln** väljer du instansen av Azure våren-molnet som du skapade i [etablera Azure våren Cloud-instansen](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance).
1. Ange den **offentliga slut punkten** som ska *aktive ras*.
1. I text rutan **app:** väljer du **skapa app...**.
1. Ange *hellospring*och klicka sedan på **OK**.

    [![Distribuera till Azure OK ](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png)](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png#lightbox)

1. Starta distributionen genom att klicka på knappen **Kör** längst ned i dialog rutan **Distribuera Azure våren Cloud App** . Plugin-programmet kör kommandot `mvn package` på `hellospring` appen och distribuerar jar som genereras av `package` kommandot.
---

När distributionen är klar kan du komma åt appen på `https://<service instance name>-hellospring.azuremicroservices.io/` .

  [![Öppna appen från webbläsare ](media/spring-cloud-quickstart-java/access-app-browser.png)](media/spring-cloud-quickstart-java/access-app-browser.png#lightbox)

## <a name="streaming-logs-in-real-time"></a>Strömmande loggar i real tid

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Använd följande kommando för att hämta real tids loggar från appen.

```azurecli
az spring-cloud app logs -n hellospring -s <service instance name> -g <resource group name> --lines 100 -f

```
Loggarna visas i resultatet:

[![Strömmande loggar ](media/spring-cloud-quickstart-java/streaming-logs.png)](media/spring-cloud-quickstart-java/streaming-logs.png#lightbox)

>[!TIP]
> Använd `az spring-cloud app logs -h` för att utforska fler parametrar och logg strömmars funktioner.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

1. Välj **Azure Explorer**och sedan **våren Cloud**.
1. Högerklicka på appen som körs.
1. Välj **strömmande loggar** från den nedrullningsbara listan.
1. Välj instans.

    [![Välj strömmande loggar ](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png)](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png)

1. Direkt uppspelnings loggen visas i fönstret utdata.

    [![Strömmande logg utdata ](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)
---

För avancerade loggar analys funktioner går du till fliken **loggar** på menyn för [Azure Portal](https://portal.azure.com/). Loggarna här har en fördröjning på några minuter.

[![Loggar analys ](media/spring-cloud-quickstart-java/logs-analytics.png)](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)

## <a name="clean-up-resources"></a>Rensa resurser
I de föregående stegen skapade du Azure-resurser i en resursgrupp. Om du inte tror att du behöver dessa resurser i framtiden tar du bort resurs gruppen från portalen eller genom att köra följande kommando i Cloud Shell:
```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

I den här snabbstarten har du lärt dig att:

> [!div class="checklist"]
> * Generera ett grundläggande Azure våren Cloud-projekt
> * Etablera en tjänst instans
> * Bygg och distribuera appen med en offentlig slut punkt
> * Strömmande loggar i real tid
## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Skapa och kör mikrotjänster](spring-cloud-quickstart-sample-app-introduction.md)

Fler exempel finns på GitHub: [Azure våren Cloud-exempel](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
