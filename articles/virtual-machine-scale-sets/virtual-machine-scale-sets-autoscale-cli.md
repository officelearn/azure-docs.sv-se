---
title: "Autoskala virtuella skalningsuppsättningarna med Azure CLI | Microsoft Docs"
description: "Hur du skapar automatiska regler för den virtuella datorn anger med Azure CLI 2.0"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 83e93d9c-cac0-41d3-8316-6016f5ed0ce4
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 8552f6b2723fef2c61d49a34d2d60c2a6c209a32
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Skala automatiskt en virtuell dator-skala med Azure CLI 2.0
När du skapar en skaluppsättning för definiera antalet VM-instanser som du vill köra. När din begäran för program ändras, kan du automatiskt öka eller minska antalet VM-instanser. Möjligheten att Autoskala kan du Håll dig uppdaterad med kundernas behov eller svara på ändringar i programmet prestanda under hela livscykeln för din app.

Den här artikeln visar hur du skapar automatiska regler med Azure CLI 2.0 som övervaka prestanda för VM-instanser i en skaluppsättning. Reglerna Autoskala öka eller minska antalet VM-instanser som svar på dessa prestandamått. Du kan också utföra dessa steg med [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) eller i den [Azure-portalen](virtual-machine-scale-sets-autoscale-portal.md).


## <a name="prerequisites"></a>Förutsättningar
Du behöver en befintlig virtuell dator för att skapa regler för automatiska skaluppsättning. Du kan skapa en skala som anges med den [Azure-portalen](virtual-machine-scale-sets-create-portal.md), [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md), eller [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md).

Att göra det enklare att skapa automatiska regler, definiera vissa variabler för din skaluppsättning. I följande exempel definieras variabler för skaluppsättningen namngivna *myScaleSet* i resursgrupp med namnet *myResourceGroup* och i den *eastus* region. Din prenumeration hämta ID med [az konto visa](/cli/azure/account#az_account_show). Om du har flera prenumerationer som är kopplade till ditt konto, returneras endast det första abonnemanget. Justera namn och prenumerations-ID på följande sätt:

```azurecli
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

## <a name="define-an-autoscale-profile"></a>Definiera en Autoskala-profil
Autoskala regler distribueras som JSON (JavaScript Object Notation) med Azure CLI 2.0. Fullständig JSON som definierar och distribuerar Autoskala regler finns senare i artikeln. 

Början av Autoskala profil definierar standardvärdet är lägsta och högsta kapacitet. I följande exempel anger standardinställningen och minimum kapacitet *2* VM-instanser och högst *10*:

```json
{
  "name": "autoscale rules",
  "capacity": {
    "minimum": "2",
    "maximum": "10",
    "default": "2"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-out"></a>Skapa en regel för att automatiskt skala ut
Om ditt program begäran ökar, ange ökar belastningen på VM-instanser i nivå. Om den här ökade belastningen är konsekvent, i stället för bara en kort begäran, kan du konfigurera automatiska regler för att öka antalet VM-instanser i skaluppsättning. När dessa VM-instanser som skapas och dina program distribueras börjar skaluppsättning distribuera trafik till dem via belastningsutjämnaren. Du kan styra vilka mått som ska övervakas, t.ex CPU eller disk, hur länge belastningen program måste uppfylla ett visst tröskelvärde och ange hur många VM-instanser som ska läggas till skalan.

Nu ska vi skapa en regel som ökar antalet VM-instanser i en skala som anges när Genomsnittlig CPU-belastningen är större än 70% under en period om 10 minuter. När regeln utlöser ökar antalet VM-instanser med 20%. I skalningsuppsättningar med ett litet antal VM-instanser som du kan ange den `type` till *ChangeCount* och öka den `value` av *1* eller *2* instanser. VM-instanser kan vara mer lämpliga i skalningsuppsättningar med ett stort antal VM-instanser, en ökning av 10% eller 20%.

Följande parametrar används för den här regeln:

| Parameter         | Förklaring                                                                                                         | Värde           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | Prestanda-mått för att övervaka och tillämpa skala ange åtgärder på.                                                   | Processorprocentandel  |
| *Tidskorn*       | Hur ofta mätvärdena som har samlats in för analys.                                                                   | 1 minut        |
| *timeAggregation* | Definierar hur mätvärdena som samlats in ska aggregeras för analys.                                                | Medel         |
| *värdet timeWindow*      | Tidsperiod som övervakas innan värdena mått och tröskelvärdet jämförs.                                   | 10 minuter      |
| *operatorn*        | Operator som används för att jämföra måttinformationen mot tröskelvärdet.                                                     | Större än    |
| *Tröskelvärde*       | Det värde som regeln Autoskala kan utlösa en åtgärd.                                                      | 70%             |
| *riktning*       | Anger om skaluppsättning bör skala upp eller ned när regeln gäller.                                             | Höj        |
| *typ*            | Anger att antalet VM-instanser som ska ändras som ett procenttal.                                 | Procentuell förändring  |
| *värdet*           | Hur många VM-instanser som ska skalas uppåt eller nedåt när regeln gäller.                                            | 20              |
| *cooldown*        | Hur lång tid ska gå innan regeln tillämpas igen så att automatiska åtgärder har tid att börja gälla. | 5 minuter       |

I följande exempel definierar regeln för att skala ut antalet VM-instanser. Den *metricResourceUri* använder de variabler som tidigare definierats för prenumerations-ID, resursgruppens namn och skala namn:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "GreaterThan",
    "threshold": 70
  },
  "scaleAction": {
    "direction": "Increase",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-in"></a>Skapa en regel för att skala automatiskt i
På en kväll eller helger minska program-begäran. Om det här minskar belastningen är konsekvent under en viss tidsperiod, kan du konfigurera automatiska regler för att minska antalet VM-instanser i skaluppsättning. Den här åtgärden för skala minskar kostnaden för att köra skaluppsättningen eftersom du bara köra antalet instanser som krävs för att uppfylla den aktuella begäran.

Skapa en annan regel som minskar antalet VM-instanser i en skala som anges när Genomsnittlig CPU-belastningen sedan sjunker under 30% över 10-minutersperiod. I följande exempel definierar regeln för att skala ut antalet VM-instanser. Den *metricResourceUri* använder de variabler som tidigare definierats för prenumerations-ID, resursgruppens namn och skala namn:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Tillämpa automatiska regler för en skaluppsättning
Det sista steget är att använda Autoskala profil och regler för din skaluppsättning. Nivå kan sedan automatiskt skala in eller ut baserat på begäran för program. Använda Autoskala profil med [az Autoskala-inställningar för övervakning av skapa](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) på följande sätt. Fullständig JSON använder profilen och regler som anges i föregående avsnitt.

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "autoscale by percentage based on CPU usage",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "PercentChangeCount",
                "value": "20",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "PercentChangeCount",
                "value": "20",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Övervaka antalet instanser i en skaluppsättning
Visa en lista över instanser för att se antalet och status för VM-instanser i nivå med [az vmss listinstanserna](/cli/azure/vmss#az_vmss_list_instances). Statusen anger om den Virtuella datorinstansen etablering som skaluppsättningen automatiskt skalas ut eller är avetablering som skalan skalas automatiskt i. I följande exempel visar VM instansens status för skaluppsättningen namngivna *myScaleSet* i resursgrupp med namnet *myResourceGroup*:

```azurecli
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```


## <a name="autoscale-based-on-a-schedule"></a>Autoskala enligt ett schema
I föregående exempel skalas automatiskt en skala ställa in eller ut med grundläggande värden, till exempel CPU-användning. Du kan också skapa automatiska regler baserat på scheman. Reglerna schemabaserade kan du skala automatiskt antalet VM-instanser i en förväntad ökning i programmet begäran, till exempel core arbetstid, och sedan automatiskt skala antalet instanser samtidigt som du planerar mindre begäran som det är helgen.

Skapa en JSON-profil som definierar antalet VM-instanser körs för en fast start och end tidsfönster om du vill använda schemabaserade Autoskala regler. I följande exempel definierar en regel för att skala ut till *10* instanser på *9* am alla arbetsdagar (måndag-fredag).

```json
{
  "name": "Scale out during each work day",
  "capacity": {
      "minimum": "10",
      "maximum": "10",
      "default": "10"
  },
  "rules": [],
  "recurrence": {
      "frequency": "Week",
      "schedule": {
          "timeZone": "Pacific Standard Time",
          "days": [
              "Monday",
              "Tuesday",
              "Wednesday",
              "Thursday",
              "Friday"
          ],
          "hours": [
              9
          ],
          "minutes": [
              0
          ]
      }
  }
}
```

Om du vill skala i på kvällar, skapa en annan regel som anger ett lägre antal VM-instanser och en lämplig starttid.

Följande komplett exempel definierar reglerna att skala ut och skala och använder sedan Autoskala profil med [az Autoskala-inställningar för övervakning av skapa](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create). Det här exemplet skriver över mått-baserade Autoskala reglerna som skapas i föregående exempel. Den *metricResourceUri* använder de variabler som tidigare definierats för prenumerations-ID, resursgruppens namn och skala namn:

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "Scale out during each work day",
          "capacity": {
            "minimum": "10",
            "maximum": "10",
            "default": "10"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                9
              ],
              "minutes": [
                0
              ]
            }
          }
        },
        {
          "name": "Scale in during the evening",
          "capacity": {
            "minimum": "3",
            "maximum": "3",
            "default": "3"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                18
              ],
              "minutes": [
                0
              ]
            }
          }
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du använder automatiska regler för att skala horisontellt och öka eller minska den *nummer* i VM-instanser i nivå. Du kan även skala lodrätt för att öka eller minska den Virtuella datorinstansen *storlek*. Mer information finns i [lodräta Autoskala med skalningsuppsättningar i virtuella](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Information om hur du hanterar dina VM-instanser finns [hantera virtuella skalningsuppsättningarna med Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Information om hur du genererar aviseringar när din Autoskala regler utlösaren finns [använda automatiska åtgärder för att skicka e-post och webhook aviseringar i Azure-Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Du kan också [Använd granskningsloggarna för att skicka e-post och webhook aviseringar i Azure-Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
