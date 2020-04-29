---
title: Referera till ett befintligt virtuellt nätverk i en Azure Scale set-mall
description: Lär dig hur du lägger till ett virtuellt nätverk i en befintlig mall för skalnings uppsättningar för virtuella Azure-datorer
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimckitt
ms.openlocfilehash: 83328a31dad8009c28e146c81b24d6d9244f88a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273672"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Lägg till en referens till ett befintligt virtuellt nätverk i en mall för Azure-skalnings uppsättning

Den här artikeln visar hur du ändrar den [grundläggande skalnings uppsättnings mal len](virtual-machine-scale-sets-mvss-start.md) så att den distribueras till ett befintligt virtuellt nätverk i stället för att skapa en ny.

## <a name="change-the-template-definition"></a>Ändra mal len definition

I en [föregående artikel](virtual-machine-scale-sets-mvss-start.md) har vi skapat en grundläggande mall för skalnings uppsättningar. Vi kommer nu att använda den tidigare mallen och ändra den för att skapa en mall som distribuerar en skalnings uppsättning i ett befintligt virtuellt nätverk. 

Lägg först till en `subnetId` parameter. Den här strängen skickas till konfigurationen för skalnings uppsättningar, så att skalnings uppsättningen kan identifiera det förskapade under nätet för att distribuera virtuella datorer till. Strängen måste ha formatet:`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Om du till exempel vill distribuera skalnings uppsättningen till ett befintligt virtuellt nätverk med `myvnet`namn, `mysubnet`undernät, resurs `myrg`grupp och prenumeration `00000000-0000-0000-0000-000000000000`skulle subnetId vara: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

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

Ta sedan bort den virtuella nätverks resursen från `resources` matrisen, när du använder ett befintligt virtuellt nätverk och inte behöver distribuera ett nytt.

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

Det virtuella nätverket finns redan innan mallen distribueras, så det finns inget behov av att ange en dependsOn-sats från skalnings uppsättningen till det virtuella nätverket. Ta bort följande rader:

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

Skicka slutligen i den `subnetId` parameter som angetts av användaren (i stället för `resourceId` att hämta ID: t för ett VNet i samma distribution, vilket är den grundläggande mallen för den grundläggande skalnings uppsättningen.).

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
