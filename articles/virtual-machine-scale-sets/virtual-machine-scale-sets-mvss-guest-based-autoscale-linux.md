---
title: Använda Azure Autoskala med gäst mått i en mall för Linux skala | Microsoft Docs
description: Lär dig hur du Autoskala med gäst mått i en Skaluppsättning för Linux virtuella dator mall
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: negat
ms.openlocfilehash: 8e822d83dd3bafabfea60ad50224c87df226bdc6
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2017
ms.locfileid: "26781436"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Autoskala med gäst mått i en Linux-skala ange mall

Det finns två typer av mått i Azure som har samlats in från virtuella datorer och skala uppsättningar: vissa komma från den Virtuella värddatorn och andra komma från den Virtuella gästdatorn. På en hög nivå om du använder standard CPU, disk och nätverk statistik och, är sedan värden förmodligen passar bra. Om du behöver dock ett större antal mått kan sedan är gäst förmodligen en passar bättre. Låt oss ta en titt på skillnaderna mellan två:

Värden är enklare och mer tillförlitlig. De inte kräver ytterligare inställningar eftersom de samlas in av värden VM, medan gäst mått måste du installera den [Windows Azure-diagnostik tillägget](../virtual-machines/windows/extensions-diagnostics-template.md) eller [Linux Azure Diagnostics tillägget](../virtual-machines/linux/diagnostic-extension.md)på den Virtuella gästdatorn. En vanlig orsak till att använda gäst mått i stället värden mått är att gäst mått ger ett större antal mått än värden mått. Ett exempel är minnesförbrukning mätvärden som är bara tillgängliga via gäst mått. Mätvärdena stöds värden visas [här](../monitoring-and-diagnostics/monitoring-supported-metrics.md), och används ofta gäst mått visas [här](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md). Den här artikeln visar hur du ändrar den [lägsta lönsam skala ange mall](./virtual-machine-scale-sets-mvss-start.md) att använda automatiska regler baserat på gästen mätvärden för skalningsuppsättningar i Linux.

## <a name="change-the-template-definition"></a>Ändra malldefinitionen

Den lägsta lönsam skala mallen kan ses [här](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), och mallen för distribution av Linux-skala med gästbaserat Autoskala kan ses [här](https://raw.githubusercontent.com/gatneil/mvss/guest-based-autoscale-linux/azuredeploy.json). Låt oss nu undersöka diff som används för att skapa den här mallen (`git diff minimum-viable-scale-set existing-vnet`) bit för bit:

Lägg först till parametrar för `storageAccountName` och `storageAccountSasToken`. Diagnostik-agent lagrar mått data i en [tabellen](../cosmos-db/table-storage-how-to-use-dotnet.md) i det här lagringskontot. Från och med Linux diagnostik agenten version 3.0 stöds med hjälp av en lagringsåtkomstnyckel inte längre. Använd i stället en [SAS-Token](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

Ändra skaluppsättning `extensionProfile` med filnamnstillägget diagnostik. Ange resurs-ID för skaluppsättningen för att samla in mått från, samt lagringskontot och SAS-token du vill lagra måtten i den här konfigurationen. Ange hur ofta mätvärdena som aggregeras (i det här fallet varje minut) och vilka mått som ska spåras (i det här fallet, Procent använt minne). Mer detaljerad information om den här konfigurationen och mått än Procent använt minne finns [denna dokumentation](../virtual-machines/linux/diagnostic-extension.md).

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Slutligen lägger du till en `autoscaleSettings` resurs för att konfigurera Autoskala baserat på de här måtten. Den här resursen har en `dependsOn` -sats som refererar till skalan ställa in så att skaluppsättning finns innan du försöker att Autoskala den. Om du väljer ett annat mått Autoskala på, använder du den `counterSpecifier` från diagnostik tilläggets konfiguration som den `metricName` i Autoskala konfigurationen. Mer information om Autoskala konfiguration finns i [Autoskala metodtips](..//monitoring-and-diagnostics/insights-autoscale-best-practices.md) och [Azure övervakaren REST API-referensdokumentation](https://msdn.microsoft.com/library/azure/dn931928.aspx).

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>Nästa steg

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
