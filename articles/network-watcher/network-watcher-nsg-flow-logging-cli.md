---
title: Hantera NSG-flödesloggar - Azure CLI
titleSuffix: Azure Network Watcher
description: På den här sidan beskrivs hur du hanterar nätverkssäkerhetsgruppflödesloggar i Azure Network Watcher med Azure CLI
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 285d19dbd0e7b8a94eada66f837d33b787006f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840969"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Konfigurera flödesloggar för nätverkssäkerhetsgrupper med Azure CLI

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-nsg-flow-logging-portal.md)
> - [Powershell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Nätverkssäkerhetsgruppsflödesloggar är en funktion i Network Watcher som gör att du kan visa information om inkommande och utgående IP-trafik via en nätverkssäkerhetsgrupp. Dessa flödesloggar är skrivna i json-format och visar utgående och inkommande flöden per regel, nätverkskortet som flödet gäller för, 5-tuppelinformation om flödet (Source/Destination IP, Source/Destination Port, Protocol) och om trafiken tilläts eller nekades.

Om du vill utföra stegen i den här artikeln måste du [installera Azure-kommandoradsgränssnittet för Mac, Linux och Windows (CLI).](/cli/azure/install-azure-cli)

## <a name="register-insights-provider"></a>Registrera Insights-providern

För att flödesloggningen ska fungera måste **Microsoft.Insights-providern** vara registrerad. Om du är osäker på om **Microsoft.Insights-providern** är registrerad kör du följande skript.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Aktivera flödesloggar för nätverkssäkerhetsgruppen

Kommandot för att aktivera flödesloggar visas i följande exempel:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
# Configure 
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName  --format JSON --log-version 2
```

Det lagringskonto som du anger kan inte ha nätverksregler konfigurerade för det som begränsar nätverksåtkomsten till endast Microsoft-tjänster eller specifika virtuella nätverk. Lagringskontot kan finnas i samma, eller en annan Azure-prenumeration, än NSG som du aktiverar flödesloggen för. Om du använder olika prenumerationer måste de båda vara associerade till samma Azure Active Directory-klientorganisation. Kontot som du använder för varje prenumeration måste ha [de behörigheter som krävs](required-rbac-permissions.md). 

Om lagringskontot finns i en annan resursgrupp, eller prenumeration, än nätverkssäkerhetsgruppen, anger du det fullständiga ID:t för lagringskontot i stället för dess namn. Om lagringskontot till exempel finns i en resursgrupp med namnet *RG-Storage*i stället för att ange *storageAccountName* i föregående kommando, anger du */subscriptions/{SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.StorageAccounts/storageAccountName*.

## <a name="disable-network-security-group-flow-logs"></a>Inaktivera flödesloggar för nätverkssäkerhetsgruppen

Använd följande exempel för att inaktivera flödesloggar:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Ladda ner en Flödeslogg

Lagringsplatsen för en flödeslogg definieras när den skapas. Ett praktiskt verktyg för att komma åt dessa flödesloggar som sparats på ett lagringskonto är Microsoft Azure Storage Explorer, som kan hämtas här:https://storageexplorer.com/

Om ett lagringskonto anges sparas flödesloggfiler i ett lagringskonto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```


## <a name="next-steps"></a>Efterföljande moment

Lär dig hur du [visualiserar dina NSG-flödesloggar med PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Lär dig hur du [visualiserar dina NSG-flödesloggar med verktyg med öppen källkod](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
