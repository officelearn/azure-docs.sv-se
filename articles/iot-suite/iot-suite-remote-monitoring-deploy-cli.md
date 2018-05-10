---
title: Distribuera fjärråtkomst övervakningslösning - Azure Java | Microsoft Docs
description: Den här kursen visar hur du etablerar fjärråtkomst övervakning solution accelerator med hjälp av CLI.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/29/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: f158af573475964eca1ff168ecf3eadc58c2a394
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Distribuera fjärråtkomst övervakning solution accelerator med hjälp av CLI

Den här kursen visar hur du etablerar fjärråtkomst övervakning solution accelerator. Du distribuerar lösningen med hjälp av CLI. Du kan också distribuera lösningen med hjälp av webbaserade Användargränssnittet på azureiotsuite.com, mer information om det här alternativet finns [Distribuera fjärråtkomst övervakning solution accelerator](iot-suite-remote-monitoring-deploy.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill distribuera fjärråtkomst övervakning solution accelerator behöver du en aktiv Azure-prenumeration.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](http://azure.microsoft.com/pricing/free-trial/).

Om du vill köra CLI, behöver du [Node.js](https://nodejs.org/) installerat på den lokala datorn.

## <a name="install-the-cli"></a>Installera CLI

Om du vill installera CLI kör du följande kommando i kommandoradsverktyget miljön:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Logga in CLI

Innan du kan distribuera solution accelerator, måste du logga in på din Azure-prenumeration med hjälp av CLI på följande sätt:

```cmd/sh
pcs login
```

Följ den på skärmen instruktionerna för att slutföra inloggningen.

## <a name="deployment-options"></a>Distributionsalternativ

När du distribuerar solution accelerator, finns det flera alternativ som konfigurerar distributionsprocessen:

| Alternativ | Värden | Beskrivning |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | En _grundläggande_ distribution är avsedd för testning och demonstrationer, alla mikrotjänster distribueras till en enskild virtuell dator. En _standard_ distribution är avsedd för produktion, har mikrotjänster distribueras till flera virtuella datorer. En _lokala_ distribution konfigurerar en dockerbehållare för att köra mikrotjänster på din lokala dator och använder Azure-tjänster, till exempel lagring och Cosmos-DB i molnet. |
| Körmiljö | `dotnet`, `java` | Väljer språk implementeringen av mikrotjänster. |

Läs om hur du använder den lokala distributionen i [kör remote övervakningslösning lokalt](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Running-the-Remote-Monitoring-Solution-Locally#deploy-azure-services-and-set-environment-variables).

## <a name="basic-vs-standard-deployments"></a>Grundläggande vs. Standard-distributioner

### <a name="basic"></a>Basic
Grundläggande distribution är inriktat mot med lösningen. För att minska kostnaden för den här demonstrationen har alla mikrotjänster distribuerats i en enskild virtuell dator; Detta anses inte klar för produktion-arkitektur.

Vår Standard distributionsalternativ som ska användas när du är redo att anpassa en produktionsklara arkitektur som skapats för utökningsbarhet och skala.

Skapa en grundläggande lösning för resulterar i följande Azure-tjänster som etableras i Azure-prenumerationen kostnad: 

| Antal | Resurs                       | Typ         | Används för |
|-------|--------------------------------|--------------|----------|
| 1     | [Linux-dator](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 V2  | Värd för mikrotjänster |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – grundläggande nivån | Enhetshantering och kommunikation |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Lagra konfigurationsdata och enhetstelemetrin som regler, larm och meddelanden |  
| 1     | [Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Lagring för virtuell dator och kontrollpunkter för strömning |
| 1     | [Webbprogram](https://azure.microsoft.com/services/app-service/web/)        |                 | Värd för frontend-webbprogram |

### <a name="standard"></a>Standard
Standarddistributionen är en produktionsklara distribution en utvecklare kan anpassa och utöka utifrån deras behov. För tillförlitlighet och skala program mikrotjänster skapas som Docker-behållare och distribueras via en orchestrator ([Kubernetes](https://kubernetes.io/) som standard). Orchestrator är ansvarig för distribution, skalning och hanteringen av programmet.

Skapa en lösning som Standard resulterar i följande Azure-tjänster som etableras i Azure-prenumerationen kostnad:

| Antal | Resurs                                     | SKU / Size      | Används för |
|-------|----------------------------------------------|-----------------|----------|
| 4     | [Virtuella Linux-datorer](https://azure.microsoft.com/services/virtual-machines/)   | Standard D2 V2  | 1 huvudserver och 3 agenter som värd för mikrotjänster med redundans |
| 1     | [Azure Container Service](https://azure.microsoft.com/services/container-service/) |                 | [Kubernetes](https://kubernetes.io) orchestrator |
| 1     | [Azure IoT-hubb] [https://azure.microsoft.com/services/iot-hub/]                     | S1 – grundläggande nivån | Hantering av enheter, kommando och kontroll |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Lagra konfigurationsdata och enhetstelemetrin som regler, larm och meddelanden |
| 5     | [Azure Storage-konton](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 för VM-lagring och 1 för strömmande kontrollpunkter |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Programgateway via SSL |

> Prisinformation för de här tjänsterna hittar [här](https://azure.microsoft.com/pricing). Förbrukningsbelopp and faktureringsinformation för prenumerationen kan hittas i den [Azure Portal](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Distribuera solution accelerator

### <a name="example-deploy-net-version"></a>Exempel: distribuera .NET-version

I följande exempel visas hur du distribuerar basic, .NET-versionen av fjärråtkomst övervakning solution accelerator:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Exempel: distribuera Java-version

I följande exempel visas hur du distribuerar standard, Java-versionen av fjärråtkomst övervakning solution accelerator:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>alternativ för datorer

När du kör den `pcs` kommando för att distribuera en lösning du blir tillfrågad om:

- Ett namn för din lösning. Det här namnet måste vara unikt.
- Den Azure-prenumeration som ska användas.
- En plats.
- Autentiseringsuppgifter för virtuella datorer som är värdar för mikrotjänster. Du kan använda dessa autentiseringsuppgifter för att få åtkomst till de virtuella datorerna för felsökning.

När den `pcs` kommandot har slutförts visas URL-Adressen till din nya solution accelerator distribution. Den `pcs` kommando skapar också en fil `{deployment-name}-output.json` med ytterligare information, till exempel namnet för IoT-hubb som etableras för dig.

Mer information om kommandoradsparametrar kör du:

```cmd/sh
pcs -h
```

Mer information om CLI finns [hur du använder CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera solution accelerator
> * Distribuera solution accelerator
> * Logga in på solution accelerator

Nu när du har distribuerat remote övervakningslösning nästa steg är att [utforska funktionerna i lösningen instrumentpanelen](./iot-suite-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->