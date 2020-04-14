---
title: Använda Automatisk Azure-skalning med gästmått i en Linux-skalningsuppsättningsmall
description: Lär dig hur du automatiskt skalar med hjälp av gästmått i en Linux Virtual Machine Scale Set-mall
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimckitt
ms.openlocfilehash: 8021b7b8feb6dc06fb2e48bc4e825200a1baad33
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273655"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Automatisk skalning med hjälp av gästmått i en Mall för Linux-skalning

Det finns två breda typer av mått i Azure som samlas in från virtuella datorer och skalningsuppsättningar: Värdmått och gästmått. På en hög nivå, om du vill använda standard CPU, disk och nätverksmått, då värdmått är en bra passform. Om du behöver ett större urval av mått bör gästmått granskas.

Värdmått kräver inte ytterligare inställningar eftersom de samlas in av den virtuella värddatorn, medan gästmått kräver att du installerar [Tillägget Windows Azure Diagnostics](../virtual-machines/windows/extensions-diagnostics-template.md) eller [Linux Azure Diagnostics-tillägget](../virtual-machines/linux/diagnostic-extension.md) i gästdatorn. En vanlig orsak till att använda gästmått i stället för värdmått är att gästmått ger ett större urval av mått än värdmått. Ett sådant exempel är mätvärden för minnesförbrukning, som endast är tillgängliga via gästmått. Värdmåtten som stöds visas [här](../azure-monitor/platform/metrics-supported.md)och vanliga gästmått visas [här](../azure-monitor/platform/autoscale-common-metrics.md). Den här artikeln visar hur du ändrar den [grundläggande skalluppsättningsmallen](virtual-machine-scale-sets-mvss-start.md) för att använda regler för automatisk skalning baserat på gästmått för Linux-skalningsuppsättningar.

## <a name="change-the-template-definition"></a>Ändra malldefinitionen

I en [tidigare artikel](virtual-machine-scale-sets-mvss-start.md) hade vi skapat en grundläggande skalningsuppsättningsmall. Vi kommer nu att använda den tidigare mallen och ändra den för att skapa en mall som distribuerar en Linux-skalningsuppsättning med gästmåttbaserad automatisk skalning.

Lägg först till `storageAccountName` `storageAccountSasToken`parametrar för och . Diagnostikagenten lagrar måttdata i en [tabell](../cosmos-db/table-storage-how-to-use-dotnet.md) i det här lagringskontot. Från och med Linux Diagnostics Agent version 3.0 stöds inte längre användning av en lagringsåtkomstnyckel. Använd i stället en [SAS-token](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

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

Ändra sedan skalningsuppsättningen `extensionProfile` så att den innehåller diagnostiktillägget. I den här konfigurationen anger du resurs-ID:t för skalningsuppsättningen för att samla in mått från, samt lagringskontot och SAS-token som ska användas för att lagra måtten. Ange hur ofta måtten aggregeras (i det här fallet varje minut) och vilka mått som ska spåras (i det här fallet procent använt minne). Mer detaljerad information om den här konfigurationen och andra mått än procent av använt minne finns i [den här dokumentationen](../virtual-machines/linux/diagnostic-extension.md).

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

Lägg slutligen `autoscaleSettings` till en resurs för att konfigurera automatisk skalning baserat på dessa mått. Den här `dependsOn` resursen har en sats som refererar till skalningsuppsättningen för att säkerställa att skalningsuppsättningen finns innan du försöker skala den automatiskt. Om du väljer ett annat mått att automatiskt `counterSpecifier` skala på, skulle `metricName` du använda från diagnostiktillägg konfigurationen som i konfigurationen för automatisk skalning. Mer information om konfiguration för automatisk skalning finns i [metodtipsen för automatisk skalning](../azure-monitor/platform/autoscale-best-practices.md) och [referensdokumentationen för AZURE Monitor REST API](/rest/api/monitor/autoscalesettings).

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
