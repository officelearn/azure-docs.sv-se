---
title: Snabbstartsexempel för Azure Monitor CLI
description: Exempel på CLI-kommandon för Azure Monitor-funktioner. Azure Monitor är en Microsoft Azure-tjänst som låter dig skicka aviseringar, anropa webb-URL:er baserat på värden för konfigurerade telemetridata och molntjänster för automatisk skalning, virtuella datorer och webbappar.
ms.subservice: ''
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: 69687cee8b8a907b82f2c848242ac64d54dedb87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248936"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>Snabbstartsexempel för Azure Monitor CLI
I den här artikeln visas exempel på CLI-kommandon (Command-Line Interface) som hjälper dig att komma åt Azure Monitor-funktioner. Med Azure Monitor kan du automatiskt skala molntjänster, virtuella datorer och webbappar och skicka aviseringsmeddelanden eller anropa webb-URL:er baserat på värden för konfigurerade telemetridata.

## <a name="prerequisites"></a>Krav

Om du inte redan har installerat Azure CLI följer du instruktionerna för [Installera Azure CLI](/cli/azure/install-azure-cli). Du kan också använda [Azure Cloud Shell](/azure/cloud-shell) för att köra CLI som en interaktiv upplevelse i din webbläsare. Se en fullständig referens för alla tillgängliga kommandon i [Azure Monitor CLI-referensen](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Logga in på Azure
Det första steget är att logga in på ditt Azure-konto.

```azurecli
az login
```

När du har kört det här kommandot måste du logga in via instruktionerna på skärmen. Alla kommandon fungerar i samband med din standardprenumeration.

Om du vill visa information om din aktuella prenumeration använder du följande kommando.

```azurecli
az account show
```

Om du vill ändra arbetskontexten till en annan prenumeration använder du följande kommando.

```azurecli
az account set -s <Subscription ID or name>
```

Om du vill visa en lista över alla Azure Monitor-kommandon som stöds utför du följande.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Visa aktivitetslogg för en prenumeration

Om du vill visa en lista över aktivitetslogghändelser utför du följande.

```azurecli
az monitor activity-log list
```

Prova följande om du vill visa alla tillgängliga alternativ.

```azurecli
az monitor activity-log list -h
```

Här är ett exempel på listloggar av en resourceGroup

```azurecli
az monitor activity-log list --resource-group <group name>
```

Exempel på listloggar efter uppringare

```azurecli
az monitor activity-log list --caller myname@company.com
```

Exempel på listloggar efter anropare på en resurstyp, inom ett datumintervall

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Arbeta med aviseringar 
> [!NOTE]
> Endast aviseringar (klassiska) stöds i CLI just nu. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Få aviseringsregler (klassiska) i en resursgrupp

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Skapa en måttavisering (klassisk) regel

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Ta bort en aviseringsregel (klassisk)

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Loggprofiler

Använd informationen i det här avsnittet om du vill arbeta med loggprofiler.

### <a name="get-a-log-profile"></a>Skaffa en loggprofil

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Lägga till en loggprofil med kvarhållning

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Lägga till en loggprofil med kvarhållning och EventHub

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

### <a name="get-a-diagnostic-setting"></a>Skaffa en diagnostisk inställning

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-setting"></a>Skapa en diagnostikinställning 

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

Använd informationen i det här avsnittet om du vill arbeta med inställningar för automatisk skalning. Du måste ändra dessa exempel.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Hämta inställningar för automatisk skalning för en resursgrupp

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Hämta inställningar för automatisk skalning efter namn i en resursgrupp

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Ange inställningar för automatisk skalning

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```

