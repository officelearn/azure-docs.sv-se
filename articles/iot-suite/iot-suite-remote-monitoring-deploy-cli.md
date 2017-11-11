---
title: "Distribuera fjärråtkomst övervakningslösning - Azure Java | Microsoft Docs"
description: "Den här kursen visar hur du etablerar fjärråtkomst övervakning förkonfigurerade lösningen Java microsoervices med hjälp av CLI."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 11/10/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: d0ed9a7fbb202b2008fee7f810ae0102b6f51c3d
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution-using-the-cli"></a>Distribuera fjärråtkomst övervakning förkonfigurerade lösningen som med hjälp av CLI

I den här självstudiekursen lär du dig hur du etablerar den förkonfigurerade lösningen för fjärrövervakning. Du distribuerar lösningen med hjälp av CLI. Du kan också distribuera lösningen med hjälp av webbaserade Användargränssnittet på azureiotsuite.com, mer information om det här alternativet finns [Distribuera fjärråtkomst övervakning förkonfigurerade lösningen](iot-suite-remote-monitoring-deploy.md).

## <a name="prerequisites"></a>Krav

Om du vill distribuera fjärråtkomst övervakning förkonfigurerade lösningen måste en aktiv Azure-prenumeration.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](http://azure.microsoft.com/pricing/free-trial/).

Om du vill köra CLI, behöver du [Node.js](https://nodejs.org/) installerat på den lokala datorn.

## <a name="install-the-cli"></a>Installera CLI

Om du vill installera CLI kör du följande kommando i kommandoradsverktyget miljön:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Logga in CLI

Innan du kan distribuera förkonfigurerade lösningen måste du logga in på din Azure-prenumeration med hjälp av CLI på följande sätt:

```cmd/sh
pcs login
```

Följ den på skärmen instruktionerna för att slutföra inloggningen.

## <a name="deployment-options"></a>Distributionsalternativ

När du distribuerar den förkonfigurerade lösningen, finns det flera alternativ som konfigurerar distributionsprocessen:

| Alternativ | Värden | Beskrivning |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard` | En _grundläggande_ distribution är avsedd för testning och demonstrationer, alla mikrotjänster distribueras till en enskild virtuell dator. En _standard_ distribution är avsedd för produktion, har mikrotjänster distribueras till flera virtuella datorer. |
| Runtime | `dotnet`, `java` | Väljer språk implementeringen av mikrotjänster. |

## <a name="deploy-the-preconfigured-solution"></a>Distribuera förkonfigurerade lösningen

### <a name="example-deploy-net-version"></a>Exempel: distribuera .NET-version

I följande exempel visas hur du distribuerar basic, .NET-versionen av fjärråtkomst övervakning förkonfigurerade lösningen:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Exempel: distribuera Java-version

I följande exempel visas hur du distribuerar, Java standardversionen av fjärråtkomst övervakning förkonfigurerade lösningen:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>alternativ för datorer

När du kör den `pcs` kommando för att distribuera en lösning du blir tillfrågad om:

- Ett namn för din lösning. Det här namnet måste vara unikt.
- Den Azure-prenumeration som ska användas.
- En plats.
- Autentiseringsuppgifter för virtuella datorer som är värdar för mikrotjänster. Du kan använda dessa autentiseringsuppgifter för att få åtkomst till de virtuella datorerna för felsökning.

När den `pcs` kommandot har slutförts visas URL-Adressen för distributionen förkonfigurerade lösningen. Den `pcs` kommando skapar också en fil `{deployment-name}-output.json` med ytterligare information, till exempel namnet för IoT-hubb som etableras för dig.

Mer information om kommandoradsparametrar kör du:

```cmd/sh
pcs -h
```

Mer information om CLI finns [hur du använder CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera förkonfigurerade lösningen
> * Distribuera förkonfigurerade lösningen
> * Logga in på den förkonfigurerade lösningen

Nu när du har distribuerat remote övervakningslösning nästa steg är att [utforska funktionerna i lösningen instrumentpanelen](./iot-suite-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->