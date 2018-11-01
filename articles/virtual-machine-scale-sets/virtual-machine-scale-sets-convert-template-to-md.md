---
title: Konvertera en skalningsuppsättningsmall för Azure Resource Manager för att använda hanterade disk | Microsoft Docs
description: Konvertera en skalningsuppsättningsmall till en skalningsuppsättningsmall för hanterad disk.
keywords: VM-skalningsuppsättningar
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
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
ms.author: manayar
ms.openlocfilehash: be56fd80229010090216413a7c1833d94e8bac25
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739574"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Konvertera en skalningsuppsättningsmall till en skalningsuppsättningsmall för hanterad disk

Kunder med en Resource Manager-mall för att skapa en skalningsuppsättning som inte använder hanterade diskar kanske vill ändra det om du vill använda hanterade diskar. Den här artikeln visar hur du använder hanterade diskar, som ett exempel med en pull-begäran från den [Azure-Snabbstartsmallar](https://github.com/Azure/azure-quickstart-templates), en community-drivna lagringsplatsen för exemplet Resource Manager-mallar. Fullständig pull-begäran finns här: [ https://github.com/Azure/azure-quickstart-templates/pull/2998 ](https://github.com/Azure/azure-quickstart-templates/pull/2998), och de relevanta delarna av versionens är nedan, tillsammans med förklaringar:

## <a name="making-the-os-disks-managed"></a>Gör OS-diskar som hanteras

Följande versionens tas flera variabler som är relaterade till egenskaper för lagring och disken bort. Typ av lagringskonto inte längre behövs (Standard_LRS är standard), men du kan ange om du vill. Endast Standard_LRS och Premium_LRS stöds med en hanterad disk. Nytt suffix för storage-konto, unika Strängmatrisen och sa antal har använts i den gamla mallen för att generera lagringskontonamn. Dessa variabler är inte längre behövs i den nya mallen eftersom hanterade diskar skapas automatiskt storage-konton för kundens räkning. På samma sätt vhd-behållarnamn och OS-disknamnet är inte längre behövs eftersom hanterade diskar automatiskt namnet underliggande storage blob-behållare och diskar.

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


Följande versionens beräkna du API har uppdaterats till version 2016-04-30-förhandsversion som är den tidigaste versionen som krävs för stöd för hanterade diskar med skalningsuppsättningar. Du kan använda ohanterade diskar i den nya API-versionen med gamla syntax om du vill. Om du bara uppdatera API-version för beräkning och inte ändrar något annat bör mallen fortsätta att fungera som tidigare.

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

Följande versionens tas resursen för lagringskonton bort från matrisen resurser helt. Resursen är inte längre behövs eftersom hanterade diskar skapar dem automatiskt.

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

Följande versionens, kan vi se att vi tar bort den beror på satsen refererar från skalningsuppsättningen att loop som skapade lagringskonton. I den gamla mallen gick det att säkerställa att storage-konton har skapats innan skalningsuppsättningen började skapa, men den här satsen är inte längre nödvändigt med en hanterad disk. Egenskapen vhd-behållare är också tas bort, jämte namnegenskapen för OS-disk som dessa egenskaper hanteras automatiskt under huven av hanterad disk. Du kan lägga till `"managedDisk": { "storageAccountType": "Premium_LRS" }` i konfigurationen av ”osDisk” om du vill ha premium OS-diskar. Endast virtuella datorer med en versal eller gemens ' i den virtuella datorn använda sku premium-diskar.

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

Det finns ingen explicit egenskap i scale set-konfigurationen för om du vill använda hanterad eller ohanterad disk. Skalningsuppsättningen vet som du vill använda utifrån de egenskaper som finns i lagringsprofilen. Det är därför viktigt när du ändrar mallen för att kontrollera att rätt egenskaperna finns i lagringsprofilen för skalningsuppsättningen ändras.


## <a name="data-disks"></a>Datadiskar

Med ovanstående ändringar scale set använder hanterade diskar för Operativsystemets disk, men vad gäller datadiskar? Lägg till egenskapen ”dataDisks” under ”storageProfile” på samma nivå som ”osDisk” för att lägga till datadiskar. Värdet för egenskapen är en JSON-lista med objekt, som har egenskaper ”lun” (som måste vara unikt för varje datadisk på en virtuell dator), ”createOption” (”tom” finns för närvarande det enda alternativet som stöds), och ”diskSizeGB” (storleken på disken i gigabyte; måste vara större än 0 och mindre än 1024) som i följande exempel: 

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Om du anger `n` diskar i den här matrisen varje virtuell dator i skalningsuppsättningen ange hämtar `n` datadiskar. Observera dock att dessa datadiskar är raw-enheter. De har inte formaterats. Det är upp till kunden att exponera, partitionera och formatera diskarna innan du använder dem. Alternativt kan du också ange `"managedDisk": { "storageAccountType": "Premium_LRS" }` i varje disk dataobjekt för att ange att det ska vara en disk för premium-data. Endast virtuella datorer med en versal eller gemens ' i den virtuella datorn använda sku premium-diskar.

Läs mer om att använda datadiskar med skalningsuppsättningar i [i den här artikeln](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Nästa steg
Till exempel Resource Manager-mallar med hjälp av skalningsuppsättningar, söka efter ”vmss” i den [github-lagringsplatsen för Azure-Snabbstartsmallar](https://github.com/Azure/azure-quickstart-templates).

Allmän information finns i [huvudlandningssida för skalningsuppsättningar](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

