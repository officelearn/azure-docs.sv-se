---
title: Konvertera en Azure Resource Manager skala mall för att använda hanterade disk | Microsoft Docs
description: Konvertera en scale set-mall till en mall för hanterade diskar skala.
keywords: Skaluppsättningar för den virtuella datorn
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/18/2017
ms.author: negat
ms.openlocfilehash: 760e30f5c6f4ecaff299bae1725548a6a7c5184c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2017
ms.locfileid: "26781079"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Konvertera en scale set-mall till en mall för hanterade diskar skala

Kunder med en Resource Manager-mall för att skapa en skalningsuppsättning som inte använder hanterade diskar kanske vill ändra det om du vill använda hanterade diskar. Den här artikeln visar hur du använder hanterade diskar, med hjälp av exempelvis en pull-begäran från den [Azure Quickstart mallar](https://github.com/Azure/azure-quickstart-templates), en community-driven lagringsplatsen för Resource Manager exempelmallarna. Fullständig pull-begäran kan visas här: [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998), och de relevanta delarna av diff nedan, tillsammans med förklaringar:

## <a name="making-the-os-disks-managed"></a>Gör OS-diskar som hanteras

I följande diff tas flera variabler som rör egenskaper för lagring och disk bort. Lagringskontotypen inte längre behövs (Standard_LRS är standard), men du kan ange om du vill. Endast Standard_LRS och Premium_LRS stöds med hanterade diskar. Nytt suffix för storage-konto och unika Strängmatrisen sa antal användes i den gamla mallen för att generera lagringskontonamn. Dessa variabler är inte längre behövs i den nya mallen eftersom hanterade diskar automatiskt skapar storage-konton för kundens räkning. På samma sätt vhd behållarens namn och OS-disknamnet är inte längre nödvändigt eftersom hanterade diskar automatiskt namnet underliggande blob-behållare för lagring och diskar.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


I följande diff beräkna du uppdateras API till version 2016-04-30-preview, vilket är den tidigaste versionen som krävs för stöd för hanterade diskar med skaluppsättningar. Du kan använda ohanterade diskar i den nya API-versionen med den gamla syntaxen om du vill. Om du bara uppdatera beräkning API-versionen och inte ändra någonting annat bör mallen fortsätta att fungera som tidigare.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

I följande diff tas konto lagringsresurs bort från matrisen resurser helt. Resursen är inte längre behövs som hanterade diskar skapar dem automatiskt.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

I följande diff kan vi se att vi tar bort den beroende satsen refererar från skalan inställd på loop som skapade storage-konton. I den gamla mallen Detta säkerställer att storage-konton har skapats innan skaluppsättning började skapas, men den här satsen är inte längre nödvändigt med hanterade diskar. Egenskapen vhd-behållare är också ta bort tillsammans med namnegenskapen för OS-disk eftersom dessa egenskaper hanteras automatiskt under huven av hanterade diskar. Du kan lägga till `"managedDisk": { "storageAccountType": "Premium_LRS" }` i konfigurationen ”osDisk” om du vill använda premium OS-diskar. Endast virtuella datorer med en versal eller gemens ' i den virtuella datorn sku använda premiumdiskar.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

Det finns ingen uttrycklig egenskap i skala ange konfiguration för om du vill använda hanterad eller ohanterad disk. Skaluppsättning vet som ska användas baserat på de egenskaper som finns i lagringsprofilen för. Därför är det viktigt när du ändrar mallen så att det är rätt egenskaperna i lagringsprofilen för skaluppsättning.


## <a name="data-disks"></a>Datadiskar

Med ovanstående ändringar scale set använder hanterade diskar för OS-disk, men vad om datadiskar? Lägga till egenskapen ”dataDisks” under ”storageProfile” på samma nivå som ”osDisk” för att lägga till datadiskar. Värdet för egenskapen är en JSON-lista över objekt som har egenskaper ”lun” (som måste vara unikt för varje datadisk på en virtuell dator) ”createOption” (”tom” är för närvarande det enda alternativet som stöds), och ”diskSizeGB” (storleken på disken i gigabyte; måste vara större än 0 och mindre än 1024) som i följande exempel: 

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Om du anger `n` diskar i denna matris varje virtuell dator i skalan ange hämtar `n` datadiskar. Observera dock att dessa datadiskar är raw-enheter. De har inte formaterats. Det är kunden att exponera, partitionera och formatera diskarna innan du använder dem. Du kan också ange `"managedDisk": { "storageAccountType": "Premium_LRS" }` i varje disk dataobjekt för att ange att det ska vara en disk för premium-data. Endast virtuella datorer med en versal eller gemens ' i den virtuella datorn sku använda premiumdiskar.

Mer information om hur du använder datadiskar med skaluppsättningar finns [i den här artikeln](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Nästa steg
Till exempel Resource Manager-mallar med hjälp av skalningsuppsättningar, söka efter ”vmss” i den [github-repo-mallar för Azure Quickstart](https://github.com/Azure/azure-quickstart-templates).

Allmän information finns i [huvudsakliga Landningssida för skalningsuppsättningar](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

