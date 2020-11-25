---
title: Distribuera fjärr styrnings lösningen lokalt – Visual Studio Code – Azure | Microsoft Docs
description: Den här instruktions guiden visar hur du distribuerar lösnings acceleratorn för fjärrövervakning till din lokala dator med Visual Studio Code för testning och utveckling.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d20e9a6a78d99a23fe4b98aeb4f3eb8359da7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024188"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Distribuera lösnings acceleratorn för fjärrövervakning lokalt – Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Den här artikeln visar hur du distribuerar lösnings acceleratorn för fjärrövervakning till din lokala dator för testning och utveckling. Du lär dig hur du kör mikrotjänster i Visual Studio Code. En lokal distribution av mikrotjänster använder följande moln tjänster: IoT Hub, Cosmos DB, Azure streaming Analytics och Azure Time Series Insights.

## <a name="prerequisites"></a>Förutsättningar

Om du vill distribuera de Azure-tjänster som används av lösningen för fjärrövervakning, behöver du en aktiv Azure-prenumeration.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Dator konfiguration

För att slutföra den lokala distributionen behöver du följande verktyg installerade på den lokala utvecklings datorn:

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [VS Codes C#-tillägg](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js V8](https://nodejs.org/) – den här program varan är nödvändig för de PC CLI-datorer som skripten använder för att skapa Azure-resurser. Använd inte Node.js v10

> [!NOTE]
> Visual Studio Code är tillgängligt för Windows, Mac och Ubuntu.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Kör mikrotjänster

I det här avsnittet ska du köra mikrotjänster för fjärr övervakning. Du kör webb gränssnittet internt, enhets simulerings tjänsten i Docker och mikrotjänsterna i Visual Studio Code.

### <a name="build-the-code"></a>Skapa koden

Navigera till Azure-IoT-PCs-Remote-Monitoring-dotnet\services i kommando tolken och kör följande kommandon för att skapa koden.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Distribuera alla andra mikrotjänster på den lokala datorn

Följande steg visar hur du kör mikrotjänster för fjärr styrning i Visual Studio Code:

1. Starta Visual Studio Code.
1. I VS Code, öppnar du mappen **Azure-IoT-PC-Remote-Monitoring – dotNet** .
1. Skapa en ny mapp med namnet **. VSCode** i mappen **Azure-IoT-PCs-Remote-Monitoring-dotNet** .
1. Kopiera filerna **launch.js** och **tasks.js** från services\scripts\local\launch\idesettings\vscode till mappen **. VSCode** som du nyss skapade.
1. Öppna **fel söknings panelen** i vs Code och kör konfigurationen **Kör alla mikrotjänster** . Den här konfigurationen kör mikrotjänsten enhets simulering i Docker och kör övriga mikrotjänster i fel söknings programmet.

Utdata från att köra **alla microsoervices** i fel söknings konsolen ser ut så här:

[![Distribuera lokala mikrotjänster](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Kör webb gränssnittet

I det här steget startar du webb gränssnittet. Gå till mappen **Azure-IoT-PCs-Remote-Monitoring-dotnet\webui** i din lokala kopia och kör följande kommandon:

```cmd
npm install
npm start
```

När starten är klar visar webbläsaren sidan **http: \/ /localhost: 3000/Dashboard**. Felen på den här sidan förväntas. Om du vill visa programmet utan fel slutför du följande steg.

### <a name="configure-and-run-nginx"></a>Konfigurera och kör NGINX

Konfigurera en omvänd proxyserver för att länka webb programmet och mikrotjänster som körs på den lokala datorn:

* Kopiera filen **nginx. conf** från mappen **webui\scripts\localhost** till installations katalogen för **nginx\conf** .
* Kör **nginx**.

Mer information om hur du kör **nginx** finns i [nginx för Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Ansluta till instrument panelen

Öppna instrument panelen för fjärrövervakning genom att gå till http: \/ /localhost: 9000 i webbläsaren.

## <a name="clean-up"></a>Rensa

För att undvika onödiga kostnader när du har utfört testet tar du bort moln tjänsterna från din Azure-prenumeration. Om du vill ta bort tjänsterna navigerar du till [Azure Portal](https://ms.portal.azure.com) och tar bort resurs gruppen som skriptet **starta. cmd** skapade.

Du kan också ta bort den lokala kopian av lagrings platsen för fjärrövervakning som skapats när du klonade käll koden från GitHub.

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat lösningen för fjärrövervakning är nästa steg att [utforska funktionerna i lösningens instrument panel](quickstart-remote-monitoring-deploy.md).
