---
title: Distribuera lösningen för fjärrövervakning lokalt (via Visual i Studio IDE) – Azure | Microsoft Docs
description: Den här guiden visar hur du distribuerar den lösningsacceleratorn för fjärrövervakningen till den lokala datorn med hjälp av Visual Studio för utveckling och testning.
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: fdb9b6c09e81ce29d2587fb0faca1aa80d28ea38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450604"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>Distribuera lösningsacceleratorn för fjärrövervakning lokalt – Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Den här artikeln visar hur du distribuerar lösningsacceleratorn för fjärrövervakning till din lokala dator för utveckling och testning. Du lär dig hur du kör mikrotjänster i Visual Studio. En lokal mikrotjänster distribution använder följande molntjänster: IoT Hub, Cosmos DB, Azure Streaming Analytics och Azure Time Series Insights-tjänster i molnet.

Om du vill köra lösningsacceleratorn för fjärrövervakning i Docker på din lokala dator, se [och distribuera lösningsacceleratorn för fjärrövervakning lokalt - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att distribuera Azure-tjänsterna används av lösningsacceleratorn för fjärrövervakning, behöver du en aktiv Azure-prenumeration.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Dator-installationen

För att slutföra lokal distribution, behöver du följande verktygen som installeras på din lokala utvecklingsdator:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) -programvaran är en förutsättning för PCS CLI som skript som använder för att skapa Azure-resurser. Använd inte Node.js v10.

> [!NOTE]
> Visual Studio är tillgängligt för Windows och Mac.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Kör mikrotjänster

I det här avsnittet ska köra du mikrotjänster för fjärrövervakning. Du kör webbgränssnittet internt, tjänsten Enhetssimulering i Docker och mikrotjänster i Visual Studio.

### <a name="run-the-device-simulation-service"></a>Köra tjänsten enheten simulering

Öppna ett nytt kommandofönster för att se till att du har åtkomst till miljövariabler som anges av den **start.cmd** skriptet i föregående avsnitt.

Kör följande kommando för att starta Docker-behållare för device simulering-tjänsten. Tjänsten simulerar enheter för lösningen för fjärrövervakning.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Distribuera alla mikrotjänster på den lokala datorn

Följande steg visar hur du kör fjärrövervakning-mikrotjänster i Visual Studio 2017:

1. Starta Visual Studio 2017
1. Öppna den **remote monitoring.sln** lösning i den **services** mapp i den lokala kopian av databasen.
1. I **Solution Explorer**, högerklicka på lösningen och klicka på **egenskaper**.
1. Välj **gemensamma egenskaper > Startprojekt**.
1. Välj **flera Startprojekt** och ange **åtgärd** till **starta** för följande projekt:
    * Webbtjänsten (asa-manager\WebService)
    * Webbtjänsten (auth\WebService)
    * Webbtjänsten (config\WebService)
    * Webbtjänsten (enheten telemetry\WebService)
    * Webbtjänsten (iothub-manager\WebService)
    * Webbtjänsten (lagring-adapter\WebService)
1. Klicka på **OK** att spara dina val.
1. Klicka på **Felsök > Starta felsökning** att skapa och köra webbtjänsterna på den lokala datorn.

Varje webbtjänst öppnas en kommandotolk och web webbläsarfönster. Du ser utdata från tjänsten som körs vid kommandotolken och webbläsarfönstret kan du övervaka status. Stäng inte kommandotolkar eller webbsidor, den här åtgärden stoppar webbtjänsten.

### <a name="start-the-stream-analytics-job"></a>Starta Stream Analytics-jobbet

Följ stegen nedan för att starta Stream Analytics-jobbet:

1. Navigera till [Azure-portalen](https://portal.azure.com).
1. Navigera till den **resursgrupp** skapats för din lösning. Namnet på resursgruppen är det namn du valde för din lösning när du körde den **start.cmd** skriptet **.
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
