---
title: Distribuera fjärrövervakningslösning lokalt – Visual Studio Code – Azure | Microsoft-dokument
description: Den här programguiden visar hur du distribuerar snabbacceleratorn för fjärrövervakningslösning till din lokala dator med Visual Studio Code för testning och utveckling.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d20e9a6a78d99a23fe4b98aeb4f3eb8359da7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890964"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Distribuera lösningsacceleratorn för fjärrövervakning lokalt - Visual Studio-kod

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Den här artikeln visar hur du distribuerar lösningsacceleratorn för fjärrövervakning till din lokala dator för testning och utveckling. Du lär dig hur du kör mikrotjänsterna i Visual Studio Code. En lokal mikrotjänstdistribution använder följande molntjänster: IoT Hub, Cosmos DB, Azure Streaming Analytics och Azure Time Series Insights.

## <a name="prerequisites"></a>Krav

Om du vill distribuera Azure-tjänster som används av lösningsacceleratorn för fjärrövervakning behöver du en aktiv Azure-prenumeration.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Installation av maskin

För att slutföra den lokala distributionen behöver du följande verktyg installerade på din lokala utvecklingsdator:

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio-kod](https://code.visualstudio.com/)
* [VS-kodens C#-tillägg](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js v8](https://nodejs.org/) - den här programvaran är en förutsättning för PCS CLI som skripten använder för att skapa Azure-resurser. Använd inte Node.js v10

> [!NOTE]
> Visual Studio Code är tillgänglig för Windows, Mac och Ubuntu.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Kör mikrotjänsterna

I det här avsnittet kör du mikrotjänsterna Fjärrövervakning. Du kör webbgränssnittet internt, enhetssimuleringstjänsten i Docker och mikrotjänsterna i Visual Studio-kod.

### <a name="build-the-code"></a>Skapa koden

Navigera till azure-iot-pcs-remote-monitoring-dotnet\services i kommandotolken och kör följande kommandon för att skapa koden.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Distribuera alla andra mikrotjänster på lokal dator

Följande steg visar hur du kör mikrotjänsterna Fjärrövervakning i Visual Studio-kod:

1. Starta Visual Studio Code.
1. Öppna mappen **azure-iot-pcs-remote-monitoring-dotnet** i VS-kod.
1. Skapa en ny mapp med namnet **.vscode** i mappen **azure-iot-pcs-remote-monitoring-dotnet.**
1. Kopiera filerna **launch.json** och **tasks.json** från services\scripts\local\launch\idesettings\vscode till **den vscode-mapp** som du just skapade.
1. Öppna **felsökningspanelen** i VS-kod och kör konfigurationen **Kör alla mikrotjänster.** Den här konfigurationen kör enhetssimuleringsmikrotjänsten i Docker och kör de andra mikrotjänsterna i felsökningshanteraren.

Utdata från att köra **Kör Alla mikrosåren** i felsökningskonsolen ser ut så här:

[![Distribuera lokala mikrotjänster](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Kör webbgränssnittet

I det här steget startar du webbgränssnittet. Navigera till **azure-iot-pcs-remote-monitoring-dotnet\webui-mappen** i din lokala kopia och kör följande kommandon:

```cmd
npm install
npm start
```

När starten är klar visar webbläsaren sidan **\/http: /localhost:3000/dashboard**. Felen på den här sidan förväntas. Om du vill visa programmet utan fel slutför du följande steg.

### <a name="configure-and-run-nginx"></a>Konfigurera och köra NGINX

Konfigurera en omvänd proxyserver för att länka webbprogrammet och mikrotjänster som körs på den lokala datorn:

* Kopiera filen **nginx.conf** från **mappen webui\scripts\localhost** till installationskatalogen **nginx\conf.**
* Kör **nginx**.

Mer information om hur du kör **nginx**finns [i nginx för Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Anslut till instrumentpanelen

Om du vill komma åt instrumentpanelen för fjärrövervakningslösning navigerar du till http:\//localhost:9000 i webbläsaren.

## <a name="clean-up"></a>Rensa

För att undvika onödiga avgifter, när du är klar med din testning ta bort molntjänster från din Azure-prenumeration. Om du vill ta bort tjänsterna navigerar du till [Azure-portalen](https://ms.portal.azure.com) och tar bort resursgruppen som **start.cmd-skriptet** skapade.

Du kan också ta bort den lokala kopian av databasen för fjärrövervakning som skapades när du klonade källkoden från GitHub.

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat lösningen för fjärrövervakning är nästa steg att [utforska funktionerna på instrumentpanelen för lösningen](quickstart-remote-monitoring-deploy.md).
