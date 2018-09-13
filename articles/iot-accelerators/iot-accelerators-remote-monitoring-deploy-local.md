---
title: Distribuera lösningen för fjärrövervakning lokalt – Azure | Microsoft Docs
description: Den här guiden visar hur du distribuerar den lösningsacceleratorn för fjärrövervakningen till din lokala dator för utveckling och testning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/06/2018
ms.topic: conceptual
ms.openlocfilehash: aaaf31d5c1faae8176dd9909f74c70300c3f0b4e
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44716448"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>Distribuera lösningsacceleratorn för fjärrövervakning lokalt

Den här artikeln visar hur du distribuerar lösningsacceleratorn för fjärrövervakning till din lokala dator för utveckling och testning. Den här metoden distribuerar mikrotjänsterna till en lokal Docker-behållare och använder IoT Hub, Cosmos DB och Azure Time Series Insights-tjänster i molnet. Du kan använda Lösningsacceleratorer (datorer) CLI för att distribuera Azure-molntjänster.

## <a name="prerequisites"></a>Förutsättningar

För att distribuera Azure-tjänsterna används av lösningsacceleratorn för fjärrövervakning, behöver du en aktiv Azure-prenumeration.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](http://azure.microsoft.com/pricing/free-trial/).

För att slutföra lokal distribution, behöver du följande verktygen som installeras på din lokala utvecklingsdator:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker compose](https://docs.docker.com/compose/install/)
* [Node.js](https://nodejs.org/) -programvaran är en förutsättning för PCS CLI.

> [!NOTE]
> Dessa verktyg är tillgängliga på flera olika plattformar, inklusive Windows, Linux och iOS.

### <a name="download-the-source-code"></a>Ladda ned källkoden

 Fjärrövervakning GitHub lagringsplatsen för källkod innehåller Docker configuration-filer som du behöver hämta, konfigurera och köra Docker-avbildningar som innehåller mikrotjänster. Använd din kommandoradsmiljö klona och skapa en lokal version av databasen genom att navigera till en lämplig mapp på den lokala datorn och kör något av följande kommandon:

Om du vill installera Java-implementeringar av mikrotjänster, kör du:

```cmd/sh
git clone --recurse-submodules -j8 https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

Om du vill installera .net-implementeringar av mikrotjänster, kör du:

```cmd\sh
git clone --recurse-submodules -j8 https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

> [!NOTE]
> Dessa kommandon ladda ned källkoden för alla mikrotjänster. Även om du inte behöver källkoden för att köra mikrotjänster i Docker, är källkoden användbart om du senare planerar att ändra den förkonfigurerade lösningen och testa ändringarna lokalt.

## <a name="deploy-the-azure-services"></a>Distribuera Azure-tjänster

Även om den här artikeln visar hur du kör mikrotjänster lokalt, de förlitar sig på Azure-tjänster som körs i molnet. Du kan distribuera Azure-tjänsterna [manuellt via Azure portal](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup), eller använda skriptet. I följande skriptexempel förutsätter att du använder .NET-lagringsplatsen på en Windows-dator. Om du arbetar i en annan miljö, justera de sökvägar, filnamnstillägg, och avgränsarna på rätt sätt. Att använda skriptet:

1. I din kommandoradmiljö, navigerar du till den **azure-iot-pcs-remote-monitoring-dotnet\services\scripts\local\launch** mapp i din klonade kopia av databasen.

1. Kör den **start.cmd** skript och följer anvisningarna. Skriptet uppmanas du att följande information:
    * Ett lösningsnamn på.
    * Den Azure-prenumeration som ska användas.
    * Platsen för Azure-datacenter att använda.

    Skriptet skapar en resursgrupp i Azure med Lösningsnamnet på din.

1. I din kommandoradmiljö, navigerar du till den **azure-iot-pcs-remote-monitoring-dotnet\services\scripts\local\launch\os\win** mapp i din klonade kopia av databasen.

1. Kör den **set-env-uri.cmd** skript.

## <a name="run-the-microservices-in-docker"></a>Kör mikrotjänster i Docker

Om du vill köra solution accelerator, navigera till den **azure-iot-pcs-remote-monitoring-dotnet\services\scripts\local** mapp i din kommandoradsmiljö och kör sedan följande kommando:

```cmd\sh
docker-compose up
```

Första gången du kör det här kommandot hämtar Docker mikrotjänst-avbildningar från Docker hub att skapa behållare lokalt. På efterföljande körningar kör Docker behållarna omedelbart.

Du kan använda ett separat gränssnitt för att visa loggar från behållaren. Hitta först ID behållaren med den `docker ps -a` kommando. Använd sedan `docker logs {container-id} --tail 1000` att visa de senaste 1000 loggposterna för den angivna behållaren.

För att komma åt instrumentpanelen för fjärrövervakning lösningen går du till [ http://localhost:8080 ](http://localhost:8080) i webbläsaren.

## <a name="clean-up"></a>Rensa

För att undvika onödiga avgifter när du är klar med testet kan du ta bort cloud services från Azure-prenumerationen. Det enklaste sättet att ta bort tjänsterna är att navigera till den [Azure-portalen](https://ms.portal.azure.com) och ta bort resursgruppen som skapades när du körde den **start.cmd** skript.

Använd den `docker-compose down --rmi all` kommando för att ta bort Docker-avbildningar och frigör utrymme på den lokala datorn. Du kan också ta bort den lokala kopian av databasen fjärrövervakning skapas när du har klonat källkoden från GitHub.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera en lokal utvecklingsmiljö
> * Konfigurera lösningsacceleratorn
> * Distribuera solution accelerator
> * Logga in på solution accelerator

Nu när du har distribuerat av lösningen för fjärrövervakning, nästa steg är att [utforska funktionerna i lösningens instrumentpanel](quickstart-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->