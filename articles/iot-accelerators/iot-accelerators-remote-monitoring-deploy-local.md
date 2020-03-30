---
title: Distribuera fjärrövervakningslösning lokalt – VS IDE - Azure | Microsoft-dokument
description: Den här programguiden visar hur du distribuerar snabbacceleratorn för fjärrövervakningslösningar till din lokala dator med Visual Studio för testning och utveckling.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a1eba1fceb959bd475d205176c2c53f6409fdc77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890894"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>Distribuera lösningsacceleratorn för fjärrövervakning lokalt - Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Den här artikeln visar hur du distribuerar lösningsacceleratorn för fjärrövervakning till din lokala dator för testning och utveckling. Du lär dig hur du kör mikrotjänsterna i Visual Studio. En lokal mikrotjänstdistribution använder följande molntjänster: IoT Hub, Cosmos DB, Azure Streaming Analytics och Azure Time Series Insights-tjänster i molnet.

Om du vill köra lösningen för fjärrövervakning i Docker på den lokala datorn läser [du Distribuera lösningsacceleratorn för fjärrövervakning lokalt - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Krav

Om du vill distribuera Azure-tjänster som används av lösningsacceleratorn för fjärrövervakning behöver du en aktiv Azure-prenumeration.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Installation av maskin

För att slutföra den lokala distributionen behöver du följande verktyg installerade på din lokala utvecklingsdator:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) - den här programvaran är en förutsättning för PCS CLI som skripten använder för att skapa Azure-resurser. Använd inte Node.js v10.

> [!NOTE]
> Visual Studio är tillgängligt för Windows och Mac.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Kör mikrotjänsterna

I det här avsnittet kör du mikrotjänsterna Fjärrövervakning. Du kör webbgränssnittet internt, enhetssimuleringstjänsten i Docker och mikrotjänsterna i Visual Studio.

### <a name="run-the-device-simulation-service"></a>Kör enhetssimuleringstjänsten

Öppna ett nytt kommandotolksfönster för att vara säker på att du har åtkomst till de miljövariabler som angetts av **start.cmd-skriptet** i föregående avsnitt.

Kör följande kommando för att starta Docker-behållaren för enhetssimuleringstjänsten. Tjänsten simulerar enheter för fjärrövervakningslösningen.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Distribuera alla andra mikrotjänster på lokal dator

Följande steg visar hur du kör mikrotjänsterna Fjärrövervakning i Visual Studio:

1. Starta Visual Studio.
1. Öppna **remote-monitoring.sln-lösningen** i **tjänstemappen** i den lokala kopian av databasen.
1. Högerklicka på lösningen i **Solution Explorer**och klicka på **Egenskaper**.
1. Välj **Vanliga egenskaper > startprojekt**.
1. Välj **Flera startprojekt** och ange **Åtgärd** till **Start** för följande projekt:
    * WebService (asa-manager\WebService)
    * WebService (auth\WebService)
    * WebService (config\WebService)
    * WebService (enhetstelemetri\WebService)
    * WebService (iothub-manager\WebService)
    * WebService (lagringskort\WebService)
1. Spara dina val genom att klicka på **OK.**
1. Klicka på **Felsökning > Starta felsökning för** att skapa och köra webbtjänsterna på den lokala datorn.

Varje webbtjänst öppnar ett kommandotolks- och webbläsarfönster. I kommandotolken visas utdata från den tjänst som körs och i webbläsarfönstret kan du övervaka statusen. Stäng inte kommandotolken eller webbsidorna, den här åtgärden stoppar webbtjänsten.

### <a name="start-the-stream-analytics-job"></a>Starta Stream Analytics-jobbet

Så här startar du Stream Analytics-jobbet:

1. Navigera till [Azure-portalen](https://portal.azure.com).
1. Navigera till den **resursgrupp** som skapats för din lösning. Namnet på resursgruppen är det namn du valde för din lösning när du körde **start.cmd-skriptet.**
1. Klicka på **Stream Analytics-jobbet** i listan över resurser.
1. Klicka på **Start-knappen** på sidan **Webbplatsöversikt** för Stream Analytics-jobb. Klicka sedan på **Start** för att starta jobbet nu.

### <a name="run-the-web-ui"></a>Kör webbgränssnittet

I det här steget startar du webbgränssnittet. Öppna ett nytt kommandotolksfönster för att vara säker på att du har åtkomst till de miljövariabler som anges av **start.cmd-skriptet.** Navigera till **webui-mappen** i den lokala kopian av databasen och kör följande kommandon:

```cmd
npm install
npm start
```

När starten är klar visar webbläsaren sidan **\/http: /localhost:3000/dashboard**. Felen på den här sidan förväntas. Om du vill visa programmet utan fel slutför du följande steg.

### <a name="configure-and-run-nginx"></a>Konfigurera och köra NGINX

Konfigurera en omvänd proxyserver för att länka webbprogrammet och mikrotjänster som körs på den lokala datorn:

* Kopiera filen **nginx.conf** från mappen **webui\scripts\localhost** i den lokala kopian av databasen till installationskatalogen **nginx\conf.**
* Kör **nginx**.

Mer information om hur du kör **nginx**finns [i nginx för Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Anslut till instrumentpanelen

Om du vill komma åt instrumentpanelen för fjärrövervakningslösning navigerar du till http:\//localhost:9000 i webbläsaren.

## <a name="clean-up"></a>Rensa

För att undvika onödiga avgifter, när du är klar med din testning ta bort molntjänster från din Azure-prenumeration. Om du vill ta bort tjänsterna navigerar du till [Azure-portalen](https://ms.portal.azure.com) och tar bort resursgruppen som **start.cmd-skriptet** skapade.

Du kan också ta bort den lokala kopian av databasen för fjärrövervakning som skapades när du klonade källkoden från GitHub.

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat lösningen för fjärrövervakning är nästa steg att [utforska funktionerna på instrumentpanelen för lösningen](quickstart-remote-monitoring-deploy.md).
