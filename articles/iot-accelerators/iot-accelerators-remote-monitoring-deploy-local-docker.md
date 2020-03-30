---
title: Distribuera fjärrövervakningslösning lokalt - Docker - Azure | Microsoft-dokument
description: Den här programguiden visar hur du distribuerar fjärrövervakningslösningsacceleratorn till din lokala dator med Docker för testning och utveckling.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 32b47d9d6d45ff471961f55f8159dbe85eae2dce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888825"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>Distribuera snabbetorn för fjärrövervakningslösning lokalt - Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Den här artikeln visar hur du distribuerar lösningsacceleratorn för fjärrövervakning till din lokala dator för testning och utveckling. Du lär dig hur du distribuerar mikrotjänsterna till lokala Docker-behållare. En lokal mikrotjänstdistribution använder följande molntjänster: IoT Hub, Cosmos DB, Azure Streaming Analytics och Azure Time Series Insights-tjänster i molnet.

Om du vill köra lösningsacceleratorn för fjärrövervakning i en IDE på din lokala dator läser [du Distribuera lösningsacceleratorn för fjärrövervakning lokalt - Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Krav

Om du vill distribuera Azure-tjänster som används av lösningsacceleratorn för fjärrövervakning behöver du en aktiv Azure-prenumeration.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Installation av maskin

För att slutföra den lokala distributionen behöver du följande verktyg installerade på din lokala utvecklingsdator:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) - om du planerar att göra ändringar i mikrotjänster.
* [Node.js v8](https://nodejs.org/) - den här programvaran är en förutsättning för PCS CLI som skripten använder för att skapa Azure-resurser. Använd inte Node.js v10.

> [!NOTE]
> Dessa verktyg finns på många plattformar, inklusive Windows, Linux och iOS.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Kör mikrotjänsterna i Docker

Öppna en ny kommandotolk för att vara säker på att ha åtkomst till de miljövariabler som angetts av **start.cmd-skriptet.** I Windows kan du kontrollera att miljövariablerna har angetts genom att köra följande kommando:

```cmd
set PCS
```

Kommandot visar alla miljövariabler som anges av **start.cmd-skriptet.**

Kontrollera att Docker körs på din lokala dator.
> [!NOTE]
> Docker måste köra [Linux-behållare](https://docs.docker.com/docker-for-windows/) om det körs på Windows.

Mikrotjänsterna som körs i de lokala Docker-behållarna måste komma åt Azure-molntjänsterna. Du kan testa Internetanslutningen i Docker-miljön med följande kommando för att pinga en internetadress inifrån en behållare:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Om du vill köra lösningsacceleratorn navigerar du till den lokala mappen **för tjänstskript\\\\** i kommandoradsmiljön och kör följande kommando:

```cmd/sh
docker-compose up
```

> [!NOTE] 
> Se till att du delar en lokal `docker-compose up` [enhet](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115) med Docker innan du kör .

Första gången du kör det här kommandot hämtar Docker mikrotjänstavbildningarna från Docker-hubben för att skapa behållarna lokalt. Vid följande körningar kör Docker behållarna omedelbart.

> [!TIP]
> Microsoft publicerar ofta nya Docker-avbildningar med nya funktioner. Du kan använda följande uppsättning kommandon för att rensa dina lokala Docker-behållare och motsvarande bilder innan du hämtar de senaste:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

Du kan använda ett separat skal för att visa loggarna från behållaren. Först hitta behållar-ID med `docker ps` kommandot. Använd `docker logs {container-id} --tail 1000` sedan för att visa de sista 1000 posterna för den angivna behållaren.

### <a name="start-the-stream-analytics-job"></a>Starta Stream Analytics-jobbet

Så här startar du Stream Analytics-jobbet:

1. Navigera till [Azure-portalen](https://portal.azure.com).
1. Navigera till den **resursgrupp** som skapats för din lösning. Namnet på resursgruppen är det namn du valde för din lösning när du körde **start.cmd-skriptet.**
1. Klicka på **Stream Analytics-jobbet** i listan över resurser.
1. Klicka på **Start-knappen** på sidan **Webbplatsöversikt** för Stream Analytics-jobb. Klicka sedan på **Start** för att starta jobbet nu.

### <a name="connect-to-the-dashboard"></a>Anslut till instrumentpanelen

Om du vill komma åt `http://localhost:8080` instrumentpanelen för fjärrövervakningslösning navigerar du till i webbläsaren. Du kan nu använda webbgränssnittet och de lokala mikrotjänsterna.

## <a name="clean-up"></a>Rensa

För att undvika onödiga avgifter, när du är klar med din testning ta bort molntjänster från din Azure-prenumeration. Om du vill ta bort tjänsterna navigerar du till [Azure-portalen](https://ms.portal.azure.com) och tar bort resursgruppen som **start.cmd-skriptet** skapade.

Använd `docker-compose down --rmi all` kommandot för att ta bort Docker-avbildningarna och frigöra utrymme på den lokala datorn. Du kan också ta bort den lokala kopian av databasen för fjärrövervakning som skapades när du klonade källkoden från GitHub.

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat lösningen för fjärrövervakning är nästa steg att [utforska funktionerna på instrumentpanelen för lösningen](quickstart-remote-monitoring-deploy.md).
