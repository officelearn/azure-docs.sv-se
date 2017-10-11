---
title: "Referera till en anpassad avbildning i en mall för Azure skala | Microsoft Docs"
description: "Lär dig hur du lägger till en anpassad avbildning i en befintlig mall för Azure Virtual Machine Scale Set"
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
ms.date: 5/10/2017
ms.author: negat
ms.openlocfilehash: cf52fc9e95267c4bc5c0106aadf626685ddd5c24
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Lägg till en anpassad avbildning i en mall för Azure skala

Den här artikeln visar hur du ändrar den [lägsta lönsam skala ange mall](./virtual-machine-scale-sets-mvss-start.md) ska distribueras från anpassad avbildning.

## <a name="change-the-template-definition"></a>Ändra malldefinitionen

Mall för våra lägsta lönsam skala kan ses [här](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), och våra mall för distribution av skalan som från en anpassad avbildning kan ses [här](https://raw.githubusercontent.com/gatneil/mvss/custom-image/azuredeploy.json). Låt oss nu undersöka diff som används för att skapa den här mallen (`git diff minimum-viable-scale-set custom-image`) bit för bit:

### <a name="creating-a-managed-disk-image"></a>Skapa en hanterad avbildning

Om du redan har en anpassad hanterad diskavbildning (en resurs av typen `Microsoft.Compute/images`), och du kan hoppa över det här avsnittet.

Först måste vi lägga till en `sourceImageVhdUri` parametern, som är URI: N till generaliserad blobb i Azure Storage som innehåller den anpassade avbildningen ska distribueras från.


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

Nu ska vi lägga till en resurs av typen `Microsoft.Compute/images`, vilket är den hanterade diskavbildning baserat på generaliserad blob finns på URI `sourceImageVhdUri`. Den här bilden måste vara i samma region som den skaluppsättning som använder den. I egenskaperna för avbildningen kan vi ange OS-typen platsen för blob (från den `sourceImageVhdUri` parametern), och lagringskontotypen:

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

Skalan ange resurs, vi lägga till en `dependsOn` instruktion refererar till den anpassade avbildningen för att kontrollera avbildningen skapas innan du försöker distribuera från den avbildningen skaluppsättning:

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

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Ändra skala ange egenskaper för att använda den hanterade diskavbildning

I den `imageReference` skalans ange `storageProfile`, istället för att ange utgivare, erbjudande, sku och version av en plattformsavbildning anger vi den `id` av den `Microsoft.Compute/images` resursen:

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

I det här exemplet använder vi den `resourceId` funktion för att hämta resurs-ID för den avbildning som har skapats i samma mall. Om du har skapat den hanterade diskavbildning i förväg, ska du i stället ange id för avbildningen. Detta id måste vara i formatet: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Nästa steg

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
