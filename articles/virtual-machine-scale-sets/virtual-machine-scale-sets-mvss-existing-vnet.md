---
title: Referera till ett befintligt virtuellt nätverk i en mall för Azure skala | Microsoft Docs
description: Lär dig hur du lägger till ett virtuellt nätverk i en befintlig mall för Azure Virtual Machine Scale Set
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: negat
ms.openlocfilehash: eb35975de5864e129f97b614a61487456dd972ef
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2017
ms.locfileid: "26782379"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Lägg till referens till ett befintligt virtuellt nätverk i en mall för Azure skala

Den här artikeln visar hur du ändrar den [lägsta lönsam skala ange mall](./virtual-machine-scale-sets-mvss-start.md) att distribuera till ett befintligt virtuellt nätverk i stället för att skapa en ny.

## <a name="change-the-template-definition"></a>Ändra malldefinitionen

Den lägsta lönsam skala mallen kan ses [här](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), och mallen för distribution av skalan i ett befintligt virtuellt nätverk kan ses [här](https://raw.githubusercontent.com/gatneil/mvss/existing-vnet/azuredeploy.json). Låt oss nu undersöka diff som används för att skapa den här mallen (`git diff minimum-viable-scale-set existing-vnet`) bit för bit:

Lägg först till en `subnetId` parameter. Den här strängen skickas till skala ange konfiguration så att skaluppsättningen att identifiera om du vill distribuera virtuella datorer i förväg skapade undernätet. Strängen måste vara i formatet: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`. Ange till exempel att distribuera skalan i ett befintligt virtuellt nätverk med namnet `myvnet`, undernät `mysubnet`, resursgrupp `myrg`, och prenumeration `00000000-0000-0000-0000-000000000000`, subnetId skulle vara: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

Ta sedan bort den virtuella nätverksresursen från den `resources` matrisen när du använder ett befintligt virtuellt nätverk och behöver inte distribuera en ny.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2016-12-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

Det virtuella nätverket redan innan mallen har distribuerats, så inte behöver du ange en dependsOn-sats från skaluppsättningen till det virtuella nätverket. Ta bort följande rader:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Slutligen skicka in den `subnetId` parameter har angetts av användaren (istället för att använda `resourceId` för att hämta ID för ett vnet i samma distribution, vilket är vad den lägsta lönsam skalan ange mall har).

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>Nästa steg

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
