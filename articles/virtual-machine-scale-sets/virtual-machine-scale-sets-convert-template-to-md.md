---
title: Konvertera en skalningsuppsättningsmall för användning av hanterad disk
description: Konvertera en azure Resource Manager-skalningsuppsättningsmall till en mall för hanterad diskskalauppsättning.
keywords: VM-skalningsuppsättningar
author: mayanknayar
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 5/18/2017
ms.author: manayar
ms.openlocfilehash: 4ab5c48c6673a2353c70fe808d09aa15675e0424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278118"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Konvertera en skalningsuppsättningsmall till en mall för hanterad diskskalauppsättning

Kunder med en Resource Manager-mall för att skapa en skalningsuppsättning som inte använder hanterad disk kanske vill ändra den för att använda hanterad disk. Den här artikeln visar hur du använder hanterade diskar, med hjälp av en pull-begäran från [Azure Quickstart-mallar](https://github.com/Azure/azure-quickstart-templates)som ett exempel på Resource Manager-mallar som exempel används. Den fullständiga pull begäran kan [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998)ses här: , och de relevanta delarna av diff är nedan, tillsammans med förklaringar:

## <a name="making-the-os-disks-managed"></a>Göra OS-diskarna hanterade

I följande diff tas flera variabler relaterade till lagringskonto och diskegenskaper bort. Lagringskontotyp är inte längre nödvändig (Standard_LRS är standard), men du kan ange den om så önskas. Endast Standard_LRS och Premium_LRS stöds med hanterad disk. Nytt suffix för lagringskonto, unik strängmatris och sa-antal användes i den gamla mallen för att generera lagringskontonamn. Dessa variabler är inte längre nödvändiga i den nya mallen eftersom hanterad disk automatiskt skapar lagringskonton för kundens räkning. På samma sätt är vhd-behållarnamn och OS-disknamn inte längre nödvändiga eftersom den hanterade disken automatiskt namnger de underliggande lagringsblobbbehållarna och diskarna.

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


I följande diff uppdateras du API till version 2016-04-30-preview, vilket är den tidigaste versionen som krävs för hanterat diskstöd med skalningsuppsättningar. Du kan använda ohanterad diskar i den nya API-versionen med den gamla syntaxen om så önskas. Om du bara uppdaterar beräknings-API-versionen och inte ändrar något annat, bör mallen fortsätta att fungera som tidigare.

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

I följande diff tas lagringskontoresursen bort från resursmatrisen helt. Resursen behövs inte längre eftersom den hanterade disken skapar dem automatiskt.

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

I följande diff kan vi se att vi tar bort beror på klausulen som refererar från skalan inställd på loopen som skapade lagringskonton. I den gamla mallen var detta att säkerställa att lagringskontona skapades innan skalningsuppsättningen började skapas, men den här satsen är inte längre nödvändig med hanterad disk. Egenskapen vhd containers tas också bort, tillsammans med egenskapen OS-disknamn eftersom dessa egenskaper automatiskt hanteras under huven av hanterad disk. Du kan `"managedDisk": { "storageAccountType": "Premium_LRS" }` lägga till i "osDisk" konfiguration om du ville premium OS diskar. Endast virtuella datorer med versaler eller gemener 's' i VM sku kan använda premiumdiskar.

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

Det finns ingen explicit egenskap i skalningsuppsättningskonfigurationen för om hanterad eller ohanterad disk ska användas. Skalningsuppsättningen vet vilken som ska användas baserat på de egenskaper som finns i lagringsprofilen. Därför är det viktigt när du ändrar mallen för att säkerställa att rätt egenskaper finns i lagringsprofilen för skalningsuppsättningen.


## <a name="data-disks"></a>Datadiskar

Med ändringarna ovan använder skalningsuppsättningen hanterade diskar för OS-disken, men hur är det med datadiskar? Om du vill lägga till datadiskar lägger du till egenskapen "dataDisks" under "storageProfile" på samma nivå som "osDisk". Värdet för egenskapen är en JSON-lista med objekt, som var och en har egenskaper "lun" (som måste vara unika per datadisk på en virtuell dator), "createOption" ("tom" är för närvarande det enda alternativet som stöds), och "diskSizeGB" (storleken på disken i gigabyte; måste vara större än 0 och mindre än 1024) som i följande exempel:

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Om du `n` anger diskar i den här matrisen hämtar `n` varje virtuell dator i skalningsuppsättningen datadiskar. Observera dock att dessa datadiskar är råa enheter. De är inte formaterade. Det är upp till kunden att bifoga, partitionera och formatera diskarna innan du använder dem. Du kan också ange `"managedDisk": { "storageAccountType": "Premium_LRS" }` i varje datadiskobjekt för att ange att det ska vara en premiumdatadisk. Endast virtuella datorer med versaler eller gemener 's' i VM sku kan använda premiumdiskar.

Mer information om hur du använder datadiskar med skalningsuppsättningar finns i [den här artikeln](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Nästa steg
Till exempel Resource Manager-mallar med skalningsuppsättningar, sök efter "vmss" i [Azure Quickstart Templates GitHub repo](https://github.com/Azure/azure-quickstart-templates).

Allmän information finns på [huvudmålsidan för skalningsuppsättningar](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

