---
title: Distribuera fjärr styrnings lösningen lokalt – VS IDE-Azure | Microsoft Docs
description: Den här instruktions guiden visar hur du distribuerar lösnings acceleratorn för fjärrövervakning till din lokala dator med Visual Studio för testning och utveckling.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a1eba1fceb959bd475d205176c2c53f6409fdc77
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024154"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>Distribuera lösnings acceleratorn för fjärrövervakning lokalt – Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Den här artikeln visar hur du distribuerar lösnings acceleratorn för fjärrövervakning till din lokala dator för testning och utveckling. Du lär dig hur du kör mikrotjänster i Visual Studio. En lokal distribution av mikrotjänster använder följande moln tjänster: IoT Hub, Cosmos DB, Azure streaming Analytics och Azure Time Series Insights tjänster i molnet.

Om du vill köra lösnings acceleratorn för fjärrövervakning i Docker på den lokala datorn läser [du distribuera lösnings Accelerator för fjärrövervakning lokalt Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill distribuera de Azure-tjänster som används av lösningen för fjärrövervakning, behöver du en aktiv Azure-prenumeration.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Dator konfiguration

För att slutföra den lokala distributionen behöver du följande verktyg installerade på den lokala utvecklings datorn:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js V8](https://nodejs.org/) – den här program varan är nödvändig för de PC CLI-datorer som skripten använder för att skapa Azure-resurser. Använd inte Node.js v10.

> [!NOTE]
> Visual Studio är tillgängligt för Windows och Mac.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Kör mikrotjänster

I det här avsnittet ska du köra mikrotjänster för fjärr övervakning. Du kör webb gränssnittet internt, enhets simulerings tjänsten i Docker och mikrotjänsterna i Visual Studio.

### <a name="run-the-device-simulation-service"></a>Kör enhets simulerings tjänsten

Öppna ett nytt kommando tolks fönster för att se till att du har åtkomst till de miljövariabler som anges av skriptet **Start. cmd** i föregående avsnitt.

Kör följande kommando för att starta Docker-behållaren för enhets simulerings tjänsten. Tjänsten simulerar enheter för fjärr styrnings lösningen.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Distribuera alla andra mikrotjänster på den lokala datorn

Följande steg visar hur du kör mikrotjänster för fjärr styrning i Visual Studio:

1. Starta Visual Studio.
1. Öppna lösningen **Remote-Monitoring. SLN** i mappen **tjänster** i din lokala kopia av lagrings platsen.
1. I **Solution Explorer** högerklickar du på lösningen och klickar på **Egenskaper**.
1. Välj **gemensamma egenskaper > start projekt**.
1. Välj **flera start projekt** och ange **åtgärd** att **Starta** för följande projekt:
    * Webbtjänst (asa-manager\WebService)
    * Webbtjänst (auth\WebService)
    * Webbtjänst (config\WebService)
    * Webbtjänst (device-telemetry\WebService)
    * Webbtjänst (iothub-manager\WebService)
    * Webbtjänst (storage-adapter\WebService)
1. Spara dina val genom att klicka på **OK** .
1. Klicka på **felsök > starta fel sökning** för att skapa och köra webb tjänsterna på den lokala datorn.

Varje webb tjänst öppnar en kommando tolk och ett webbläsarfönster. I kommando tolken visas utdata från den aktiva tjänsten och webbläsarfönstret gör att du kan övervaka statusen. Stäng inte kommando tolken eller webb sidor. den här åtgärden stoppar webb tjänsten.

### <a name="start-the-stream-analytics-job"></a>Starta Stream Analytics jobbet

Följ de här stegen för att starta Stream Analytics jobbet:

1. Navigera till [Azure Portal](https://portal.azure.com).
1. Navigera till **resurs gruppen** som har skapats för din lösning. Namnet på resurs gruppen är det namn som du valde för lösningen när du körde skriptet **Start. cmd** .
1. Klicka på **Stream Analytics jobb** i listan över resurser.
1. På sidan Stream Analytics jobb **Översikt** klickar du på knappen **Starta** . Klicka sedan på **Starta** för att starta jobbet nu.

### <a name="run-the-web-ui"></a>Kör webb gränssnittet

I det här steget startar du webb gränssnittet. Öppna ett nytt kommando tolks fönster för att se till att du har åtkomst till de miljövariabler som anges av skriptet **Start. cmd** . Navigera till mappen **webui** i din lokala kopia av lagrings platsen och kör följande kommandon:

```cmd
npm install
npm start
```

När starten är klar visar webbläsaren sidan **http: \/ /localhost: 3000/Dashboard**. Felen på den här sidan förväntas. Om du vill visa programmet utan fel slutför du följande steg.

### <a name="configure-and-run-nginx"></a>Konfigurera och kör NGINX

Konfigurera en omvänd proxyserver för att länka webb programmet och mikrotjänster som körs på den lokala datorn:

* Kopiera filen **nginx. conf** från mappen **webui\scripts\localhost** i din lokala kopia av lagrings platsen till **nginx\conf** installations katalog.
* Kör **nginx**.

Mer information om hur du kör **nginx** finns i [nginx för Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Ansluta till instrument panelen

Öppna instrument panelen för fjärrövervakning genom att gå till http: \/ /localhost: 9000 i webbläsaren.

## <a name="clean-up"></a>Rensa

För att undvika onödiga kostnader när du har utfört testet tar du bort moln tjänsterna från din Azure-prenumeration. Om du vill ta bort tjänsterna navigerar du till [Azure Portal](https://ms.portal.azure.com) och tar bort resurs gruppen som skriptet **starta. cmd** skapade.

Du kan också ta bort den lokala kopian av lagrings platsen för fjärrövervakning som skapats när du klonade käll koden från GitHub.

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat lösningen för fjärrövervakning är nästa steg att [utforska funktionerna i lösningens instrument panel](quickstart-remote-monitoring-deploy.md).
