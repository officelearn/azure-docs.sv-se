---
title: Distribuera fjärr styrnings lösningen lokalt – IntelliJ IDE – Azure | Microsoft Docs
description: Den här instruktions guiden visar hur du distribuerar lösnings acceleratorn för fjärrövervakning till din lokala dator med hjälp av IntelliJ för testning och utveckling.
author: dominicbetts
ms.custom: devx-track-java
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 78573cfe00d8e2e7ddcbf705dffdd5530f82c4e0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024205"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Distribuera lösnings acceleratorn för fjärr styrning lokalt – IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Den här artikeln visar hur du distribuerar lösnings acceleratorn för fjärrövervakning till din lokala dator för testning och utveckling. Du lär dig hur du kör mikrotjänsterna i IntelliJ. En lokal distribution av mikrotjänster kommer att använda följande moln tjänster: IoT Hub, Azure Cosmos DB, Azure streaming Analytics och Azure Time Series Insights.

Om du vill köra lösnings acceleratorn för fjärrövervakning i Docker på den lokala datorn läser [du distribuera lösnings Accelerator för fjärrövervakning lokalt Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill distribuera de Azure-tjänster som används av lösningen för fjärrövervakning, behöver du en aktiv Azure-prenumeration.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Dator konfiguration

För att slutföra den lokala distributionen behöver du följande verktyg installerade på den lokala utvecklings datorn:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ community-utgåva](https://www.jetbrains.com/idea/download/)
* [IntelliJ Scala-plugin](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ SBT-plugin](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ SBT utförar-plugin](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js V8](https://nodejs.org/)

Node.js V8 är ett krav för de PC CLI-datorer som skripten använder för att skapa Azure-resurser. Använd inte Node.js v10.

> [!NOTE]
> IntelliJ IDE är tillgänglig för Windows och Mac.

## <a name="download-the-source-code"></a>Ladda ned käll koden

Käll kods databaserna för fjärrövervakning innehåller käll koden och de Docker-konfigurationsfiler som du behöver för att köra Docker-avbildningarna för mikrotjänster.

Om du vill klona och skapa en lokal version av lagrings platsen använder du kommando rads miljön för att gå till en lämplig mapp på den lokala datorn. Kör sedan en av följande kommando uppsättningar för att klona Java-databasen:

* Om du vill ladda ned den senaste versionen av Java mikroservice-implementeringar kör du följande kommando:

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* Hämta de senaste undermodulerna genom att köra följande kommandon:

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> De här kommandona hämtar käll koden för alla mikrotjänster utöver de skript som du använder för att köra mikrotjänster lokalt. Du behöver inte käll koden för att köra mikrotjänster i Docker. Men käll koden är användbar om du senare planerar att ändra lösnings Accelerator och testa dina ändringar lokalt.

## <a name="deploy-the-azure-services"></a>Distribuera Azure-tjänsterna

Även om den här artikeln visar hur du kör mikrotjänster lokalt, är de beroende av Azure-tjänster som körs i molnet. Använd följande skript för att distribuera Azure-tjänsterna. I skript exemplen förutsätter vi att du använder Java-lagringsplatsen på en Windows-dator. Om du arbetar i en annan miljö kan du justera Sök vägarna, fil namns tilläggen och Sök vägs avgränsarna på rätt sätt.

### <a name="create-new-azure-resources"></a>Skapa nya Azure-resurser

Om du ännu inte har skapat de nödvändiga Azure-resurserna följer du dessa steg:

1. I kommando rads miljön går du till mappen **\services\scripts\local\launch** i din klonade kopia av lagrings platsen.

1. Kör följande kommandon för att installera **PC** CLI-verktyget och logga in på ditt Azure-konto:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Kör skriptet **Start. cmd** . I skriptet uppmanas du att ange följande information:

   * Ett lösnings namn.
   * Den Azure-prenumeration som ska användas.
   * Platsen för det Azure-datacenter som ska användas.

   Skriptet skapar en resurs grupp i Azure som har ditt lösnings namn. Den här resurs gruppen innehåller de Azure-resurser som används av Solution Accelerator. Du kan ta bort den här resurs gruppen när du inte längre behöver motsvarande resurser.

   Skriptet lägger också till en uppsättning miljövariabler på den lokala datorn. Varje variabel namn har prefixet **PCs**. De här miljövariablerna innehåller information som tillåter fjärr styrning att läsa konfigurations värden från en Azure Key Vault resurs.

   > [!TIP]
   > När skriptet har slutförts sparas miljövariablerna i en fil med namnet **\<your home folder\> \\ . PCs. \\ \<solution name\> Kuvert**. Du kan använda dem för framtida distributioner av lösnings Accelerator. Observera att alla miljövariabler som angetts på den lokala datorn åsidosätter värdena i **den \\ \\ lokala \\ . kuvert** filen för Services-skript när du kör **Docker-sammanställning**.

1. Stäng kommando rads miljön.

### <a name="use-existing-azure-resources"></a>Använd befintliga Azure-resurser

Om du redan har skapat de nödvändiga Azure-resurserna ställer du in motsvarande miljövariabler på den lokala datorn:
* **PCS_KEYVAULT_NAME**: namnet på den Key Vault resursen.
* **PCS_AAD_APPID**: Azure Active Directory (Azure AD) program-ID.
* **PCS_AAD_APPSECRET**: Azure AD Application Secret.

Konfigurations värden kommer att läsas från den här Key Vault resursen. Dessa miljövariabler kan sparas i filen **\<your home folder\> \\ . PCs \\ \<solution name\> . miljö** från distributionen. Observera att miljövariabler som har angetts på den lokala datorn åsidosätter värden i den **\\ \\ lokala \\ . kuvert** filen för Services-skript när du kör **Docker-sammanställning**.

En del av konfigurationen som krävs av mikrotjänsten lagras i en instans av Key Vault som skapades vid den första distributionen. Motsvarande variabler i nyckel valvet bör ändras efter behov.

## <a name="run-the-microservices"></a>Kör mikrotjänster

I det här avsnittet ska du köra mikrotjänster för fjärr övervakning. Du kör:

* Webb gränssnittet internt.
* Azure IoT Device simulering, auth och Azure Stream Analytics Manager-tjänster i Docker.
* Mikrotjänsterna i IntelliJ.

### <a name="run-the-device-simulation-service"></a>Kör enhets simulerings tjänsten

Öppna ett nytt kommando tolks fönster. Kontrol lera att du har åtkomst till de miljövariabler som anges av skriptet **Start. cmd** i föregående avsnitt.

Kör följande kommando för att öppna Docker-behållaren för enhets simulerings tjänsten. Tjänsten simulerar enheter för fjärr styrnings lösningen.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Köra auth-tjänsten

Öppna ett nytt kommando tolks fönster och kör sedan följande kommando för att öppna Docker-behållaren för auth-tjänsten. Genom att använda den här tjänsten kan du hantera de användare som har behörighet att komma åt Azure IoT-lösningar.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>Kör tjänsten Stream Analytics Manager

Öppna ett nytt kommando tolks fönster och kör sedan följande kommando för att öppna Docker-behållaren för tjänsten Stream Analytics Manager. Med den här tjänsten kan du hantera Stream Analytics-jobb. Sådan hantering omfattar inställning av jobb konfiguration och Start, stopp och övervakning av jobb status.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>Distribuera alla andra mikrotjänster på den lokala datorn

Följande steg visar hur du kör mikrotjänster för fjärr styrning i IntelliJ.

#### <a name="import-a-project"></a>Importera ett projekt

1. Öppna IntelliJ IDE.
1. Välj **Importera projekt**.
1. Välj **Azure-IoT-PCs-Remote-Monitoring-java\services\build.SBT**.

#### <a name="create-run-configurations"></a>Skapa körnings konfigurationer

1. Välj **Kör**  >  **Redigera konfigurationer**.
1. Välj **Lägg till ny konfiguration**  >  **SBT uppgift**.
1. Ange **namn** och ange sedan **aktiviteter** som **Kör**.
1. Välj **arbets katalog** baserat på den tjänst som du vill köra.
1. Välj **tillämpa**  >  **OK** för att spara dina val.
1. Skapa kör konfigurationer för följande webb tjänster:
    * Webbtjänst (services\config)
    * Webbtjänst (services\device-Telemetry)
    * Webbtjänst (services\iothub-Manager)
    * Webbtjänst (services\storage-adapter)

I följande bild visas ett exempel på hur du lägger till en konfiguration för en tjänst:

[![Skärm bild av fönstret IntelliJ IDE Run/debug Configurations, som visar alternativet storageAdapter markerat i listan SBT tasks i den vänstra rutan och poster i rutorna namn, uppgifter, arbets katalog och VM-parametrar i den högra rutan.](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>Skapa en sammansatt konfiguration

1. Om du vill köra alla tjänster tillsammans väljer du **Lägg till ny konfiguration**  >  **sammansatt**.
1. Ange **ett namn** och välj sedan **Lägg till SBT-aktiviteter**.
1. Välj **tillämpa**  >  **OK** för att spara dina val.

I följande bild visas ett exempel på hur du lägger till alla SBT-aktiviteter i en enda konfiguration:

[![Skärm bild av fönstret IntelliJ IDE Run/debug Configurations, som visar alternativet AllServices markerat i den sammanslagna listan i det vänstra fönstret och alternativet SBT Task deviceTelemetry är markerat i den högra rutan.](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Välj **Kör** för att skapa och köra webb tjänsterna på den lokala datorn.

Varje webb tjänst öppnar ett kommando tolks fönster och webbläsarfönster. I kommando tolken visas utdata från den aktiva tjänsten. I webbläsarfönstret kan du övervaka statusen. Stäng inte kommando tolken Windows eller webb sidor eftersom de här åtgärderna stoppar webb tjänsten.

Gå till följande URL: er för att få åtkomst till tjänsternas status:

* IoT-Hub Manager: `http://localhost:9002/v1/status`
* Telemetri för enhet: `http://localhost:9004/v1/status`
* konfigurationsfil `http://localhost:9005/v1/status`
* lagrings kort: `http://localhost:9022/v1/status`

### <a name="start-the-stream-analytics-job"></a>Starta Stream Analytics jobbet

Följ de här stegen för att starta Stream Analytics jobbet:

1. Gå till [Azure-portalen](https://portal.azure.com).
1. Gå till **resurs gruppen** som har skapats för din lösning. Namnet på resurs gruppen är det namn som du valde för lösningen när du körde skriptet **Start. cmd** .
1. Välj **Stream Analytics jobb** i listan över resurser.
1. På sidan Stream Analytics jobb **Översikt** väljer du knappen **Start** och sedan **Starta** för att starta jobbet.

### <a name="run-the-web-ui"></a>Kör webb gränssnittet

I det här steget startar du webb gränssnittet. Öppna ett nytt kommando tolks fönster. Kontrol lera att du har åtkomst till de miljövariabler som anges av skriptet **Start. cmd** . Gå till mappen **webui** i din lokala kopia av lagrings platsen och kör sedan följande kommandon:

```cmd
npm install
npm start
```

När **Start** kommandot har slutförts visas sidan på adressen i webbläsaren `http://localhost:3000/dashboard` . Felen på den här sidan förväntas. Om du vill visa programmet utan fel utför du följande steg.

### <a name="configure-and-run-nginx"></a>Konfigurera och kör nginx

Konfigurera en omvänd proxyserver som länkar webb programmet till mikrotjänster som körs på den lokala datorn:

1. Kopiera filen **nginx. conf** från mappen **webui\scripts\localhost** i din lokala kopia av lagrings platsen till installations katalogen för **nginx\conf** .
1. Kör nginx.

Mer information om hur du kör nginx finns i [nginx för Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Ansluta till instrument panelen

Öppna instrument panelen för fjärrövervakning genom att gå till `http://localhost:9000` i webbläsaren.

## <a name="clean-up"></a>Rensa

Undvik onödiga kostnader genom att ta bort moln tjänsterna från din Azure-prenumeration när du är klar med testet. Om du vill ta bort tjänsterna går du till [Azure Portal](https://ms.portal.azure.com)och tar bort resurs gruppen som skriptet **starta. cmd** skapade.

Du kan också ta bort den lokala kopian av lagrings platsen för fjärrövervakning som skapades när du klonade käll koden från GitHub.

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat lösningen för fjärrövervakning är nästa steg att [utforska funktionerna i lösningens instrument panel](quickstart-remote-monitoring-deploy.md).
