---
title: Snabbstartsexempel för Azure Monitor-CLI
description: Exemplet CLI-kommandon för Azure Monitor-funktioner. Azure Monitor är en Microsoft Azure-tjänst som låter dig skicka aviseringar, anropa webbadresser som baseras på värden för konfigurerade telemetridata, och automatisk skalning molntjänster, virtuella datorer och Webbappar.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: robb
ms.component: ''
ms.openlocfilehash: 5fb4e4f754fdac9e72fc9317c7e1ccfa6a08feb2
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53326367"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>Snabbstartsexempel för Azure Monitor-CLI
Den här artikeln visar exempel kommandoradsgränssnittet (CLI)-kommandon för att få åtkomst till Azure Monitor-funktioner. Azure Monitor kan du att automatisk skalning molntjänster, virtuella datorer och Webbappar och för att skicka aviseringar eller anropa webbadresser som baseras på värden för konfigurerade telemetridata.

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har installerat Azure CLI, följer du anvisningarna för [installera Azure CLI](/cli/azure/install-azure-cli). Du kan också använda [Azure Cloud Shell](/azure/cloud-shell) att köra CLI som en interaktiv upplevelse i din webbläsare. Se fullständiga referenser för alla tillgängliga kommandon finns i den [Azure Monitor CLI-referensen](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Logga in på Azure
Det första steget är att logga in på ditt Azure-konto.

```azurecli
az login
```

Du måste logga in via anvisningarna på skärmen när du har kört det här kommandot. Alla kommandon fungerar i kontexten för din Standardprenumeration.

Använd följande kommando om du vill visa information om din aktuella prenumeration.

```azurecli
az account show
```

Använd följande kommando om du vill ändra fungerande kontext till en annan prenumeration.

```azurecli
az account set -s <Subscription ID or name>
```

Om du vill visa en lista över alla kommandon som stöds i Azure Monitor, utför du följande.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Visa aktivitetslogg för en prenumeration

Utför följande om du vill visa en lista över händelser i aktivitetsloggen.

```azurecli
az monitor activity-log list
```

Prova följande för att visa alla tillgängliga alternativ.

```azurecli
az monitor activity-log list -h
```

Här är ett exempel för att lista loggar av en resourceGroup

```azurecli
az monitor activity-log list --resource-group <group name>
```

Exempel för att lista loggar av anroparen

```azurecli
az monitor activity-log list --caller myname@company.com
```

Exempel för att lista loggar av anroparen på en resurstyp i ett datumintervall

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Arbeta med aviseringar 
> [!NOTE]
> Bara aviseringar (klassisk) stöds i CLI just nu. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Hämta aviseringsregler (klassisk) i en resursgrupp

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Skapa en måttavisering (klassisk) aviseringsregel

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Ta bort en varningsregel (klassisk)

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Loggprofiler

Använd informationen i det här avsnittet för att arbeta med loggprofiler.

### <a name="get-a-log-profile"></a>Hämta en loggprofil

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Lägg till en loggprofil med kvarhållning

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Lägg till en loggprofil med kvarhållning och EventHub

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Ta bort en loggprofil

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnostik

Använd informationen i det här avsnittet för att arbeta med diagnostikinställningar.

### <a name="get-a-diagnostic-setting"></a>Hämta en diagnostikinställning

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-log-setting"></a>Skapa en diagnostiklogg-inställning 

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <storage account ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

### <a name="delete-a-diagnostic-setting"></a>Ta bort en diagnostikinställning

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Automatisk skalning

Använd informationen i det här avsnittet för att arbeta med inställningarna för automatisk skalning. Du behöver ändra de här exemplen.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Hämta inställningarna för automatisk skalning för en resursgrupp

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Hämta inställningarna för automatisk skalning efter namn i en resursgrupp

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Ange inställningarna för automatisk skalning

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```
