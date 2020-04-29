---
title: Distribuera lösningen för fjärrövervakning med CLI-Azure | Microsoft Docs
description: Den här instruktions guiden visar hur du etablerar lösnings acceleratorn för fjärrövervakning med hjälp av CLI.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 501ca51a9542229a14e98a56679837950a82891e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80258302"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Distribuera lösnings acceleratorn för fjärr styrning med hjälp av CLI

Den här instruktions guiden visar hur du distribuerar lösnings acceleratorn för fjärrövervakning. Du distribuerar lösningen med hjälp av CLI. Du kan också distribuera lösningen med hjälp av det webbaserade användar gränssnittet på azureiotsolutions.com. mer information om det här alternativet finns i Distribuera snabb starten för [fjärrövervakning av Solution Accelerator](quickstart-remote-monitoring-deploy.md) .

## <a name="prerequisites"></a>Krav

Du behöver en aktiv Azure-prenumeration för att kunna distribuera lösnings acceleratorn för fjärrövervakning.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

Om du vill köra CLI måste du ha [Node. js](https://nodejs.org/) installerat på den lokala datorn.

## <a name="install-the-cli"></a>Installera CLI

Installera CLI genom att köra följande kommando i din kommando rads miljö:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Logga in på CLI

Innan du kan distribuera Solution Accelerator måste du logga in på din Azure-prenumeration med hjälp av CLI:

```cmd/sh
pcs login
```

Följ anvisningarna på skärmen för att slutföra inloggnings processen.

## <a name="deployment-options"></a>Distributionsalternativ

När du distribuerar Solution Accelerator finns det flera alternativ som konfigurerar distributions processen:

| Alternativ | Värden | Beskrivning |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | En _grundläggande_ distribution är avsedd för test och demonstrationer och distribuerar alla mikrotjänster till en enda virtuell dator. En _standard_ distribution är avsedd för produktion och distribuerar mikrotjänster till flera virtuella datorer. En _lokal_ distribution konfigurerar en Docker-behållare för att köra mikrotjänster på den lokala datorn och använder Azure Cloud Services, t. ex. lagring och Cosmos dB. |
| Körmiljö | `dotnet`, `java` | Väljer språk implementering av mikrotjänster. |

Information om hur du använder det lokala distributions alternativet finns i [köra fjärr styrnings lösningen lokalt](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="basic-and-standard-deployments"></a>Basic-och standard-distributioner

I det här avsnittet sammanfattas viktiga skillnader mellan en Basic-och standard-distribution.

### <a name="basic"></a>Basic

Du kan utföra en grundläggande distribution från [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) eller med hjälp av cli.

Grundläggande distribution är riktad mot att presentera lösningen. För att minska kostnaderna distribueras alla mikrotjänster på en enda virtuell dator. Den här distributionen använder inte en produktions klar arkitektur.

En grundläggande distribution skapar följande tjänster i din Azure-prenumeration:

| Antal | Resurs                       | Typ         | Används för |
|-------|--------------------------------|--------------|----------|
| 1     | [Virtuell Linux-dator](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 v2  | Värd för mikrotjänster |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – standard nivå | Enhets hantering och kommunikation |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Lagra konfigurations data, regler, aviseringar och annan kall lagring |  
| 1     | [Azure Storage konto](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Lagring för VM-och strömmande kontroll punkter |
| 1     | [Webb program](https://azure.microsoft.com/services/app-service/web/)        |                 | Värd för klient webb program |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Hantera användar identiteter och säkerhet |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Visa till gångs platser |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 enheter              | Aktivera analys i real tid |
| 1     | [Azure Device Provisioning-tjänst](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Etablering av enheter i stor skala |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 enhet              | Lagring för meddelande data och möjliggör djupgående telemetri-analys |

### <a name="standard"></a>Standard

Du kan bara utföra en standard distribution med hjälp av CLI.

En standard distribution är en produktions färdig distribution som en utvecklare kan anpassa och utöka. Använd standard distributions alternativet när du är redo att anpassa en produktions färdig arkitektur, byggd för skalning och utökning. Application mikrotjänster skapas som Docker-behållare och distribueras med Azure Kubernetes-tjänsten. Kubernetes Orchestrator distribuerar, skalar och hanterar mikrotjänster.

En standard distribution skapar följande tjänster i din Azure-prenumeration:

| Antal | Resurs                                     | SKU/storlek      | Används för |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Använd en fullständigt hanterad Kubernetes container Orchestration-tjänst, som standard tre agenter|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 – standard nivå | Enhets hantering, kommando och kontroll |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Lagra konfigurations data och telemetri för enheter som regler, aviseringar och meddelanden |
| 5     | [Azure Storage konton](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 för VM-lagring och 1 för de strömmande kontroll punkterna |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Application Gateway över TLS |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Hantera användar identiteter och säkerhet |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Visa till gångs platser |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 enheter              | Aktivera analys i real tid |
| 1     | [Azure Device Provisioning-tjänst](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Etablering av enheter i stor skala |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 enhet              | Lagring för meddelande data och möjliggör djupgående telemetri-analys |

> [!NOTE]
> Du hittar pris information för dessa tjänster på [https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing). Du kan hitta användnings-och fakturerings information för din prenumeration i [Azure Portal](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Distribuera lösningsacceleratorn

Distributions exempel:

### <a name="example-deploy-net-version"></a>Exempel: distribuera .NET-version

I följande exempel visas hur du distribuerar Basic .NET-versionen av lösningen för fjärr styrnings lösningar:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Exempel: distribuera Java-version

I följande exempel visas hur du distribuerar standard-Java-versionen av Solution Accelerator för fjärr övervakning:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>kommando alternativ för datorer

När du kör `pcs` kommandot för att distribuera en lösning, uppmanas du att:

- Ett namn för din lösning. Det här namnet måste vara unikt.
- Den Azure-prenumeration som ska användas.
- En plats.
- Autentiseringsuppgifter för de virtuella datorer som är värdar för mikrotjänsterna. Du kan använda dessa autentiseringsuppgifter för att få åtkomst till de virtuella datorerna för fel sökning.

När `pcs` kommandot har slutförts visas URL: en för den nya Solution Accelerator. `pcs` Kommandot skapar också en fil `{deployment-name}-output.json` som innehåller information, till exempel namnet på den IoT Hub som den skapade.

Mer information om kommando rads parametrar får du genom att köra:

```cmd/sh
pcs -h
```

Mer information om CLI finns i [så här använder du CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Nästa steg

I den här instruktions guiden har du lärt dig att:

> [!div class="checklist"]
> * Konfigurera lösningsacceleratorn
> * Distribuera lösningsacceleratorn
> * Logga in på Solution Accelerator

Nu när du har distribuerat lösningen för fjärrövervakning är nästa steg att [utforska funktionerna i lösningens instrument panel](./quickstart-remote-monitoring-deploy.md).

<!-- Next how-to guides in the sequence -->
