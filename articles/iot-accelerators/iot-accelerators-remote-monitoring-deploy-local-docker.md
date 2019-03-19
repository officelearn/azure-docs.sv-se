---
title: Distribuera lösningen för fjärrövervakning lokalt - Docker – Azure | Microsoft Docs
description: Den här guiden visar hur du distribuerar den lösningsacceleratorn för fjärrövervakningen till den lokala datorn med Docker för utveckling och testning.
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: c00e62e237fe263f54926c8e74fb6211a2e5a4e4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993054"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>Distribuera lösningsacceleratorn för fjärrövervakning lokalt - Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Den här artikeln visar hur du distribuerar lösningsacceleratorn för fjärrövervakning till din lokala dator för utveckling och testning. Du lär dig hur du distribuerar mikrotjänsterna till lokala Docker-behållare. En lokal mikrotjänster distribution använder följande molntjänster: IoT Hub, Cosmos DB, Azure Streaming Analytics och Azure Time Series Insights-tjänster i molnet.

Om du vill köra lösningsacceleratorn för fjärrövervakning i IDE på din lokala dator, se [och distribuera lösningsacceleratorn för fjärrövervakning lokalt – Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Förutsättningar

För att distribuera Azure-tjänsterna används av lösningsacceleratorn för fjärrövervakning, behöver du en aktiv Azure-prenumeration.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Dator-installationen

För att slutföra lokal distribution, behöver du följande verktygen som installeras på din lokala utvecklingsdator:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) – om du planerar att göra ändringar i mikrotjänster.
* [Node.js v8](https://nodejs.org/) -programvaran är en förutsättning för PCS CLI som skript som använder för att skapa Azure-resurser. Använd inte Node.js v10.

> [!NOTE]
> Dessa verktyg är tillgängliga på flera olika plattformar, inklusive Windows, Linux och iOS.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Kör mikrotjänster i Docker

Öppna en ny kommandotolk att ha åtkomst till miljövariabler som anges av den **start.cmd** skript. På Windows, kan du verifiera miljövariablerna anges genom att köra följande kommando:

```cmd
set PCS
```

Kommandot visar de miljövariabler som anges av den **start.cmd** skript.

Se till att Docker körs på den lokala datorn.
> [!NOTE]
> Docker måste köras [Linux-behållare](https://docs.docker.com/docker-for-windows/) om den körs på Windows.

Mikrotjänster som körs i lokala Docker-behållare behöver åtkomst till Azure-molntjänster. Du kan testa internet-anslutning av din Docker-miljö med följande kommando för att pinga en internet-adress i en behållare:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Om du vill köra solution accelerator, navigera till den **services\\skript\\lokala** mapp i din kommandoradsmiljö och kör sedan följande kommando:

```cmd/sh
docker-compose up
```

> [!NOTE] 
> Se till att du [dela en lokal enhet](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115) med Docker innan du kör `docker-compose up`.

Första gången du kör det här kommandot hämtar Docker mikrotjänst-avbildningar från Docker hub att skapa behållare lokalt. På följande körningar kör Docker behållarna omedelbart.

> [!TIP]
> Microsoft publicerar ofta nya Docker-avbildningar med nya funktioner. Du kan använda följande uppsättning kommandon för att rensa dina lokala Docker-behållare och motsvarande bilder innan du hämtar de senaste:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

Du kan använda ett separat gränssnitt för att visa loggar från behållaren. Hitta först ID behållaren med den `docker ps` kommando. Använd sedan `docker logs {container-id} --tail 1000` att visa de senaste 1 000 posterna för den angivna behållaren.

### <a name="start-the-stream-analytics-job"></a>Starta Stream Analytics-jobbet

Följ stegen nedan för att starta Stream Analytics-jobbet:

1. Navigera till [Azure-portalen](https://portal.azure.com).
1. Navigera till den **resursgrupp** skapats för din lösning. Namnet på resursgruppen är det namn du valde för din lösning när du körde den **start.cmd** skript.
1. Klicka på den **Stream Analytics-jobbet** i listan över resurser.
1. På Stream Analytics-jobbet **översikt** klickar du på den **starta** knappen. Klicka sedan på **starta** att starta jobbet nu.

### <a name="connect-to-the-dashboard"></a>Ansluta till instrumentpanelen

För att komma åt instrumentpanelen för fjärrövervakning lösningen går du till `http://localhost:8080` i din webbläsare. Du kan nu använda Webbgränssnittet och lokala mikrotjänster.

## <a name="clean-up"></a>Rensa

Ta bort molntjänsterna från din Azure-prenumeration för att undvika onödiga avgifter när du är klar med testet. Om du vill ta bort tjänsterna, navigera till den [Azure-portalen](https://ms.portal.azure.com) och ta bort resursen som den **start.cmd** skriptet som du skapade.

Använd den `docker-compose down --rmi all` kommando för att ta bort Docker-avbildningar och frigör utrymme på den lokala datorn. Du kan också ta bort den lokala kopian av databasen fjärrövervakning skapas när du har klonat källkoden från GitHub.

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat av lösningen för fjärrövervakning, nästa steg är att [utforska funktionerna i lösningens instrumentpanel](quickstart-remote-monitoring-deploy.md).
