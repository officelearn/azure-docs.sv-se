---
title: Referera till en anpassad avbildning i en Azure-skalningsuppsättningsmall
description: Lär dig hur du lägger till en anpassad avbildning i en befintlig Azure Virtual Machine Scale Set-mall
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimckitt
ms.openlocfilehash: 3965090239949b5e1116ceebe427728e49ffafe4
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273706"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Lägga till en anpassad avbildning till en skalningsuppsättningsmall i Azure

Den här artikeln visar hur du ändrar den [grundläggande skalningsuppsättningsmallen](virtual-machine-scale-sets-mvss-start.md) för att distribuera från anpassad avbildning.

## <a name="change-the-template-definition"></a>Ändra malldefinitionen
I en [tidigare artikel](virtual-machine-scale-sets-mvss-start.md) hade vi skapat en grundläggande skalningsuppsättningsmall. Vi kommer nu att använda den tidigare mallen och ändra den för att skapa en mall som distribuerar en skalningsuppsättning från en anpassad avbildning.  

### <a name="creating-a-managed-disk-image"></a>Skapa en hanterad diskavbildning

Om du redan har en anpassad hanterad `Microsoft.Compute/images`diskavbildning (en resurs av typen) kan du hoppa över det här avsnittet.

Lägg först `sourceImageVhdUri` till en parameter, som är URI till den generaliserade bloben i Azure Storage som innehåller den anpassade avbildningen att distribuera från.


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

Lägg sedan till en `Microsoft.Compute/images`typresurs , som är den hanterade diskavbildningen `sourceImageVhdUri`baserat på den generaliserade bloben som finns på URI . Den här bilden måste finnas i samma region som skalningsuppsättningen som använder den. I bildens egenskaper anger du os-typen, blobens `sourceImageVhdUri` plats (från parametern) och lagringskontotypen:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2019-03-01",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

I skalningsuppsättningsresursen lägger du till en `dependsOn` sats som refererar till den anpassade avbildningen för att se till att avbildningen skapas innan skalningsuppsättningen försöker distribuera från avbildningen:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Ändra skalningsuppsättningsegenskaper för att använda den hanterade diskavbildningen

`imageReference` I skalan anger `storageProfile`du resursens `id` `Microsoft.Compute/images` i stället för att ange utgivaren, erbjudandet, sku och versionen av en plattformsavbildning:

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

I det här `resourceId` exemplet använder du funktionen för att hämta resurs-ID:t för den bild som skapats i samma mall. Om du har skapat den hanterade diskavbildningen i förväg bör du ange ID för den avbildningen i stället. Detta ID måste vara `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`av samma form: .


## <a name="next-steps"></a>Efterföljande moment

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
