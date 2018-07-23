---
title: Distribuera Java av lösningen för fjärrövervakning – Azure | Microsoft Docs
description: Den här självstudien visar hur du etablerar den lösningsacceleratorn för fjärrövervakning med hjälp av CLI.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: dd696330c9ee78ef84ac9fcf85946c837ad5b824
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188031"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Distribuera den lösningsacceleratorn för fjärrövervakning med hjälp av CLI

Den här självstudien visar hur du etablerar lösningsacceleratorn för fjärrövervakning. Du kan distribuera lösningen med hjälp av CLI. Du kan också distribuera lösningen med webbaserat gränssnitt på azureiotsuite.com, om du vill veta mer om det här alternativet finns i [distribuerar lösningsacceleratorn för fjärrövervakning](quickstart-remote-monitoring-deploy.md).

## <a name="prerequisites"></a>Förutsättningar

Du behöver en aktiv Azure-prenumeration om du vill distribuera lösningsacceleratorn för fjärrövervakning.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](http://azure.microsoft.com/pricing/free-trial/).

Om du vill köra CLI, behöver du [Node.js](https://nodejs.org/) installerad på den lokala datorn.

## <a name="install-the-cli"></a>Installera CLI

Om du vill installera CLI kör du följande kommando i miljön kommandoraden:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Logga in på CLI

Innan du kan distribuera solution accelerator, måste du logga in på Azure-prenumerationen med hjälp av CLI på följande sätt:

```cmd/sh
pcs login
```

Följ den på skärmen instruktionerna för att slutföra inloggningsprocessen.

## <a name="deployment-options"></a>Distributionsalternativ

När du distribuerar solution accelerator, finns det flera alternativ som konfigurerar distributionsprocessen:

| Alternativ | Värden | Beskrivning |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | En _grundläggande_ distribution är avsedd för testning och demonstrationer, alla mikrotjänster distribueras till en virtuell dator. En _standard_ distribution är avsedd för produktion, mikrotjänster distribueras till flera virtuella datorer. En _lokala_ distribution konfigurerar en Docker-behållare för att köra mikrotjänster på den lokala datorn och använder Azure-tjänster, till exempel lagring och Cosmos DB, i molnet. |
| Körmiljö | `dotnet`, `java` | Väljer språk implementeringen av mikrotjänster. |

Läs om hur du använder den lokala distributionen i [körs av lösningen för fjärrövervakning lokalt](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Running-the-Remote-Monitoring-Solution-Locally#deploy-azure-services-and-set-environment-variables).

## <a name="basic-vs-standard-deployments"></a>Grundläggande vs. Standard-distributioner

### <a name="basic"></a>Basic
Grundläggande distribution är inriktat för visar lösningen. För att minska kostnaden för den här demonstrationen distribueras alla mikrotjänster i en enskild virtuell dator; Detta anses inte vara en produktionsklar arkitektur.

Vår Standard distributionsalternativ bör användas när du är redo att anpassa en arkitektur med produktionsklara, byggd för skalning och utökningsbarhet.

Skapa en grundläggande lösning resulterar i följande Azure-tjänster som etableras i din Azure-prenumeration till en kostnad: 

| Antal | Resurs                       | Typ         | Används för |
|-------|--------------------------------|--------------|----------|
| 1     | [Linux-dator](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 V2  | Som är värd för mikrotjänster |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – Standard-nivån | Enhetshantering och kommunikation |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Lagra konfigurationsdata och enhetstelemetri som regler och larm meddelanden |  
| 1     | [Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Lagring för virtuell dator och strömma kontrollpunkter |
| 1     | [Webbprogram](https://azure.microsoft.com/services/app-service/web/)        |                 | Som är värd för frontend-webbprogram |

### <a name="standard"></a>Standard
Standarddistributionen är en produktionsklar distribution utvecklare kan anpassa och utöka för att uppfylla deras behov. För tillförlitlighet och skala program mikrotjänster skapas med Docker-behållare och distribueras via en initierare ([Kubernetes](https://kubernetes.io/) som standard). Orchestrator är ansvarig för distribution, skalning och hantering av programmet.

Skapa en lösning som Standard leder följande Azure-tjänster som etableras i din Azure-prenumeration till en kostnad:

| Antal | Resurs                                     | SKU / storlek      | Används för |
|-------|----------------------------------------------|-----------------|----------|
| 4     | [Virtuella Linux-datorer](https://azure.microsoft.com/services/virtual-machines/)   | Standard D2 V2  | 1 huvudserver och 3 agenter som värd för mikrotjänster med redundans |
| 1     | [Azure Container Service](https://azure.microsoft.com/services/container-service/) |                 | [Kubernetes](https://kubernetes.io) orchestrator |
| 1     | [Azure IoT Hub] [https://azure.microsoft.com/services/iot-hub/]                     | S2 – Standard-nivån | Enhetshantering, kommando och kontroll |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Lagra konfigurationsdata och enhetstelemetri som regler och larm meddelanden |
| 5     | [Azure Storage-konton](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 för VM-lagring och 1 för strömmande kontrollpunkter |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Programgateway via SSL |

> Information om priser för de här tjänsterna hittar [här](https://azure.microsoft.com/pricing). Förbrukade belopp och fakturainformationen för prenumerationen kan hittas i den [Azure-portalen](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Distribuera solution accelerator

### <a name="example-deploy-net-version"></a>Exempel: distribuera .NET-version

I följande exempel visas hur du distribuerar lösningsacceleratorn för fjärrövervakning basic-, .NET-versionen:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Exempel: distribuera Java-version

I följande exempel visas hur du distribuerar lösningsacceleratorn för fjärrövervakning standard Java-versionen:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>kommandoalternativ för datorer

När du kör den `pcs` kommando för att distribuera en lösning kan du ange:

- Ett namn för din lösning. Det här namnet måste vara unikt.
- Den Azure-prenumeration som ska användas.
- En plats.
- Autentiseringsuppgifter för virtuella datorer som är värdar för mikrotjänster. Du kan använda dessa autentiseringsuppgifter för att få åtkomst till de virtuella datorerna för felsökning.

När den `pcs` kommandot har slutförts visas URL: en för din nya lösning accelerator distribution. Den `pcs` kommandot skapar även en fil `{deployment-name}-output.json` med ytterligare information, till exempel namnet på IoT-hubben som etableras för dig.

Mer information om kommandoradsparametrar kör du:

```cmd/sh
pcs -h
```

Mer information om CLI finns i [hur du använder CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera lösningsacceleratorn
> * Distribuera solution accelerator
> * Logga in på solution accelerator

Nu när du har distribuerat av lösningen för fjärrövervakning, nästa steg är att [utforska funktionerna i lösningens instrumentpanel](./quickstart-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->
