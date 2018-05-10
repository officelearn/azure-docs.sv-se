---
title: Distribuera fjärråtkomst övervakning lösningen lokalt - Azure | Microsoft Docs
description: Den här kursen visar hur du distribuerar fjärråtkomst övervakning solution accelerator till din lokala dator för testning och utveckling.
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
ms.openlocfilehash: a470987c4e8b5755554e4827cf1295a174d301e8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>Distribuera fjärråtkomst övervakning solution accelerator lokalt

Den här artikeln visar hur du distribuerar fjärråtkomst övervakning solution accelerator till din lokala dator för testning och utveckling. Den här metoden distribuerar mikrotjänster till en lokal dockerbehållare och använder IoT-hubb Cosmos DB och Azure storage-tjänster i molnet. Du kan använda solution Accelerator (datorer) CLI för att distribuera Azure-molntjänster.

## <a name="prerequisites"></a>Förutsättningar

För att distribuera Azure-tjänster används av fjärråtkomst övervakning solution accelerator, behöver du en aktiv Azure-prenumeration.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](http://azure.microsoft.com/pricing/free-trial/).

För att slutföra den lokala distributionen behöver du följande verktyg installerat på utvecklingsdatorn lokala:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker compose](https://docs.docker.com/compose/install/)
* [Node.js](https://nodejs.org/) -programvaran är en förutsättning för PCS CLI.
* DATORER CLI
* Lokal databas för källkoden

> [!NOTE]
> Dessa verktyg är tillgängliga på flera olika plattformar, inklusive Windows, Linux och iOS.

### <a name="install-the-pcs-cli"></a>Installera datorer CLI

För att installera PCS CLI via npm, kör du följande kommando i Kommandotolken miljön:

```cmd/sh
npm install iot-solutions -g
```

Mer information om CLI finns [hur du använder CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

### <a name="download-the-source-code"></a>Ladda ned källkoden

 Fjärråtkomst övervakning kod källdatabasen innehåller Docker-konfigurationsfiler som du behöver hämta, konfigurera och köra Docker-avbildningar som innehåller mikrotjänster. Gå till en lämplig mapp på din lokala dator via din favorit kommandoraden eller terminal för att klona och skapa en lokal version av databasen, och kör något av följande kommandon:

Om du vill installera Java-implementeringar av mikrotjänster, kör du:

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

För att installera .net-implementeringar av mikrotjänster, kör du:

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

Remote Monioring förkonfigurerade lösningen lagringsplatsen & submodules [ [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) | [.Net](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) ]

> [!NOTE]
> Dessa kommandon Hämta källkoden för alla mikrotjänster. Även om du inte behöver källkod för att köra mikrotjänster i Docker är källkoden användbart om du senare planerar att ändra förkonfigurerade lösningen och testa dina ändringar lokalt.

## <a name="deploy-the-azure-services"></a>Distribuera Azure-tjänster

Även om den här artikeln visar hur du kör mikrotjänster lokalt, beror på tre Azure-tjänster som körs i molnet. Du kan distribuera dessa Azure-tjänster [manuellt via Azure portal](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup), eller Använd PCS CLI. Den här artikeln visar hur du använder den `pcs` verktyget.

### <a name="sign-in-to-the-cli"></a>Logga in CLI

Innan du kan distribuera solution accelerator, måste du logga in på din Azure-prenumeration med hjälp av CLI på följande sätt:

```cmd/sh
pcs login
```

Följ den på skärmen instruktionerna för att slutföra inloggningen. Kontrollera att du inte klickar någonstans i insidan CLI eller inloggningen kan misslyckas. Visas ett meddelande som genomförd inloggning i CLI om du har slutfört inloggningen. 

### <a name="run-a-local-deployment"></a>Kör en lokal distribution

Använd följande kommando för att starta lokala distributionen. Detta skapar nödvändiga azure-resurser och skriva ut environemnt variabler i konsolen. 

```cmd/pcs
pcs -s local
```

Ombeds du följande information:

* Lösningens namn.
* Den Azure-prenumeration som ska användas.
* Platsen för Azure-datacenter ska användas.

> [!NOTE]
> Skriptet skapar en IoT-hubb instans, en Cosmos-DB-instans och Azure storage-konto i en resursgrupp i Azure-prenumeration. Namnet på resursgruppen är namnet på den lösning som du valde när du körde den `pcs` verktyget ovan. 

> [!IMPORTANT]
> Skriptet tar flera minuter att köra. När den är klar, visas ett meddelande `Copy the following environment variables to /scripts/local/.env file:`. Kopiera ned miljön variabeln definitioner efter meddelandet du ska använda dem i ett senare steg.

## <a name="run-the-microservices-in-docker"></a>Kör mikrotjänster i Docker

Om du vill köra mikrotjänster i Docker först redigera den **skript\\lokala\\.env** fil i den lokala kopian av databasen som du har klonat i ett av de föregående stegen ovan. Ersätt hela innehållet i filen med miljö variabeln definitioner du antecknade när du körde den `pcs` i det sista steget. De här miljövariablerna aktivera mikrotjänster i dockerbehållare att ansluta till Azure-tjänster skapas av den `pcs` verktyget.

Om du vill köra solution accelerator, navigera till den **scripts\local** mapp i din kommandoradsmiljö och kör sedan följande kommando:

```cmd\sh
docker-compose up
```

Första gången du kör det här kommandot hämtar Docker mikrotjänster bilder från Docker-hubb för att skapa behållare lokalt. Efterföljande körningar körs Docker behållarna omedelbart.

Du kan använda ett separat gränssnitt för att visa loggar från behållaren. Hitta först ID behållaren med den `docker ps -a` kommando. Använd sedan `docker logs {container-id} --tail 1000` att visa de senaste 1000 loggposter för den angivna behållaren.

Om du vill komma åt instrumentpanelen för fjärråtkomst övervakning lösning, gå till [ http://localhost:8080 ](http://localhost:8080) i webbläsaren.

## <a name="clean-up"></a>Rensa

För att undvika onödiga kostnader, när du har slutfört din testning, tar du bort molntjänster från din Azure-prenumeration. Det enklaste sättet att ta bort tjänsterna är att navigera till den [Azure-portalen](https://ms.portal.azure.com) och ta bort resursgruppen som du har skapat via den `pcs` verktyget.

Använd den `docker-compose down --rmi all` kommando för att ta bort Docker-bilder och frigör utrymme på den lokala datorn. Du kan också ta bort den lokala kopian av övervakning fjärrdatabasen skapas när du har klonat källkod från GitHub.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera en lokal utvecklingsmiljö
> * Konfigurera solution accelerator
> * Distribuera solution accelerator
> * Logga in på solution accelerator

Nu när du har distribuerat remote övervakningslösning nästa steg är att [utforska funktionerna i lösningen instrumentpanelen](./iot-suite-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->