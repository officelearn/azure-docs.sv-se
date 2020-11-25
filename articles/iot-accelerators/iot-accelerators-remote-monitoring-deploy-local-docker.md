---
title: Distribuera fjärr styrnings lösningen lokalt – Docker – Azure | Microsoft Docs
description: Den här instruktions guiden visar hur du distribuerar lösnings acceleratorn för fjärrövervakning till din lokala dator med Docker för testning och utveckling.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 32b47d9d6d45ff471961f55f8159dbe85eae2dce
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020267"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>Distribuera lösnings acceleratorn för fjärrövervakning lokalt

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Den här artikeln visar hur du distribuerar lösnings acceleratorn för fjärrövervakning till din lokala dator för testning och utveckling. Du lär dig hur du distribuerar mikrotjänster till lokala Docker-behållare. En lokal distribution av mikrotjänster använder följande moln tjänster: IoT Hub, Cosmos DB, Azure streaming Analytics och Azure Time Series Insights tjänster i molnet.

Om du vill köra lösnings acceleratorn för fjärrövervakning i en IDE på din lokala dator läser [du distribuera lösnings acceleratorn för fjärrövervakning lokalt – Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill distribuera de Azure-tjänster som används av lösningen för fjärrövervakning, behöver du en aktiv Azure-prenumeration.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Dator konfiguration

För att slutföra den lokala distributionen behöver du följande verktyg installerade på den lokala utvecklings datorn:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) – om du planerar att göra ändringar i mikrotjänsterna.
* [Node.js V8](https://nodejs.org/) – den här program varan är nödvändig för de PC CLI-datorer som skripten använder för att skapa Azure-resurser. Använd inte Node.js v10.

> [!NOTE]
> Dessa verktyg är tillgängliga på många plattformar, inklusive Windows, Linux och iOS.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Kör mikrotjänster i Docker

Öppna en ny kommando tolk för att se till att ha åtkomst till de miljövariabler som anges av skriptet **Start. cmd** . I Windows kan du kontrol lera att miljövariablerna är inställda genom att köra följande kommando:

```cmd
set PCS
```

Kommandot visar alla miljövariabler som anges av skriptet **Start. cmd** .

Se till att Docker körs på den lokala datorn.
> [!NOTE]
> Docker måste köra [Linux-behållare](https://docs.docker.com/docker-for-windows/) om den körs i Windows.

Mikrotjänster som körs i de lokala Docker-behållare måste ha åtkomst till Azure Cloud Services. Du kan testa din Docker-Miljös Internet anslutning genom att använda följande kommando för att pinga en Internet adress inifrån en behållare:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Om du vill köra Solution Accelerator navigerar du till mappen **Services- \\ skript \\ lokalt** i din kommando rads miljö och kör följande kommando:

```cmd/sh
docker-compose up
```

> [!NOTE] 
> Se till att du [delar en lokal enhet](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115) med Docker innan du kör `docker-compose up` .

Första gången du kör det här kommandot hämtar Dockaren mikroservice-avbildningarna från Docker Hub för att skapa behållare lokalt. Vid följande körningar kör Docker dock behållare omedelbart.

> [!TIP]
> Microsoft publicerar ofta nya Docker-avbildningar med nya funktioner. Du kan använda följande uppsättning kommandon för att rensa dina lokala Docker-behållare och motsvarande avbildningar innan du hämtar de senaste versionerna:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

Du kan använda ett separat gränssnitt för att visa loggarna från behållaren. Först ska du hitta container-ID med hjälp av `docker ps` kommandot. Använd sedan `docker logs {container-id} --tail 1000` för att visa de sista 1000 posterna för den angivna behållaren.

### <a name="start-the-stream-analytics-job"></a>Starta Stream Analytics jobbet

Följ de här stegen för att starta Stream Analytics jobbet:

1. Navigera till [Azure Portal](https://portal.azure.com).
1. Navigera till **resurs gruppen** som har skapats för din lösning. Namnet på resurs gruppen är det namn som du valde för lösningen när du körde skriptet **Start. cmd** .
1. Klicka på **Stream Analytics jobb** i listan över resurser.
1. På sidan Stream Analytics jobb **Översikt** klickar du på knappen **Starta** . Klicka sedan på **Starta** för att starta jobbet nu.

### <a name="connect-to-the-dashboard"></a>Ansluta till instrument panelen

Öppna instrument panelen för fjärrövervakning genom att gå till `http://localhost:8080` i webbläsaren. Nu kan du använda webb gränssnittet och de lokala mikrotjänsterna.

## <a name="clean-up"></a>Rensa

För att undvika onödiga kostnader när du har utfört testet tar du bort moln tjänsterna från din Azure-prenumeration. Om du vill ta bort tjänsterna navigerar du till [Azure Portal](https://ms.portal.azure.com) och tar bort resurs gruppen som skriptet **starta. cmd** skapade.

Använd `docker-compose down --rmi all` kommandot för att ta bort Docker-avbildningarna och frigöra utrymme på den lokala datorn. Du kan också ta bort den lokala kopian av lagrings platsen för fjärrövervakning som skapats när du klonade käll koden från GitHub.

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat lösningen för fjärrövervakning är nästa steg att [utforska funktionerna i lösningens instrument panel](quickstart-remote-monitoring-deploy.md).
