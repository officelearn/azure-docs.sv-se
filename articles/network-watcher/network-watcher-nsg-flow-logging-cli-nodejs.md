---
title: Hantera Network Security Group flöda loggar med Nätverksbevakaren Azure - Azure CLI 1.0 | Microsoft Docs
description: Den här sidan förklarar hur du hanterar Network Security Group flöda loggar i Azure Nätverksbevakaren med Azure CLI 1.0
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 1cdef3454a326fbdbdef5ab51ac93cc8a558f34d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
ms.locfileid: "26774496"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli-10"></a>Konfigurera Network Security Group flöda loggar med Azure CLI 1.0

> [!div class="op_single_selector"]
> - [Azure-portalen](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Nätverkssäkerhetsgruppen flöde loggarna är en funktion i Nätverksbevakaren där du kan visa information om ingående och utgående IP-trafik via en Nätverkssäkerhetsgrupp. Loggarna flödet skrivs i json-format och visa utgående och inkommande flöden på grundval av per regel, NIC flödet gäller för 5-tuppel information om flödet (källan/målet IP-källan/målet Port Protocol), och om trafiken tillåts eller nekas.

Den här artikeln använder plattformsoberoende Azure CLI version 1.0, som är tillgänglig för Windows, Mac och Linux. Nätverksbevakaren använder för närvarande Azure CLI 1.0 för CLI-stöd.

## <a name="register-insights-provider"></a>Registrera providern insikter

För att flöda loggning för att fungera korrekt, den **Microsoft.Insights** provider måste vara registrerad. Om du inte är säker på om den **Microsoft.Insights** provider är registrerad, kör följande skript.

```azurecli
azure provider register --namespace Microsoft.Insights --subscription <subscriptionid>
```

## <a name="enable-network-security-group-flow-logs"></a>Aktivera Nätverkssäkerhetsgruppen flöde loggar

Kommandot för att aktivera flödet loggarna visas i följande exempel:

```azurecli
azure network watcher configure-flow-log -g resourceGroupName -n networkWatcherName -t nsgId -i storageAccountId -e true
```

## <a name="disable-network-security-group-flow-logs"></a>Inaktivera Nätverkssäkerhetsgruppen flöde loggar

Använd följande exempel för att inaktivera flödet loggar:

```azurecli
azure network watcher configure-flow-log -g resourceGroupName -n networkWatcherName -t nsgId -i storageAccountId -e false
```

## <a name="download-a-flow-log"></a>Hämta en logg flöde

Lagringsplatsen för en flödet logg definieras på Skapa. Ett enkelt verktyg för att komma åt dessa flödet loggar som sparats i ett lagringskonto är Microsoft Azure Lagringsutforskaren, som kan hämtas här: http://storageexplorer.com/

Om ett storage-konto anges sparas paket avbilda filer till ett lagringskonto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Information om strukturen i loggen finns [Network Security Group flöda logg: översikt](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [visualisera dina NSG flödet loggar med PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Lär dig hur du [visualisera dina NSG flödet loggar med öppen källkod verktyg](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
