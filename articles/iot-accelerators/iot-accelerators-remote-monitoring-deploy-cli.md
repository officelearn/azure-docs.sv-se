---
title: Distribuera lösningen för fjärrövervakning med hjälp av CLI - Azure | Microsoft Docs
description: Den här guiden visar hur du etablerar den lösningsacceleratorn för fjärrövervakning med hjälp av CLI.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/30/2018
ms.topic: conceptual
ms.openlocfilehash: 7f1b9bb9d542b54afb6bf8b7bfc0df9fc1a9e434
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57009434"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Distribuera den lösningsacceleratorn för fjärrövervakning med hjälp av CLI

Den här guiden visar hur du distribuerar lösningsacceleratorn för fjärrövervakning. Du kan distribuera lösningen med hjälp av CLI. Du kan också distribuera lösningen med webbaserat gränssnitt på azureiotsolutions.com, om du vill veta mer om det här alternativet finns i den [distribuerar lösningsacceleratorn för fjärrövervakning](quickstart-remote-monitoring-deploy.md) Snabbstart.

## <a name="prerequisites"></a>Förutsättningar

Du behöver en aktiv Azure-prenumeration om du vill distribuera lösningsacceleratorn för fjärrövervakning.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).

Om du vill köra CLI, behöver du [Node.js](https://nodejs.org/) installerad på den lokala datorn.

## <a name="install-the-cli"></a>Installera CLI

Om du vill installera CLI kör du följande kommando i miljön kommandoraden:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Logga in på CLI

Innan du kan distribuera solution accelerator, måste du logga in på Azure-prenumerationen med hjälp av CLI:

```cmd/sh
pcs login
```

Följ den på skärmen instruktionerna för att slutföra inloggningsprocessen.

## <a name="deployment-options"></a>Distributionsalternativ

När du distribuerar solution accelerator, finns det flera alternativ som konfigurerar distributionsprocessen:

| Alternativ | Värden | Beskrivning |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | En _grundläggande_ distribution är avsedd för testning och demonstrationer, alla mikrotjänster distribueras till en virtuell dator. En _standard_ distribution är avsedd för produktion, mikrotjänster distribueras till flera virtuella datorer. En _lokala_ distribution konfigurerar en Docker-behållare för att köra mikrotjänster på den lokala datorn och använder Azure molntjänster, till exempel lagring och Cosmos DB. |
| Körmiljö | `dotnet`, `java` | Väljer språk implementeringen av mikrotjänster. |

Läs hur du använder alternativet för lokal distribution i [körs av lösningen för fjärrövervakning lokalt](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="basic-and-standard-deployments"></a>Basic och standard-distributioner

Det här avsnittet sammanfattas de största skillnaderna mellan en grundläggande och standard-distribution.

### <a name="basic"></a>Basic

Basic-distributionen är anpassad för att visa upp lösningen. För att minska kostnaderna, distribueras alla mikrotjänster i en enskild virtuell dator. Den här distributionen använder inte en produktionsklar arkitektur.

En grundläggande distribution skapar följande tjänster i Azure-prenumerationen:

| Antal | Resurs                       | Type         | Används för |
|-------|--------------------------------|--------------|----------|
| 1     | [Linux-dator](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 V2  | Som är värd för mikrotjänster |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – Standard-nivån | Enhetshantering och kommunikation |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Lagra konfigurationsdata, regler, aviseringar och andra kall lagring |  
| 1     | [Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Lagring för virtuell dator och strömma kontrollpunkter |
| 1     | [Webbprogram](https://azure.microsoft.com/services/app-service/web/)        |                 | Som är värd för frontend-webbprogram |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Hantera användaridentiteter och säkerhet |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Visa tillgången platser |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 enheter              | Att aktivera analyser i realtid |
| 1     | [Azure Device Provisioning-tjänsten](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Etablera enheter i stor skala |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 enhet              | Lagring för meddelanden data och aktiverar djupdykning telemetrianalyser |

### <a name="standard"></a>Standard

En vanlig distribution är en produktionsklar-distribution som utvecklare kan anpassa och utöka. Använd alternativet standarddistribution när du är redo att anpassa en arkitektur med produktionsklara, byggd för skalning och utökningsbarhet. Mikrotjänster för program skapas med Docker-behållare och distribueras via Azure Kubernetes Service. Distribuerar Kubernetes-orchestratorn, skalar och hanterar mikrotjänster.

En vanlig distribution skapar följande tjänster i Azure-prenumerationen:

| Antal | Resurs                                     | SKU / Size      | Används för |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Använd en fullständigt hanterad Kubernetes-behållare orchestration-tjänst, standardvärdet är 3 agenter|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 – Standard-nivån | Enhetshantering, kommando och kontroll |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Lagra konfigurationsdata och enhetstelemetri som regler, aviseringar och meddelanden |
| 5     | [Azure Storage-konton](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 för VM-lagring och 1 för strömmande kontrollpunkter |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Programgateway via SSL |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Hantera användaridentiteter och säkerhet |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Visa tillgången platser |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 enheter              | Att aktivera analyser i realtid |
| 1     | [Azure Device Provisioning-tjänsten](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Etablera enheter i stor skala |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 enhet              | Lagring för meddelanden data och aktiverar djupdykning telemetrianalyser |

> [!NOTE]
> Du hittar information om priser för dessa tjänster enligt [ https://azure.microsoft.com/pricing ](https://azure.microsoft.com/pricing). Du kan hitta användning och fakturering uppgifterna för din prenumeration i den [Azure-portalen](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Distribuera solution accelerator

Exempel på distribution:

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

När du kör den `pcs` kommando för att distribuera en lösning kan du blir tillfrågad om:

- Ett namn för din lösning. Det här namnet måste vara unikt.
- Den Azure-prenumeration som ska användas.
- En plats.
- Autentiseringsuppgifter för virtuella datorer som är värdar för mikrotjänster. Du kan använda dessa autentiseringsuppgifter för att få åtkomst till de virtuella datorerna för felsökning.

När den `pcs` kommandot har slutförts visas URL: en för din nya lösningsaccelerator. Den `pcs` kommandot skapar även en fil `{deployment-name}-output.json` som innehåller information som namnen på IoT-hubben som det skapats.

Mer information om kommandoradsparametrar kör du:

```cmd/sh
pcs -h
```

Mer information om CLI finns i [hur du använder CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Nästa steg

I den här guiden beskrivs hur du:

> [!div class="checklist"]
> * Konfigurera lösningsacceleratorn
> * Distribuera solution accelerator
> * Logga in på solution accelerator

Nu när du har distribuerat av lösningen för fjärrövervakning, nästa steg är att [utforska funktionerna i lösningens instrumentpanel](./quickstart-remote-monitoring-deploy.md).

<!-- Next how-to guides in the sequence -->
