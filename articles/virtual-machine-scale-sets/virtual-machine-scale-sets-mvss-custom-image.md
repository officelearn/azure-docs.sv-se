---
title: Referera till en anpassad avbildning i en Azure skalningsuppsättningsmall | Microsoft Docs
description: Lär dig hur du lägger till en anpassad avbildning i en befintlig mall för Azure Virtual Machine Scale Sets
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
origin.date: 05/10/2017
ms.date: 11/30/2018
ms.author: v-junlch
ms.openlocfilehash: 2e3c8177a32082c251be74e597a18730ae1c9d37
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108385"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Lägga till en anpassad avbildning till en Azure skalningsuppsättningsmall

Den här artikeln visar hur du ändrar den [minsta lönsamma skaluppsättningsmall](./virtual-machine-scale-sets-mvss-start.md) att distribuera från anpassad avbildning.

## <a name="change-the-template-definition"></a>Ändra malldefinitionen

Den minsta lönsamma skalningsuppsättningsmall kan ses [här](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), och mallen för distribution av skalan från en anpassad avbildning kan ses [här](https://raw.githubusercontent.com/gatneil/mvss/custom-image/azuredeploy.json). Låt oss nu undersöka diff som används för att skapa den här mallen (`git diff minimum-viable-scale-set custom-image`) del för del:

### <a name="creating-a-managed-disk-image"></a>Skapa en hanterad disk-avbildning

Om du redan har en anpassad hanterad diskavbildning (en resurs av typen `Microsoft.Compute/images`), och du kan hoppa över det här avsnittet.

Lägg först till en `sourceImageVhdUri` parametern, som är den URI som generaliserad blob i Azure Storage som innehåller den anpassade avbildningen ska distribueras från.


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

Lägg sedan till en resurs av typen `Microsoft.Compute/images`, vilket är den hantera diskavbildningen baserat på generaliserad blob finns på URI `sourceImageVhdUri`. Den här bilden måste vara i samma region som skalningsuppsättningen som använder den. I egenskaperna för avbildningen, anger du OS-typ, platsen för blob (från den `sourceImageVhdUri` parametern), och typ av lagringskonto:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2016-04-30-preview",
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

Ange resurs i skalningsuppsättningen, lägga till en `dependsOn` satsen som refererar till den anpassade avbildningen för att kontrollera att avbildningen skapas innan du försöker distribuera från avbildningen skalningsuppsättningen:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Ändra skala ange egenskaper för att använda hanterade disk image

I den `imageReference` för skalan `storageProfile`, istället för att ange utgivare, erbjudande, sku och version av en plattformsavbildning anger den `id` av den `Microsoft.Compute/images` resursen:

```diff
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
-              "publisher": "Canonical",
-              "offer": "UbuntuServer",
-              "sku": "16.04-LTS",
-              "version": "latest"
+              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

I det här exemplet använder den `resourceId` funktionen för att hämta resurs-ID för den avbildning som skapas i samma mall. Om du har skapat den hantera diskavbildningen i förväg, bör du i stället ange ID för avbildningen. Detta ID måste vara i formatet: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Nästa steg

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

<!-- Update_Description: update metedata properties -->