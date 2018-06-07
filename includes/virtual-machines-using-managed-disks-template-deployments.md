---
title: ta med fil
description: ta med fil
services: storage
author: jboeshart
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: jaboes
ms.custom: include file
ms.openlocfilehash: f363c52093e0ae8314e07e750eb4362f71e9a4b1
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34826522"
---
# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Med hjälp av hanterade diskar i Azure Resource Manager-mallar

Det här dokumentet går igenom skillnader mellan hanterade och ohanterade diskar när du använder Azure Resource Manager-mallar för att etablera virtuella datorer. Exemplen hjälpa dig att uppdatera befintliga mallar som använder ohanterade diskar till hanterade diskar. För referens anger vi använder den [101-vm-enkel-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) mallen som en vägledning. Du kan se mallen använder både [hanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) och en tidigare version med hjälp av [ohanterad diskar](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) om du vill jämföra dem direkt.

## <a name="unmanaged-disks-template-formatting"></a>Ohanterad diskar mallen formatering

Om du vill börja, ska vi ta en titt på hur ohanterade diskar har distribuerats. När du skapar en ohanterad diskar, behöver ett lagringskonto för VHD-filer. Du kan skapa ett nytt lagringskonto eller använda en som redan finns. Den här artikeln visar hur du skapar ett nytt lagringskonto. Skapa en resurs för storage-konto i blocket resurser som visas nedan.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2016-01-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

I det virtuella datorobjektet, lägger du till ett beroende på lagringskontot för att se till att den har skapats innan den virtuella datorn. I den `storageProfile` avsnitt, anger du den fullständiga URI för VHD-platsen som refererar till lagringskontot och behövs för OS-disken och eventuella hårddiskar.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Hanterade diskar mallen formatering

Med Azure hanterade diskar disken blir en översta resurs och kräver inte längre ett lagringskonto skapas av användaren. Hanterade diskar har först exponeras i den `2016-04-30-preview` API-versionen som de är tillgängliga i alla efterföljande API-versioner och nu är standard disktyp. I följande avsnitt igenom standardinställningarna och innehåller information om hur du anpassar ytterligare diskar.

> [!NOTE]
> Det rekommenderas att använda en API-version senare än `2016-04-30-preview` eftersom viktiga förändringar mellan `2016-04-30-preview` och `2017-03-30`.
>
>

### <a name="default-managed-disk-settings"></a>Standardinställningarna för hanterade diskar

Om du vill skapa en virtuell dator med hanterade diskar, behöver du inte längre skapa lagring kontot resurs och uppdatera din virtuella datorresursen enligt följande. Observera att särskilt den `apiVersion` återspeglar `2017-03-30` och `osDisk` och `dataDisks` inte längre referera till en specifik URI för den virtuella Hårddisken. När du distribuerar utan att ange ytterligare egenskaper som använder disken [Standard LRS lagring](../articles/storage/common/storage-redundancy.md). Om inget namn anges tar format `<VMName>_OsDisk_1_<randomstring>` för OS-disken och `<VMName>_disk<#>_<randomstring>` för varje datadisk. Azure disk encryption är inaktiverat som standard. cachelagring är läsning och skrivning för OS-disken och ingen för datadiskar. Du kan se i exemplet nedan har fortfarande ett beroende som storage-konto, även om detta är endast avsedd för lagring av diagnostik och behövs inte för disklagring.

```json
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>Med hjälp av en översta hanterade diskresurs

Du kan skapa en översta diskresurs och bifoga den som en del av den virtuella dator skapandet som ett alternativ till att ange diskkonfigurationen i det virtuella datorobjektet. Du kan till exempel skapa en diskresurs enligt följande för att använda som en datadisk.

```json
{
    "type": "Microsoft.Compute/disks",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "apiVersion": "2017-03-30",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

I det Virtuella datorobjektet, referera till diskobjektet ska anslutas. Ange resurs-ID för hanterade diskar som skapats i den `managedDisk` egenskapen kan den bifogade filen på disken som den virtuella datorn skapas. Den `apiVersion` för den virtuella datorn resursen har angetts till `2017-03-30`. Ett beroende för diskresursen läggs till se till att den har skapats innan du skapa en virtuell dator. 

```json
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Skapa hanterade tillgänglighetsuppsättningar med virtuella datorer med hjälp av hanterade diskar

För att skapa hanterade tillgänglighetsuppsättningar med virtuella datorer med hjälp av hanterade diskar, lägga till den `sku` objekt tillgängligheten resursen och den `name` egenskapen `Aligned`. Den här egenskapen garanterar att diskar för varje virtuell dator är tillräckligt isolerade från varandra för att undvika enskilda felpunkter. Observera att den `apiVersion` för resursen tillgänglighetsuppsättning är inställd på `2017-03-30`.

```json
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/availabilitySets",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="standard-ssd-disks"></a>Standard SSD-diskar

Nedan visas de parametrar som behövs i Resource Manager-mall för att skapa Standard SSD-diskar:

* *apiVersion* för Microsoft.Compute måste anges som `2018-04-01` (eller senare)
* Ange *managedDisk.storageAccountType* som `StandardSSD_LRS`

I följande exempel visas den *properties.storageProfile.osDisk* för en virtuell dator som använder Standard SSD-diskar:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

En fullständig mall exempel på hur du skapar en Standard SSD-disk med en mall finns i [skapa en virtuell dator från en Windows-avbildning med Datadiskar som Standard SSD](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>Fler scenarier och anpassningar

Om du vill hitta fullständig information om REST API-specifikationerna, granska den [skapar du en hanterad disk REST API-dokumentation](/rest/api/manageddisks/disks/disks-create-or-update). Du hittar fler scenarier, såväl som standard och godkända värden som ska skickas till API: N via mall-distributioner. 

## <a name="next-steps"></a>Nästa steg

* Fullständig mallar som använder hanterade diskar finns på följande länkar för Azure Quickstart lagringsplatsen.
    * [Windows virtuell dator med hanterad disk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Linux VM med hanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [Fullständig lista över mallar för hanterade diskar](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* Besök den [hanterade diskar översikt över Azure](../articles/virtual-machines/windows/managed-disks-overview.md) dokumentet för mer information om hanterade diskar.
* Granska referensdokumentationen mall för virtuella datorresurser genom att besöka den [Microsoft.Compute/virtualMachines mallreferensen](/azure/templates/microsoft.compute/virtualmachines) dokumentet.
* Granska referensdokumentationen mall för diskresurser genom att besöka den [Microsoft.Compute/disks mallreferensen](/azure/templates/microsoft.compute/disks) dokumentet.
* Information om hur du använder hanterade diskar i skalningsuppsättningar i virtuella Azure-datorn finns i [använder datadiskar med skalningsuppsättningar](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md) dokumentet.
