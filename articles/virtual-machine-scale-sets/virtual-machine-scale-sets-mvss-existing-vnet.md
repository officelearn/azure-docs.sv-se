---
title: Referera till ett befintligt virtuellt nätverk i en Azure skalningsuppsättningsmall | Microsoft Docs
description: Lär dig hur du lägger till ett virtuellt nätverk i en befintlig mall för Azure Virtual Machine Scale Sets
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 8b75b9898eb767866c0843594a82570cfb65d122
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64868954"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Lägg till referens till ett befintligt virtuellt nätverk i en mall för Azure skalningsuppsättningen

Den här artikeln visar hur du ändrar den [grundläggande skaluppsättningsmall](virtual-machine-scale-sets-mvss-start.md) att distribuera till ett befintligt virtuellt nätverk istället för att skapa en ny.

## <a name="change-the-template-definition"></a>Ändra malldefinitionen

I en [föregående artikel](virtual-machine-scale-sets-mvss-start.md) vi har skapat en grundläggande skalningsuppsättningsmall. Vi kommer nu använda mallen som tidigare och ändra det om du vill skapa en mall som distribuerar en skalningsuppsättning i ett befintligt virtuellt nätverk. 

Lägg först till en `subnetId` parametern. Den här strängen skickas till den konfiguration för skalningsuppsättningen, vilket gör att skaluppsättningen för att identifiera det undernät som har skapats i förväg för att distribuera virtuella datorer i. Den här strängen måste vara i formatet: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Ange till exempel att distribuera skalan i ett befintligt virtuellt nätverk med namnet `myvnet`, undernät `mysubnet`, resursgrupp `myrg`, och prenumeration `00000000-0000-0000-0000-000000000000`, subnetId skulle bli: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

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

Ta sedan bort den virtuella nätverksresursen från den `resources` matris, när du använder ett befintligt virtuellt nätverk och behöver inte distribuera en ny.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
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

Det virtuella nätverket redan innan mallen har distribuerats, så du behöver inte ange en dependsOn-sats från skalningsuppsättningen till det virtuella nätverket. Ta bort följande rader:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Slutligen skickar in den `subnetId` parameter som anges av användaren (istället för att använda `resourceId` för att hämta ID för ett virtuellt nätverk i samma distribution, vilket är vad det grundläggande lönsamma skaluppsättningsmall har).

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
