---
title: "Distribuera fjärråtkomst övervakning lösningen lokalt - Azure | Microsoft Docs"
description: "Den här kursen visar hur du distribuerar fjärråtkomst övervakning förkonfigurerade lösningen till din lokala dator för testning och utveckling."
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 03/07/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 77f40e2f10cbdb9930a22a4248e19bb3356af7af
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution-locally"></a>Distribuera fjärråtkomst övervakning förkonfigurerade lösningen lokalt

Den här artikeln visar hur du distribuerar fjärråtkomst övervakning förkonfigurerade lösningen till din lokala dator för testning och utveckling. Den här metoden distribuerar mikrotjänster till en lokal dockerbehållare och använder IoT-hubb Cosmos DB och Azure storage-tjänster i molnet. Du kan använda förkonfigurerade lösningar (datorer) CLI för att distribuera Azure-molntjänster.

## <a name="prerequisites"></a>Förutsättningar

Om du vill distribuera Azure-tjänster används av den fjärranslutna förkonfigurerade övervakningslösning måste en aktiv Azure-prenumeration.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](http://azure.microsoft.com/pricing/free-trial/).

För att slutföra den lokala distributionen behöver du följande verktyg installerat på utvecklingsdatorn lokala:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker compose](https://docs.docker.com/compose/install/)
* [Node.js](https://nodejs.org/) -programvaran är en förutsättning för PCS CLI.
* PCS CLI

> [!NOTE]
> Dessa verktyg är tillgängliga på flera olika plattformar, inklusive Windows, Linux och iOS.

### <a name="install-the-pcs-cli"></a>Installera datorer CLI

Om du vill installera CLI kör du följande kommando i kommandoradsverktyget miljön:

```cmd/sh
npm install iot-solutions -g
```

Mer information om CLI finns [hur du använder CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="deploy-the-azure-services"></a>Distribuera Azure-tjänster

Även om den här artikeln visar hur du kör mikrotjänster lokalt, beror på tre Azure-tjänster som körs i molnet. Du kan distribuera dessa Azure-tjänster manuellt via Azure-portalen eller använda PCS CLI. Den här artikeln visar hur du använder den `pcs` verktyget.

### <a name="sign-in-to-the-cli"></a>Logga in CLI

Innan du kan distribuera förkonfigurerade lösningen måste du logga in på din Azure-prenumeration med hjälp av CLI på följande sätt:

```cmd/sh
pcs login
```

Följ den på skärmen instruktionerna för att slutföra inloggningen.

### <a name="run-a-local-deployment"></a>Kör en lokal distribution

Använd följande kommando för att starta lokala distributionen:

```cmd/pcs
pcs -s local
```

Ombeds du följande information:

* Lösningens namn.
* Den Azure-prenumeration som ska användas.
* Platsen för Azure-datacenter ska användas.

Skriptet skapar en IoT-hubb instans, en Cosmos-DB-instans och Azure storage-konto i en resursgrupp i Azure-prenumeration. Namnet på resursgruppen är namnet på den lösning som du valde när du körde den `pcs` verktyget.

Skriptet tar flera minuter att köra. När den är klar, visas ett meddelande `Copy the following environment variables to /scripts/local/.env file:`. Kopiera miljö variabeln definitionerna efter meddelandet kan du använda dem i ett senare steg.

## <a name="download-the-source-code"></a>Ladda ned källkoden

Fjärråtkomst övervakning kod källdatabasen innehåller Docker-konfigurationsfiler som du behöver hämta, konfigurera och köra Docker-avbildningar som innehåller mikrotjänster. Klona lagringsplatsen, navigera till en lämplig mapp på den lokala datorn och kör något av följande kommandon:

Om du vill installera Java-implementeringar av mikrotjänster, kör du:

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

För att installera .net-implementeringar av mikrotjänster, kör du:

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

Dessa kommandon Hämta källkoden för alla mikrotjänster. Även om du inte behöver källkod för att köra mikrotjänster i Docker är källkoden användbart om du senare planerar att ändra förkonfigurerade lösningen och testa dina ändringar lokalt.

## <a name="run-the-microservices-in-docker"></a>Kör mikrotjänster i Docker

Om du vill köra mikrotjänster i Docker först redigera den **skript\\lokala\\.env** fil i den lokala kopian av databasen. Ersätt hela innehållet i filen med miljö variabeln definitioner du antecknade när du körde den `pcs` kommandot tidigare. De här miljövariablerna aktivera mikrotjänster i dockerbehållare att ansluta till Azure-tjänster skapas av den `pcs` verktyget.

Om du vill köra den förkonfigurerade lösningen, navigera till den **scripts\local** mapp i din kommandoradsmiljö och kör sedan följande kommando:

```cmd\sh
docker-compose up
```

Första gången du kör det här kommandot hämtar Docker mikrotjänster bilder från Docker-hubb för att skapa behållare lokalt. Efterföljande körningar körs Docker behållarna omedelbart.

Du kan använda ett separat gränssnitt för att visa loggar från behållaren. Hitta först ID behållaren med den `docker ps -a` kommando. Använd sedan `docker logs {container-id} --tail 1000` att visa de senaste 1000 loggposter för den angivna behållaren.

Om du vill komma åt instrumentpanelen för fjärråtkomst övervakning lösning, gå till [http://localhost: 8080](http://localhost:8080) i webbläsaren.

## <a name="tidy-up"></a>Tömma

För att undvika onödiga kostnader, när du har slutfört din testning, tar du bort molntjänster från din Azure-prenumeration. Det enklaste sättet att ta bort tjänsterna är att använda Azure portal för att ta bort resursgruppen som skapats av den `pcs` verktyget.

Använd den `docker-compose down --rmi all` kommando för att ta bort Docker-bilder och frigör utrymme på den lokala datorn. Du kan också ta bort den lokala kopian av övervakning fjärrdatabasen skapas när du har klonat källkod från GitHub.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera förkonfigurerade lösningen
> * Distribuera förkonfigurerade lösningen
> * Logga in på den förkonfigurerade lösningen

Nu när du har distribuerat remote övervakningslösning nästa steg är att [utforska funktionerna i lösningen instrumentpanelen](./iot-suite-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->