---
title: Distribuera lösningen för fjärrövervakning lokalt (via IntelliJ IDE) – Azure | Microsoft Docs
description: Den här guiden visar hur du distribuerar den lösningsacceleratorn för fjärrövervakningen till den lokala datorn med IntelliJ för utveckling och testning.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 209a91453875531e81ddbcead8c1ed2fb309a0a0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147454"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Distribuera lösningsacceleratorn för fjärrövervakning lokalt - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Den här artikeln visar hur du distribuerar lösningsacceleratorn för fjärrövervakning till din lokala dator för utveckling och testning. Du lär dig hur du kör mikrotjänster i IntelliJ. En lokal mikrotjänster distribution använder följande molntjänster: IoT Hub, Cosmos DB, Azure Streaming Analytics och Azure Time Series Insights-tjänster i molnet.

Om du vill köra lösningsacceleratorn för fjärrövervakning i Docker på din lokala dator, se [och distribuera lösningsacceleratorn för fjärrövervakning lokalt - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att distribuera Azure-tjänsterna används av lösningsacceleratorn för fjärrövervakning, behöver du en aktiv Azure-prenumeration.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Dator-installationen

För att slutföra lokal distribution, behöver du följande verktygen som installeras på din lokala utvecklingsdator:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/)
* [IntelliJ Plugin Scala](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ Plugin SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ Plugin SBT Executor](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) -programvaran är en förutsättning för PCS CLI som skript som använder för att skapa Azure-resurser. Använd inte Node.js v10.

> [!NOTE]
> IntelliJ IDE är tillgängligt för Windows och Mac.

[!INCLUDE [iot-accelerators-local-setup-java](../../includes/iot-accelerators-local-setup-java.md)]

## <a name="run-the-microservices"></a>Kör mikrotjänster

I det här avsnittet ska köra du mikrotjänster för fjärrövervakning. Du kör webbgränssnittet internt, den Enhetssimulering, autentisering och ASA Manager-tjänsten i Docker och mikrotjänster i IntelliJ.

### <a name="run-the-device-simulation-service"></a>Köra tjänsten enheten simulering

Öppna ett nytt kommandofönster för att se till att du har åtkomst till miljövariabler som anges av den **start.cmd** skriptet i föregående avsnitt.

Kör följande kommando för att starta Docker-behållare för device simulering-tjänsten. Tjänsten simulerar enheter för lösningen för fjärrövervakning.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Kör tjänsten Auth

Öppna ett nytt kommandofönster och kör följande kommando för att starta Docker-behållare för Auth-tjänsten. Tjänsten kan hantera användare behörighet att komma åt Azure IoT-lösningar.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-asa-manager-service"></a>Kör ASA Manager-tjänsten

Öppna ett nytt kommandofönster och kör följande kommando för att starta Docker-behållare för ASA Manager-tjänsten. Tjänsten möjliggör hantering av Azure Stream Analytics (ASA) jobb, inklusive ställa in konfigurationen och Start, stopp och övervaka deras status.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Distribuera alla mikrotjänster på den lokala datorn

Följande steg visar hur du kör fjärrövervakning-mikrotjänster i IntelliJ:

#### <a name="import-project"></a>Importera projekt

1. Starta IntelliJ IDE
1. Välj **Importera projekt** och välj **azure-iot-pcs-remote-monitoring-java\services\build.sbt**

#### <a name="create-run-configurations"></a>Skapa kör konfigurationer

1. Välj **kör > Redigera konfigurationer**
1. Välj **Lägg till ny konfiguration > segregerade barlasttankar uppgift** 
1. Ange **namn** och ange **uppgifter** som körs 
1. Välj den **Working Directory** baserat på vilken tjänst som du vill köra
1. Klicka på **tillämpa > Ok** att spara dina val.
1. Skapa kör konfigurationer för följande tjänster:
    * Webbtjänsten (services\config)
    * Webbtjänsten (services\device telemetri)
    * Webbtjänsten (services\iothub manager)
    * Webbtjänsten (services\storage-adapter)

Som ett exempel på följande bild visar att lägga till konfigurationen för en tjänst:

[![Lägg till konfiguration](./media/deploy-locally-intelliJ/run-configurations.png)](./media/deploy-locally-intelliJ/run-configurations.png#lightbox)


#### <a name="create-compound-configuration"></a>Skapa sammansatt konfiguration

1. Om du vill köra alla tjänster tillsammans Välj **lägga till en ny konfiguration > sammansatt**
1. Ange den **namn** och **lägga till segregerade barlasttankar aktiviteter**
1. Klicka på **tillämpa > Ok** att spara dina val.

Visar att lägga till alla segregerade barlasttankar uppgifter i konfigurationen för enkel exempelvis följande bild:


[![Add-All-Services](./media/deploy-locally-intelliJ/all-services.png)](./media/deploy-locally-intelliJ/all-services.png#lightbox)



1. Klicka på **kör** att skapa och köra webbtjänsterna på den lokala datorn.

Varje webbtjänst öppnas en kommandotolk och web webbläsarfönster. Du ser utdata från tjänsten som körs vid kommandotolken och webbläsarfönstret kan du övervaka status. Stäng inte kommandotolkar eller webbsidor, den här åtgärden stoppar webbtjänsten.


För att komma åt status för tjänsterna, kan du navigera till följande webbadresser:
* IoT Hub-hanterare [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Enhetstelemetri  [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* config [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* Storage-adapter [http://localhost:9022/v1/status](http://localhost:9022/v1/status)


### <a name="start-the-stream-analytics-job"></a>Starta Stream Analytics-jobbet

Följ stegen nedan för att starta Stream Analytics-jobbet:

1. Navigera till [Azure-portalen](https://portal.azure.com).
1. Navigera till den **resursgrupp** skapats för din lösning. Namnet på resursgruppen är det namn du valde för din lösning när du körde den **start.cmd** skript.
1. Klicka på den **Stream Analytics-jobbet** i listan över resurser.
1. På Stream Analytics-jobbet **översikt** klickar du på den **starta** knappen. Klicka sedan på **starta** att starta jobbet nu.

### <a name="run-the-web-ui"></a>Kör webbgränssnittet

I det här steget ska starta du webbgränssnittet. Öppna ett nytt kommandofönster för att se till att du har åtkomst till miljövariabler som anges av den **start.cmd** skript. Navigera till den **webbgränssnittet** mappen i din lokala kopia av databasen och kör följande kommandon:

```cmd
npm install
npm start
```

När början är klar visas sidan i webbläsaren **http:\//localhost:3000 / instrumentpanel**. Fel på den här sidan förväntas. Följ anvisningarna nedan om du vill visa programmet utan fel.

### <a name="configure-and-run-nginx"></a>Konfigurera och köra NGINX

Ställa in en omvänd proxy-server för att länka webbprogram och mikrotjänster som körs på den lokala datorn:

* Kopiera den **nginx.conf** fil från den **webui\scripts\localhost** mapp i din lokala kopia av lagringsplatsen till den **nginx\conf** installationskatalog.
* Kör **nginx**.

Mer information om att köra **nginx**, se [nginx för Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Ansluta till instrumentpanelen

Om du vill komma åt instrumentpanelen för fjärrövervakning lösningen måste gå till http:\//localhost:9000 i webbläsaren.

## <a name="clean-up"></a>Rensa

Ta bort molntjänsterna från din Azure-prenumeration för att undvika onödiga avgifter när du är klar med testet. Om du vill ta bort tjänsterna, navigera till den [Azure-portalen](https://ms.portal.azure.com) och ta bort resursen som den **start.cmd** skriptet som du skapade.

Du kan också ta bort den lokala kopian av databasen fjärrövervakning skapas när du har klonat källkoden från GitHub.

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat av lösningen för fjärrövervakning, nästa steg är att [utforska funktionerna i lösningens instrumentpanel](quickstart-remote-monitoring-deploy.md).
