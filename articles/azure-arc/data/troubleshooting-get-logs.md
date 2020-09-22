---
title: Hämta loggar för att felsöka Azure Arc-aktiverad data styrenhet
description: Hämta tjänst loggar för att felsöka Azure Arc-aktiverad data styrenhet.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 71c84b35c001be7fafdc2df53014050ae21dec63
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90941560"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>Hämta Azure Arc-aktiverade data Services-loggar

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

För att hämta Azure Arc-aktiverade data Services-loggar behöver du Azure Data CLI-verktyget. [Installationsinstruktioner](./install-client-tools.md)

Du måste kunna logga in på tjänsten Azure Arc Enabled Data Services Controller som administratör.

## <a name="get-azure-arc-enabled-data-services-logs"></a>Hämta Azure Arc-aktiverade data Services-loggar

Du kan hämta Azure Arc-aktiverade data Services-loggar över alla poddar eller vissa poddar i fel söknings syfte.  Du kan göra detta med hjälp av standard Kubernetes-verktyg som `kubectl logs` kommandot eller i den här artikeln ska du använda Azure Data CLI-verktyget som gör det enklare att hämta alla loggar samtidigt.

Kontrol lera först att du är inloggad på data styrenheten.

```console
azdata login
```

Kör sedan följande kommando för att dumpa loggarna:
```console
azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress

#Example:
#azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
```

Loggfilerna skapas i den aktuella arbets katalogen som standard i en under katalog som kallas "loggar".  Du kan spara loggfilerna i en annan katalog med hjälp av- `--target-folder` parametern.

Du kan välja att komprimera filerna genom att utelämna `--skip-compress` parametern.

Du kan utlösa och ta med minnes dum par genom att utesluta `--exclude-dumps` , men detta rekommenderas inte om Microsoft support har begärt minnes dum par.  Om du tar en minnes dumpning krävs det att inställningen `allowDumps` för datakontrollant är inställd på `true` tiden för att skapa datakontrollanten.

Du kan välja att filtrera för att samla in loggar för bara en speciell Pod ( `--pod` ) eller container ( `--container` ) efter namn.

Du kan också välja att filtrera för att samla in loggar för en speciell resurs genom att skicka filen `--resource-kind` och `--resource-name` parameter.  `resource-kind`Parametervärdet måste vara ett av de anpassade resurs definitions namn som kan hämtas av kommandot `kubectl get customresourcedefinition` .

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>

#Example
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Exempel på mapphierarki.  Observera att mapphierarkin är ordnad efter namn på pod namn och sedan efter behållare och sedan efter katalog-hierarki i behållaren.

```console
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```