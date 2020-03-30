---
title: Distribuera fjärrövervakningslösningen med CLI - Azure | Microsoft-dokument
description: Den här programguiden visar hur du etablerar lösningsacceleratorn för fjärrövervakning med CLI.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 501ca51a9542229a14e98a56679837950a82891e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258302"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Distribuera lösningsacceleratorn för fjärrövervakning med CLI

Den här programguiden visar hur du distribuerar lösningsacceleratorn för fjärrövervakning. Du distribuerar lösningen med CLI. Du kan också distribuera lösningen med hjälp av det webbaserade användargränssnittet på azureiotsolutions.com, om du vill veta mer om det här alternativet, se [snabbstarten för lösningen för fjärrövervakning.](quickstart-remote-monitoring-deploy.md)

## <a name="prerequisites"></a>Krav

För att distribuera lösningsacceleratorn för fjärrövervakning behöver du en aktiv Azure-prenumeration.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

För att köra CLI behöver du [Node.js](https://nodejs.org/) installerat på din lokala dator.

## <a name="install-the-cli"></a>Installera CLI

Om du vill installera CLI kör du följande kommando i kommandoradsmiljön:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Logga in på CLI

Innan du kan distribuera lösningsacceleratorn måste du logga in på din Azure-prenumeration med CLI:

```cmd/sh
pcs login
```

Följ anvisningarna på skärmen för att slutföra inloggningsprocessen.

## <a name="deployment-options"></a>Distributionsalternativ

När du distribuerar lösningsacceleratorn finns det flera alternativ som konfigurerar distributionsprocessen:

| Alternativ | Värden | Beskrivning |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | En _grundläggande_ distribution är avsedd för test och demonstrationer, distribuerar den alla mikrotjänster till en enda virtuell dator. En _standarddistribution_ är avsedd för produktion, distribuerar mikrotjänster till flera virtuella datorer. En _lokal_ distribution konfigurerar en Docker-behållare för att köra mikrotjänsterna på din lokala dator och använder Azure-molntjänster, till exempel lagring och Cosmos DB. |
| Körmiljö | `dotnet`, `java` | Väljer språkimplementeringen av mikrotjänsterna. |

Mer information om hur du använder alternativet lokal distribution finns i [Köra fjärrövervakningslösningen lokalt](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="basic-and-standard-deployments"></a>Grundläggande distributioner och standarddistributioner

Det här avsnittet sammanfattar de viktigaste skillnaderna mellan en grundläggande distribution och standarddistribution.

### <a name="basic"></a>Basic

Du kan göra en grundläggande distribution från [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) eller använda CLI.

Grundläggande distribution är inriktad på att visa upp lösningen. För att minska kostnaderna distribueras alla mikrotjänster på en enda virtuell dator. Den här distributionen använder inte en produktionsklar arkitektur.

En grundläggande distribution skapar följande tjänster i din Azure-prenumeration:

| Antal | Resurs                       | Typ         | Används för |
|-------|--------------------------------|--------------|----------|
| 1     | [Virtuell Linux-dator](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 V2  | Hosting mikrotjänster |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – Standardnivå | Enhetshantering och kommunikation |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Lagra konfigurationsdata, regler, varningar och annan kyllagring |  
| 1     | [Azure-lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Lagring för virtuella datorer och strömningskontroller |
| 1     | [Webbprogram](https://azure.microsoft.com/services/app-service/web/)        |                 | Hosting front-end webbapplikation |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Hantera användaridentiteter och säkerhet |
| 1     | [Azure-kartor](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Visa tillgångsplatser |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 enheter              | Aktivera analys i realtid |
| 1     | [Tjänst för etablerande av Azure-enhet](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Etablera enheter i stor skala |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 enhet              | Lagring för meddelandedata och möjliggör djupdyre telemetrianalys |

### <a name="standard"></a>Standard

Du kan bara göra en standarddistribution med CLI.

En standarddistribution är en produktionsklar distribution som en utvecklare kan anpassa och utöka. Använd standarddistributionsalternativet när du är redo att anpassa en produktionsklar arkitektur, byggd för skalning och utökningsbarhet. Programmikrotjänster skapas som Docker-behållare och distribueras med Azure Kubernetes-tjänsten. Kubernetes orchestrator distribuerar, skalar och hanterar mikrotjänsterna.

En standarddistribution skapar följande tjänster i din Azure-prenumeration:

| Antal | Resurs                                     | SKU / Storlek      | Används för |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Använd en fullständigt hanterad Kubernetes-behållarorkestreringstjänst, som standard till 3 agenter|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 – Standardnivå | Enhetshantering, kommando och kontroll |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Lagra konfigurationsdata och enhetstelemetri som regler, aviseringar och meddelanden |
| 5     | [Azure Storage-konton](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 för VM-lagring och 1 för kontrollpunkter för direktuppspelning |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Programgateway över TLS |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Hantera användaridentiteter och säkerhet |
| 1     | [Azure-kartor](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Visa tillgångsplatser |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 enheter              | Aktivera analys i realtid |
| 1     | [Tjänst för etablerande av Azure-enhet](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Etablera enheter i stor skala |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 enhet              | Lagring för meddelandedata och möjliggör djupdyre telemetrianalys |

> [!NOTE]
> Du hittar prisinformation för [https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing)dessa tjänster på . Du hittar användnings- och faktureringsinformation för din prenumeration i [Azure Portal](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Distribuera lösningsacceleratorn

Exempel på distribution:

### <a name="example-deploy-net-version"></a>Exempel: distribuera .NET-versionen

I följande exempel visas hur du distribuerar den grundläggande .NET-versionen av lösningsacceleratorn för fjärrövervakning:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Exempel: distribuera Java-version

I följande exempel visas hur du distribuerar standardversionen av lösningsacceleratorn för fjärrövervakning:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>kommandoalternativ för datorer

När du `pcs` kör kommandot för att distribuera en lösning tillfrågas du om:

- Ett namn på din lösning. Det här namnet måste vara unikt.
- Den Azure-prenumeration som ska användas.
- En plats.
- Autentiseringsuppgifter för de virtuella datorer som är värdar för mikrotjänsterna. Du kan använda dessa autentiseringsuppgifter för att komma åt de virtuella datorerna för felsökning.

När `pcs` kommandot är klart visas url:en för den nya lösningsacceleratorn. Kommandot `pcs` skapar också en `{deployment-name}-output.json` fil som innehåller information som namnet på IoT Hub som den skapade.

Om du vill ha mer information om kommandoradsparametrarna kör du:

```cmd/sh
pcs -h
```

Mer information om CLI finns i [Så här använder du CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Nästa steg

I den här guiden lärde du dig att:

> [!div class="checklist"]
> * Konfigurera lösningsacceleratorn
> * Distribuera lösningsacceleratorn
> * Logga in på lösningsacceleratorn

Nu när du har distribuerat lösningen för fjärrövervakning är nästa steg att [utforska funktionerna på instrumentpanelen för lösningen](./quickstart-remote-monitoring-deploy.md).

<!-- Next how-to guides in the sequence -->
