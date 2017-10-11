---
title: "Referera till ett befintligt virtuellt nätverk i en mall för Azure skala | Microsoft Docs"
description: "Lär dig hur du lägger till ett virtuellt nätverk i en befintlig mall för Azure Virtual Machine Scale Set"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: negat
ms.openlocfilehash: 28117d467b491704aed8d45e5eba42530579dfa2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Lägg till referens till ett befintligt virtuellt nätverk i en mall för Azure skala

Den här artikeln visar hur du ändrar den [lägsta lönsam skala ange mall](./virtual-machine-scale-sets-mvss-start.md) att distribuera till ett befintligt virtuellt nätverk i stället för att skapa en ny.

## <a name="change-the-template-definition"></a>Ändra malldefinitionen

Mall för våra lägsta lönsam skala kan ses [här](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), och våra mallen för distribution av skalan i ett befintligt virtuellt nätverk kan ses [här](https://raw.githubusercontent.com/gatneil/mvss/existing-vnet/azuredeploy.json). Låt oss nu undersöka diff som används för att skapa den här mallen (`git diff minimum-viable-scale-set existing-vnet`) bit för bit:

Först måste vi lägga till en `subnetId` parameter. Den här strängen skickas till skala ange konfiguration så att skaluppsättningen att identifiera om du vill distribuera virtuella datorer i förväg skapade undernätet. Strängen måste vara i formatet: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`. Ange till exempel att distribuera skalan i ett befintligt virtuellt nätverk med namnet `myvnet`, undernät `mysubnet`, resursgrupp `myrg`, och prenumeration `00000000-0000-0000-0000-000000000000`, subnetId skulle vara: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

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

Nu ska vi kan ta bort resursen virtuellt nätverk från den `resources` matrisen eftersom vi använder ett befintligt virtuellt nätverk och behöver inte distribuera en ny.

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

Det virtuella nätverket redan innan mallen har distribuerats, så inte behöver du ange en dependsOn-sats från skaluppsättningen till det virtuella nätverket. Därför vi ta bort dessa rader:

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

Slutligen kan vi skicka in den `subnetId` parameter har angetts av användaren (istället för att använda `resourceId` för att hämta id för ett vnet i samma distribution, vilket är vad den lägsta lönsam skalan ange mall har).

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
