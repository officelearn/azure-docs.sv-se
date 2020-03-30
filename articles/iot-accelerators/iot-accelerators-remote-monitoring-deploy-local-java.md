---
title: Distribuera fjärrövervakningslösning lokalt - IntelliJ IDE - Azure | Microsoft-dokument
description: Den här programguiden visar hur du distribuerar lösningsacceleratorn för fjärrövervakning till din lokala dator med IntelliJ för testning och utveckling.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 779ee1e057d74b11c5e0ba58dc2fd32b803f1e0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888814"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Distribuera lösningsacceleratorn för fjärrövervakning lokalt - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Den här artikeln visar hur du distribuerar lösningsacceleratorn för fjärrövervakning till din lokala dator för testning och utveckling. Du får lära dig hur du kör mikrotjänsterna i IntelliJ. En lokal mikrotjänstdistribution använder följande molntjänster: IoT Hub, Azure Cosmos DB, Azure Streaming Analytics och Azure Time Series Insights.

Om du vill köra lösningen för fjärrövervakning i Docker på den lokala datorn läser [du Distribuera lösningsacceleratorn för fjärrövervakning lokalt - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Krav

Om du vill distribuera Azure-tjänster som används av lösningsacceleratorn för fjärrövervakning behöver du en aktiv Azure-prenumeration.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Installation av maskin

För att slutföra den lokala distributionen behöver du följande verktyg installerade på din lokala utvecklingsdator:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Gemenskapen Edition](https://www.jetbrains.com/idea/download/)
* [IntelliJ Scala plugin](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ SBT plugin](https://plugins.jetbrains.com/plugin/5007-sbt)
* [Plugin-program för IntelliJ SBT Executor](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/)

Node.js v8 är en förutsättning för PCS CLI som skripten använder för att skapa Azure-resurser. Använd inte Node.js v10.

> [!NOTE]
> IntelliJ IDE är tillgängligt för Windows och Mac.

## <a name="download-the-source-code"></a>Ladda ner källkoden

Källkodsdatabaserna för fjärrövervakning innehåller källkoden och de Docker-konfigurationsfiler som du behöver för att köra dockeravbildningarna för mikrotjänster.

Om du vill klona och skapa en lokal version av databasen använder du kommandoradsmiljön för att gå till en lämplig mapp på den lokala datorn. Kör sedan en av följande uppsättningar kommandon för att klona Java-databasen:

* Om du vill hämta den senaste versionen av Java-implementeringarna för mikrotjänst kör du följande kommando:

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* Om du vill hämta de senaste undermodulerna kör du följande kommandon:

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> Dessa kommandon hämtar källkoden för alla mikrotjänster utöver de skript som du använder för att köra mikrotjänsterna lokalt. Du behöver inte källkoden för att köra mikrotjänsterna i Docker. Men källkoden är användbar om du senare planerar att ändra lösningsacceleratorn och testa ändringarna lokalt.

## <a name="deploy-the-azure-services"></a>Distribuera Azure-tjänsterna

Även om den här artikeln visar hur du kör mikrotjänsterna lokalt, beror de på Azure-tjänster som körs i molnet. Använd följande skript för att distribuera Azure-tjänsterna. Skriptexemplen förutsätter att du använder Java-databasen på en Windows-dator. Om du arbetar i en annan miljö justerar du sökvägarna, filnamnstilläggen och sökvägsavgränsarna på rätt sätt.

### <a name="create-new-azure-resources"></a>Skapa nya Azure-resurser

Om du ännu inte har skapat de nödvändiga Azure-resurserna gör du så här:

1. I kommandoradsmiljön går du till mappen **\services\scripts\local\launch** i den klonade kopian av databasen.

1. Kör följande kommandon för att installera **dators CLI-verktyget** och logga in på ditt Azure-konto:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Kör **start.cmd-skriptet.** Skriptet uppmanar dig att ange följande information:

   * Ett lösningsnamn.
   * Den Azure-prenumeration som ska användas.
   * Platsen för Azure-datacentret som ska användas.

   Skriptet skapar en resursgrupp i Azure som har ditt lösningsnamn. Den här resursgruppen innehåller De Azure-resurser som lösningsacceleratorn använder. Du kan ta bort den här resursgruppen när du inte längre behöver motsvarande resurser.

   Skriptet lägger också till en uppsättning miljövariabler på den lokala datorn. Varje variabelnamn har prefixet **PCS**. Dessa miljövariabler innehåller information som gör att fjärrövervakning kan läsa dess konfigurationsvärden från en Azure Key Vault-resurs.

   > [!TIP]
   > När skriptet är klart sparas miljövariablerna i en fil som kallas ** \<\>\\lösningsnamnet\\\<\>.pcs .env**. Du kan använda dem för framtida distributioner av lösningsacceleratorer. Observera att alla miljövariabler som anges på den lokala datorn **åsidosätter\\\\\\** värdena i den lokala .env-filen för tjänster när du kör **docker-compose**.

1. Stäng kommandoradsmiljön.

### <a name="use-existing-azure-resources"></a>Använda befintliga Azure-resurser

Om du redan har skapat de nödvändiga Azure-resurserna anger du motsvarande miljövariabler på den lokala datorn:
* **PCS_KEYVAULT_NAME**: Namnet på resursen Key Vault.
* **PCS_AAD_APPID**: Azure Active Directory (Azure AD) program-ID.
* **PCS_AAD_APPSECRET**: Hemligheten bakom Azure AD-programmet.

Konfigurationsvärden läss från den här Key Vault-resursen. Dessa miljövariabler kan sparas i ** \<startmappen\>\\.pcs-lösningsnamnet\\\<\>.env-filen** från distributionen. Observera att miljövariabler som anges på din lokala dator åsidosätter värden i **\\tjänstskriptens\\lokala\\.env-fil** när du kör **docker-compose**.

En del av konfigurationen som behövs av mikrotjänsten lagras i en instans av Key Vault som skapades vid den första distributionen. Motsvarande variabler i nyckelvalvet bör ändras efter behov.

## <a name="run-the-microservices"></a>Kör mikrotjänsterna

I det här avsnittet kör du mikrotjänsterna Fjärrövervakning. Du kör:

* Webbgränssnittet internt.
* Azure IoT-enhetssimulering, Auth och Azure Stream Analytics Manager-tjänsterna i Docker.
* Mikrotjänsterna i IntelliJ.

### <a name="run-the-device-simulation-service"></a>Kör tjänsten Enhetssimulering

Öppna ett nytt kommandotolksfönster. Kontrollera att du har åtkomst till de miljövariabler som anges av **start.cmd-skriptet** i föregående avsnitt.

Kör följande kommando för att öppna Docker-behållaren för tjänsten Enhetssimulering. Tjänsten simulerar enheter för fjärrövervakningslösningen.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Kör Auth-tjänsten

Öppna ett nytt kommandotolksfönster och kör sedan följande kommando för att öppna Docker-behållaren för Auth-tjänsten. Med den här tjänsten kan du hantera de användare som har behörighet att komma åt Azure IoT-lösningar.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>Kör tjänsten Stream Analytics Manager

Öppna ett nytt kommandotolksfönster och kör sedan följande kommando för att öppna Docker-behållaren för Tjänsten Stream Analytics Manager. Med den här tjänsten kan du hantera Stream Analytics-jobb. Sådan hantering omfattar att ange jobbkonfiguration och starta, stoppa och övervaka jobbstatus.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>Distribuera alla andra mikrotjänster på din lokala dator

Följande steg visar hur du kör mikrotjänsterna Fjärrövervakning i IntelliJ.

#### <a name="import-a-project"></a>Importera ett projekt

1. Öppna IntelliJ IDE.
1. Välj **Importera Projekt**.
1. Välj **azure-iot-pcs-remote-monitoring-java\services\build.sbt**.

#### <a name="create-run-configurations"></a>Skapa körningskonfigurationer

1. Välj **Kör** > **redigeringskonfigurationer**.
1. Välj **Lägg till ny konfiguration** > **sbt uppgift**.
1. Ange **Namn**och **ange** aktiviteter som **körs**.
1. Välj **arbetskatalogen** baserat på den tjänst du vill köra.
1. Välj **Använd** > **OK** om du vill spara dina alternativ.
1. Skapa körningskonfigurationer för följande webbtjänster:
    * WebService (tjänster\config)
    * WebService (tjänster\enhets-telemetri)
    * WebService (tjänster\iothub-manager)
    * WebService (tjänster\lagringskort)

Som ett exempel visar följande avbildning hur du lägger till en konfiguration för en tjänst:

[![Skärmbild av fönstret IntelliJ IDE-körning/felsökningskonfigurationer, som visar alternativet storageAdapter markerat i listan SBT-uppgifter i den vänstra rutan och poster i rutorna Namn, Uppgifter, Arbetskatalog och VIRTUELL i den högra rutan.](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>Skapa en sammansatt konfiguration

1. Om du vill köra alla tjänster tillsammans väljer du **Lägg till ny konfigurationssubstans** > **Compound**.
1. Ange **Namn**och välj sedan **lägg till sbt-aktiviteter**.
1. Välj **Använd** > **OK** om du vill spara dina alternativ.

Som ett exempel visar följande bild hur du lägger till alla sbt-uppgifter i en enda konfiguration:

[![Skärmbild av fönstret IntelliJ IDE Run/Debug Configurations, som visar alternativet AllServices markerat i listan Sammansatta i den vänstra rutan och alternativet sbt Task 'deviceTelemetry' markerat i den högra rutan.](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Välj **Kör** för att skapa och köra webbtjänsterna på den lokala datorn.

Varje webbtjänst öppnar ett kommandotolksfönster och webbläsarfönster. I kommandotolken visas utdata från den tjänst som körs. I webbläsarfönstret kan du övervaka statusen. Stäng inte kommandotolksfönster eller webbsidor, eftersom dessa åtgärder stoppar webbtjänsten.

Om du vill komma åt tjänsternas status går du till följande webbadresser:

* IoT-Hub Manager:[http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Enhet telemetri:[http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* Config:[http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* lagringsadapter:[http://localhost:9022/v1/status](http://localhost:9022/v1/status)

### <a name="start-the-stream-analytics-job"></a>Starta Stream Analytics-jobbet

Så här startar du Stream Analytics-jobbet:

1. Gå till [Azure-portalen](https://portal.azure.com).
1. Gå till den **resursgrupp** som skapats för din lösning. Namnet på resursgruppen är det namn du valde för din lösning när du körde **start.cmd-skriptet.**
1. Välj **Stream Analytics-jobbet** i listan över resurser.
1. På sidan Översikt **över** Stream Analytics-jobb väljer du **Start-knappen** och väljer sedan **Start** för att starta jobbet.

### <a name="run-the-web-ui"></a>Kör webbgränssnittet

I det här steget startar du webbgränssnittet. Öppna ett nytt kommandotolksfönster. Kontrollera att du har åtkomst till de miljövariabler som anges av **start.cmd-skriptet.** Gå till **webui-mappen** i den lokala kopian av databasen och kör sedan följande kommandon:

```cmd
npm install
npm start
```

När **startkommandot** är klart visar webbläsaren sidan [http://localhost:3000/dashboard](http://localhost:3000/dashboard)på adressen . Felen på den här sidan förväntas. Om du vill visa programmet utan fel slutför du följande steg.

### <a name="configure-and-run-nginx"></a>Konfigurera och köra Nginx

Konfigurera en omvänd proxyserver som länkar webbprogrammet till de mikrotjänster som körs på den lokala datorn:

1. Kopiera filen **nginx.conf** från mappen **webui\scripts\localhost** i den lokala kopian av databasen till installationskatalogen **nginx\conf.**
1. Kör Nginx.

Mer information om hur du kör Nginx finns i [nginx för Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Anslut till instrumentpanelen

Om du vill komma åt http://localhost:9000 instrumentpanelen för fjärrövervakningslösning går du till i webbläsaren.

## <a name="clean-up"></a>Rensa

Undvik onödiga debiteringar genom att ta bort molntjänsterna från din Azure-prenumeration när du har slutfört testningen. Om du vill ta bort tjänsterna går du till [Azure-portalen](https://ms.portal.azure.com)och tar bort resursgruppen som **start.cmd-skriptet** skapade.

Du kan också ta bort den lokala kopian av databasen för fjärrövervakning som skapades när du klonade källkoden från GitHub.

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat lösningen för fjärrövervakning är nästa steg att [utforska funktionerna på instrumentpanelen för lösningen](quickstart-remote-monitoring-deploy.md).
