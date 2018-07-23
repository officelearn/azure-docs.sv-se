---
title: Distribuera lösningen för fjärrövervakning lokalt – Azure | Microsoft Docs
description: Den här självstudien visar hur du distribuerar den lösningsacceleratorn för fjärrövervakningen till din lokala dator för utveckling och testning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/07/2018
ms.topic: conceptual
ms.openlocfilehash: 21bc8c27a44c940279b0c5bdcdbe04e579dc4bfa
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188794"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>Distribuera lösningsacceleratorn för fjärrövervakning lokalt

Den här artikeln visar hur du distribuerar lösningsacceleratorn för fjärrövervakning till din lokala dator för utveckling och testning. Den här metoden distribuerar mikrotjänsterna till en lokal Docker-behållare och använder IoT Hub, Cosmos DB och Azure storage-tjänster i molnet. Du kan använda Lösningsacceleratorer (datorer) CLI för att distribuera Azure-molntjänster.

## <a name="prerequisites"></a>Förutsättningar

För att distribuera Azure-tjänsterna används av lösningsacceleratorn för fjärrövervakning, behöver du en aktiv Azure-prenumeration.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](http://azure.microsoft.com/pricing/free-trial/).

För att slutföra lokal distribution, behöver du följande verktygen som installeras på din lokala utvecklingsdator:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker compose](https://docs.docker.com/compose/install/)
* [Node.js](https://nodejs.org/) -programvaran är en förutsättning för PCS CLI.
* DATORER CLI
* Lokal databas för källkoden

> [!NOTE]
> Dessa verktyg är tillgängliga på flera olika plattformar, inklusive Windows, Linux och iOS.

### <a name="install-the-pcs-cli"></a>Installera CLI-datorer

Om du vill installera PCS CLI via npm, kör du följande kommando i miljön kommandoraden:

```cmd/sh
npm install iot-solutions -g
```

Mer information om CLI finns i [hur du använder CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

### <a name="download-the-source-code"></a>Ladda ned källkoden

 Lagringsplatsen för fjärrövervakning källkod innehåller Docker configuration-filer som du behöver hämta, konfigurera och köra Docker-avbildningar som innehåller mikrotjänster. Klona och skapa en lokal version av databasen genom att gå till en lämplig mapp på din lokala dator via dina favorit-kommandoraden eller terminal och kör något av följande kommandon:

Om du vill installera Java-implementeringar av mikrotjänster, kör du:

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

Om du vill installera .net-implementeringar av mikrotjänster, kör du:

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

Remote Monioring förkonfigurerade lösning lagringsplatsen & undermoduler [ [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) | [.Net](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) ]

> [!NOTE]
> Dessa kommandon ladda ned källkoden för alla mikrotjänster. Även om du inte behöver källkoden för att köra mikrotjänster i Docker, är källkoden användbart om du senare planerar att ändra den förkonfigurerade lösningen och testa ändringarna lokalt.

## <a name="deploy-the-azure-services"></a>Distribuera Azure-tjänster

Även om den här artikeln visar hur du kör mikrotjänster lokalt, de förlitar sig på tre Azure-tjänster som körs i molnet. Du kan distribuera Azure-tjänsterna [manuellt via Azure portal](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup), eller använda PCS CLI. Den här artikeln visar hur du använder den `pcs` verktyget.

### <a name="sign-in-to-the-cli"></a>Logga in på CLI

Innan du kan distribuera solution accelerator, måste du logga in på Azure-prenumerationen med hjälp av CLI på följande sätt:

```cmd/sh
pcs login
```

Följ den på skärmen instruktionerna för att slutföra inloggningsprocessen. Se till att du inte klickar du på insidan CLI eller inloggningen kan misslyckas. Du ser meddelandet genomförd inloggning i CLI om du har slutfört inloggning. 

### <a name="run-a-local-deployment"></a>Kör en lokal distribution

Använd följande kommando för att starta distributionen av lokala. Detta skapar nödvändiga azure-resurser och skriva ut miljövariabler till konsolen. 

```cmd/pcs
pcs -s local
```

Skriptet uppmanas du att följande information:

* Ett lösningsnamn på.
* Den Azure-prenumeration som ska användas.
* Platsen för Azure-datacenter att använda.

> [!NOTE]
> Skriptet skapar en IoT-hubb instans, en Cosmos DB-instans och ett Azure storage-konto i en resursgrupp i Azure-prenumerationen. Namnet på resursgruppen är namnet på lösningen som du valde när du körde den `pcs` verktyget ovan. 

> [!IMPORTANT]
> Skriptet tar flera minuter att köra. När den är klar kan du se ett meddelande `Copy the following environment variables to /scripts/local/.env file:`. Kopiera ned miljön variabeln definitioner efter meddelanden du vill använda dem i ett senare steg.

## <a name="run-the-microservices-in-docker"></a>Kör mikrotjänster i Docker

Om du vill köra mikrotjänster i Docker, redigera den **skript\\lokala\\.env** fil i den lokala kopian av den lagringsplats du klonade i ett tidigare steg som ovan. Ersätt hela innehållet i filen med de variabeln miljödefinitioner som du antecknade när du körde den `pcs` kommandot i det sista steget. Dessa miljövariabler aktivera mikrotjänster i Docker-behållare för att ansluta till Azure-tjänster som skapats av den `pcs` verktyget.

Om du vill köra solution accelerator, navigera till den **scripts\local** mapp i din kommandoradsmiljö och kör sedan följande kommando:

```cmd\sh
docker-compose up
```

Första gången du kör det här kommandot hämtar Docker mikrotjänst-avbildningar från Docker hub att skapa behållare lokalt. På efterföljande körningar kör Docker behållarna omedelbart.

Du kan använda ett separat gränssnitt för att visa loggar från behållaren. Hitta först ID behållaren med den `docker ps -a` kommando. Använd sedan `docker logs {container-id} --tail 1000` att visa de senaste 1000 loggposterna för den angivna behållaren.

För att komma åt instrumentpanelen för fjärrövervakning lösningen går du till [ http://localhost:8080 ](http://localhost:8080) i webbläsaren.

## <a name="clean-up"></a>Rensa

För att undvika onödiga avgifter när du är klar med testet kan du ta bort cloud services från Azure-prenumerationen. Det enklaste sättet att ta bort tjänsterna är att navigera till den [Azure-portalen](https://ms.portal.azure.com) och ta bort resursgruppen du skapade via den `pcs` verktyget.

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