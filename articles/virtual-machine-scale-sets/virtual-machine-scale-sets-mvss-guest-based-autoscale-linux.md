---
title: Använd automatisk skalning i Azure med gästmått i en skalningsuppsättningsmall för Linux | Microsoft Docs
description: Lär dig hur du automatisk skalning med hjälp av gästmått i en mall för Linux Virtual Machine Scale Sets
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 8cd665ffd82547c4f554eb4a515a8da7dc5b3f5f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64868994"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Automatisk skalning med gästmått i en skalningsuppsättningsmall för Linux

Det finns två generella typer av mått i Azure som har samlats in från virtuella datorer och skalningsuppsättningar: Värdmått och gästmått. På en hög nivå om du vill använda standard CPU, disk och nätverk mätvärden, är sedan värdmått ett bra alternativ. Om du behöver dock ett större antal mått, sedan gästmått bör ha i åtanke till.

Värdmått kräver inte ytterligare inställningar eftersom de har samlats in av värden VM, medan gästmått måste du installera den [Windows Azure-diagnostiktillägget](../virtual-machines/windows/extensions-diagnostics-template.md) eller [Linux Azure-diagnostiktillägget ](../virtual-machines/linux/diagnostic-extension.md) på den Virtuella gästdatorn. En vanlig orsak till att använda gästmått i stället för värdmått är att gästmått ger ett större antal mått än värdmått. Ett exempel är minnesförbrukning mätvärden, som endast är tillgängliga via gästmått. Mått för stöds värden [här](../azure-monitor/platform/metrics-supported.md), och i vanliga gästmått [här](../azure-monitor/platform/autoscale-common-metrics.md). Den här artikeln visar hur du ändrar den [grundläggande lönsamma skaluppsättningsmall](virtual-machine-scale-sets-mvss-start.md) att använda regler för automatisk skalning baserat på gästmått för Linux-skalningsuppsättningar.

## <a name="change-the-template-definition"></a>Ändra malldefinitionen

I en [föregående artikel](virtual-machine-scale-sets-mvss-start.md) vi har skapat en grundläggande skalningsuppsättningsmall. Vi kommer nu använda mallen som tidigare och ändra det om du vill skapa en mall som distribuerar en Linux-skalningsuppsättning med gästen mått baserat automatisk skalning.

Lägg först till parametrar för `storageAccountName` och `storageAccountSasToken`. Diagnostics-agenten lagrar måttdata i en [tabell](../cosmos-db/table-storage-how-to-use-dotnet.md) i det här lagringskontot. Från och med Diagnostikagenten Linux version 3.0 stöds med hjälp av en lagringsåtkomstnyckel inte längre. Använd i stället en [SAS-Token](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

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

Sedan ändrar skalningsuppsättningen `extensionProfile` att inkludera diagnostics-tillägg. Ange resurs-ID för skalningsuppsättningen att samla in mått från, samt lagringskontot och SAS-token som du använder för att lagra mått i den här konfigurationen. Ange hur ofta måtten sammanställs (i det här fallet varje minut) och vilka mått som ska spåras (i det här fallet, Procent använt minne). Mer detaljerad information om den här konfigurationen och mått än Procent använt minne finns i [den här dokumentationen](../virtual-machines/linux/diagnostic-extension.md).

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

Slutligen lägger du till en `autoscaleSettings` resursen för att konfigurera automatisk skalning baserat på de här måtten. Den här resursen har en `dependsOn` satsen som refererar till skalan ställa in så att det finns skalningsuppsättningen innan du försöker att automatiskt skala den. Om du väljer ett annat mått för automatisk skalning på, använder du den `counterSpecifier` från konfigurationen för diagnostiktillägg som den `metricName` i konfigurationen för automatisk skalning. Mer information om autoskalningskonfigurationen finns i den [Metodtips för autoskalning](../azure-monitor/platform/autoscale-best-practices.md) och [Azure Monitor REST API-referensdokumentation](/rest/api/monitor/autoscalesettings).

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
