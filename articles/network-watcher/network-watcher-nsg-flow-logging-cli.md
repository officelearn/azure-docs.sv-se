---
title: Hantera Network Security Group Flow loggar med Azure Network Watcher – Azure CLI | Microsoft Docs
description: Den här sidan förklarar hur du hanterar Network Security Group Flow loggar i Azure Network Watcher med Azure CLI
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
ms.openlocfilehash: 43e18326aa9afe41ddfd1183bf689972c1877b0d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57904992"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Konfigurera Network Security Group Flow loggar med Azure CLI

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST-API](network-watcher-nsg-flow-logging-rest.md)

Flödesloggar för Nätverkssäkerhetsgruppen är en funktion i Network Watcher där du kan visa information om ingående och utgående IP-trafik via en Nätverkssäkerhetsgrupp. Dessa flödesloggar skrivs i json-format och visa utgående och inkommande flöden på basis av per regel, nätverkskortet flödet gäller för 5-tuppel information om flödet (källa/mål-IP, källa/mål-Port, Protocol), och om trafik tillåts eller nekas.

Om du vill utföra stegen i den här artikeln, måste du [installera Azures kommandoradsgränssnitt för Mac, Linux och Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="register-insights-provider"></a>Registrera Insights-providern

För flow loggning för att fungera korrekt, den **Microsoft.Insights** providern måste vara registrerad. Om du inte är säker på att om den **Microsoft.Insights** providern är registrerad, kör följande skript.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Aktivera Nätverkssäkerhetsgrupp flödesloggar

Kommando för att aktivera flödesloggar visas i följande exempel:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
# Configure 
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName  --format JSON --log-version 2
```

Det lagringskonto som du anger inte får ha konfigurerat för den Nätverksregler som begränsar nätverksåtkomst till endast Microsoft-tjänster eller specifika virtuella nätverk. Lagringskontot kan vara i samma eller en annan Azure-prenumeration, än NSG: N som du aktiverar det flöde du vill ha. Om du använder olika prenumerationer kan vara de båda kopplade till samma Azure Active Directory-klient. Det konto som används för varje prenumeration måste ha den [behörighet](required-rbac-permissions.md). 

Om lagringskontot tillhör en annan resursgrupp eller prenumeration, än nätverkssäkerhetsgruppen, ange fullständig ID för storage-konto i stället för dess namn. Exempel: om lagringskontot är i en resursgrupp med namnet *RG-Storage*, i stället för att ange *storageAccountName* i föregående kommando, anger du   */subscriptions / { SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName*.

## <a name="disable-network-security-group-flow-logs"></a>Inaktivera Nätverkssäkerhetsgrupp flödesloggar

Använd följande exempel för att inaktivera flödesloggar:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Ladda ned en Flow-log

Lagringsplatsen för en flow-log definieras när du skapar. Ett praktiskt verktyg för att komma åt dessa flödesloggar som sparats i ett lagringskonto är Microsoft Azure Storage Explorer, som kan hämtas här:  https://storageexplorer.com/

Om ett lagringskonto anges sparas flow loggfiler till ett lagringskonto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Information om strukturen i loggen på [Network Security Group Flow log översikt](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [visualisera din NSG-flödesloggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Lär dig hur du [visualisera din NSG-flödesloggar med verktyg för öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
