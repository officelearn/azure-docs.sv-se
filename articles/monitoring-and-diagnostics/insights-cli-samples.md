---
title: Azure Monitor CLI 2.0 Snabbstart-exempel. | Microsoft Docs
description: CLI 2.0 exempelkommandon för Azure-Monitor-funktioner. Azure övervakaren är en Microsoft Azure-tjänst som gör att du kan skicka aviseringar, anropa webbadresser som baseras på värden för konfigurerade telemetridata och Autoskala molntjänster, virtuella datorer och Web Apps.
author: kamathashwin
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1653aa81-0ee6-4622-9c65-d4801ed9006f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2018
ms.author: ashwink
ms.openlocfilehash: 475ee794d2b67639b447a1ca66b12d3d589425cb
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="azure-monitor-cli-20-quick-start-samples"></a>Azure-Monitor CLI 2.0 Snabbstart-exempel
Den här artikeln visar exempel kommandoradsgränssnittet (CLI)-kommandon som hjälper dig att komma åt Azure-Monitor-funktioner. Azure-Monitor kan du Autoskala molntjänster, virtuella datorer och Web Apps att skicka aviseringar eller anropa webbadresser som baseras på värden för konfigurerade telemetridata.

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har installerat Azure CLI, följer du instruktionerna för [installera Azure CLI 2.0](/cli/azure/install-azure-cli). Du kan också använda [Azure Cloud Shell](/azure/cloud-shell) köra CLI som en interaktiv upplevelse i webbläsaren. 

## <a name="log-in-to-azure"></a>Logga in på Azure
Det första steget är att logga in på ditt Azure-konto.

```azurecli
az login
```

Du måste logga in via anvisningarna på skärmen när du har kört kommandot. Alla kommandon som fungerar i samband med standard-prenumeration.

Använd följande kommando om du vill visa information om din aktuella prenumeration.

```azurecli
az account show
```

Använd följande kommando om du vill ändra fungerande kontexten till en annan prenumeration.

```azurecli
az account set -s <Subscription ID or name>
```

Gör följande om du vill visa en lista över alla kommandon som stöds i Azure-Monitor.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Visa aktivitetsloggen för en prenumeration

Gör följande om du vill visa en lista över aktiviteten logghändelser.

```azurecli
az monitor activity-log list
```

Gör något av följande om du vill visa alla tillgängliga alternativ.

```azurecli
az monitor activity-log list -h
```

Här är ett exempel i listan loggar av en resursgrupp

```azurecli
az monitor activity-log list --resource-group <group name>
```

Till exempel för att lista loggar av anroparen

```azurecli
az monitor activity-log list --caller myname@company.com
```

Till exempel för att lista loggar av anroparen på en resurstyp inom ett datumintervall

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Arbeta med aviseringar

Du kan använda informationen i avsnittet för att arbeta med aviseringar.

### <a name="get-alert-rules-in-a-resource-group"></a>Hämta Varningsregler i en resursgrupp

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-rule"></a>Skapa ett mått varningsregel

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-rule"></a>Ta bort en varningsregel

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Log-profiler

Använd informationen i det här avsnittet för att arbeta med loggen profiler.

### <a name="get-a-log-profile"></a>Hämta en logg-profil

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Lägg till en logg-profil med kvarhållning

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Lägg till en logg-profil med kvarhållning och EventHub

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Ta bort en logg-profil

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnostik

Använd informationen i det här avsnittet för att arbeta med diagnostikinställningar.

### <a name="get-a-diagnostic-setting"></a>Hämta en diagnostikinställningen

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-log-setting"></a>Skapa en diagnostisk log-inställning 

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

### <a name="delete-a-diagnostic-setting"></a>Ta bort en diagnostikinställningen

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Automatisk skalning

Använd informationen i det här avsnittet för att arbeta med Autoskala inställningar. Du behöver ändra exemplen.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Hämta Autoskala inställningar för en resursgrupp

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Hämta inställningar för Autoskala efter namn i en resursgrupp

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Ange inställningar för Autoskala

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```
