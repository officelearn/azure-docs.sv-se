---
title: "Azure övervakaren CLI 1.0 Snabbstart-exempel. | Microsoft Docs"
description: "Exempel CLI 1.0-kommandon för Azure-Monitor-funktioner. Azure övervakaren är en Microsoft Azure-tjänst som gör att du kan skicka aviseringar, anropa webbadresser som baseras på värden för konfigurerade telemetridata och Autoskala molntjänster, virtuella datorer och Web Apps."
author: kamathashwin
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1653aa81-0ee6-4622-9c65-d4801ed9006f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: ashwink
ms.openlocfilehash: ec4512500dc3c77a40d2ebd1e6b460d5bb005811
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-monitor--cross-platform-cli-10-quick-start-samples"></a>Azure CLI för övervakaren plattformsoberoende 1.0 Snabbstart-exempel
Den här artikeln visar exempel kommandoradsgränssnittet (CLI)-kommandon som hjälper dig att komma åt Azure-Monitor-funktioner. Azure-Monitor kan du Autoskala molntjänster, virtuella datorer och Web Apps att skicka aviseringar eller anropa webbadresser som baseras på värden för konfigurerade telemetridata.

> [!NOTE]
> Azure övervakaren är det nya namnet för vad anropades ”Azure Insights” förrän den 25 september 2016. Namnområden och därmed nedanstående kommandon fortfarande innehåller dock ”insikter”.
> 
> 

## <a name="prerequisites"></a>Krav
Om du inte redan har installerat Azure CLI, se [installerar Azure CLI](../cli-install-nodejs.md). Om du är bekant med Azure CLI, kan du läsa mer om den på [använder Azure CLI för Mac, Linux och Windows med Azure Resource Manager](../xplat-cli-azure-resource-manager.md).

I Windows, installera npm från den [Node.js webbplats](https://nodejs.org/). När du har slutfört installationen med hjälp av CMD.exe med Kör som administratör, kör du följande från mappen där npm är installerad:

```console
npm install azure-cli --global
```

Nu ska du navigera till mappen/plats och du vill ange på kommandoraden:

```console
azure help
```

## <a name="log-in-to-azure"></a>Logga in på Azure
Det första steget är att logga in på ditt Azure-konto.

```console
azure login
```

Du måste logga in via anvisningarna på skärmen när du har kört kommandot. Därefter kan se du ditt konto, TenantId och standard prenumerations-Id. Alla kommandon som fungerar i samband med standard-prenumeration.

Använd följande kommando om du vill visa information om din aktuella prenumeration.

```console
azure account show
```

Använd följande kommando om du vill ändra fungerande kontexten till en annan prenumeration.

```console
azure account set "subscription ID or subscription name"
```

Du måste vara i läget Azure Resource Manager om du vill använda Azure Resource Manager och Azure-Monitor-kommandon.

```console
azure config mode arm
```

Gör följande om du vill visa en lista över alla kommandon som stöds i Azure-Monitor.

```console
azure insights
```

## <a name="view-activity-log-for-a-subscription"></a>Visa aktivitetsloggen för en prenumeration
Gör följande om du vill visa en lista över aktiviteten logghändelser.

```console
azure insights logs list [options]
```

Gör något av följande om du vill visa alla tillgängliga alternativ.

```console
azure insights logs list -help
```

Här är ett exempel i listan loggar av en resursgrupp

```console
azure insights logs list --resourceGroup "myrg1"
```

Till exempel för att lista loggar av anroparen

```console
azure insights logs list --caller "myname@company.com"
```

Till exempel för att lista loggar som anroparen på en resurstyp i start-och slut

```console
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Arbeta med aviseringar
Du kan använda informationen i avsnittet för att arbeta med aviseringar.

### <a name="get-alert-rules-in-a-resource-group"></a>Hämta Varningsregler i en resursgrupp
```console
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>Skapa ett mått varningsregel
```console
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-webtest-alert-rule"></a>Skapa varningsregeln för webbtestet
```console
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>Ta bort en varningsregel
```console
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>Log-profiler
Använd informationen i det här avsnittet för att arbeta med loggen profiler.

### <a name="get-a-log-profile"></a>Hämta en logg-profil
```console
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>Lägg till en logg profil utan kvarhållning
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Ta bort en logg-profil
```console
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>Lägg till en logg-profil med kvarhållning
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Lägg till en logg-profil med kvarhållning och EventHub
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>Diagnostik
Använd informationen i det här avsnittet för att arbeta med diagnostikinställningar.

### <a name="get-a-diagnostic-setting"></a>Hämta en diagnostikinställningen
```console
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>Inaktivera en diagnostikinställningen
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>Aktivera diagnostikinställningen utan kvarhållning
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>Automatisk skalning
Använd informationen i det här avsnittet för att arbeta med Autoskala inställningar. Du behöver ändra exemplen.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Hämta Autoskala inställningar för en resursgrupp
```console
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Hämta inställningar för Autoskala efter namn i en resursgrupp
```console
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>Ange inställningar för auotoscale
```console
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```
