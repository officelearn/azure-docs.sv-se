---
title: Referera till ett befintligt virtuellt nätverk i en Azure-skalningsuppsättningsmall
description: Lär dig hur du lägger till ett virtuellt nätverk i en befintlig Azure Virtual Machine Scale Set-mall
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimckitt
ms.openlocfilehash: 83328a31dad8009c28e146c81b24d6d9244f88a8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273672"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Lägga till referens till ett befintligt virtuellt nätverk i en Azure-skalningsuppsättningsmall

Den här artikeln visar hur du ändrar den [grundläggande skalningsuppsättningsmallen](virtual-machine-scale-sets-mvss-start.md) för att distribuera till ett befintligt virtuellt nätverk i stället för att skapa ett nytt.

## <a name="change-the-template-definition"></a>Ändra malldefinitionen

I en [tidigare artikel](virtual-machine-scale-sets-mvss-start.md) hade vi skapat en grundläggande skalningsuppsättningsmall. Vi kommer nu att använda den tidigare mallen och ändra den för att skapa en mall som distribuerar en skala som till ett befintligt virtuellt nätverk. 

Lägg först `subnetId` till en parameter. Den här strängen skickas in i skalningsuppsättningskonfigurationen, vilket gör att skalningsuppsättningen för att identifiera det förskapade undernätet för att distribuera virtuella datorer till. Den här strängen måste vara av formen:`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Om du till exempel vill distribuera skalningsuppsättningen `mysubnet`till ett `myrg`befintligt `00000000-0000-0000-0000-000000000000`virtuellt nätverk med `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`namn, `myvnet`undernät , resursgrupp och prenumeration , skulle undernätverket vara: .

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

Ta sedan bort den virtuella `resources` nätverksresursen från matrisen, eftersom du använder ett befintligt virtuellt nätverk och inte behöver distribuera en ny.

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

Det virtuella nätverket finns redan innan mallen distribueras, så det finns ingen anledning att ange en dependsOn-sats från skalan inställd på det virtuella nätverket. Ta bort följande rader:

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

Slutligen, passera `subnetId` i parametern som anges `resourceId` av användaren (i stället för att använda för att få ID för ett vnet i samma distribution, vilket är vad den grundläggande livskraftiga skala uppsättning mallen gör).

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
