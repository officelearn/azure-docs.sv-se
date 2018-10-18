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
ms.openlocfilehash: 0c21ba3df6805c053f3d318d1ca3734a89c2ab60
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400328"
---
# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Med hanterade diskar i Azure Resource Manager-mallar

Det här dokumentet beskriver skillnaderna mellan hanterade och ohanterade diskar när du använder Azure Resource Manager-mallar för att etablera virtuella datorer. Exemplen hjälper dig att uppdatera befintliga mallar som använder ohanterade diskar till hanterade diskar. Referens så vi använder den [101-vm-enkel-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) mallen som en vägledning. Du kan se mallen genom att använda både [hanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) och en tidigare version med hjälp av [ohanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) om du vill jämföra dem direkt.

## <a name="unmanaged-disks-template-formatting"></a>Ohanterade diskar mall formatering

Om du vill börja, låt oss ta en titt på hur ohanterade diskar har distribuerats. När du skapar ohanterade diskar, behöver du ett storage-konto för VHD-filer. Du kan skapa ett nytt lagringskonto eller använda en som redan finns. Den här artikeln visar hur du skapar ett nytt lagringskonto. Skapa en resurs för storage-konto i blocket resurser enligt nedan.

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

I det virtuella datorobjektet, lägger du till ett beroende på lagringskontot för att se till att den har skapats innan den virtuella datorn. I den `storageProfile` avsnittet, ange den fullständiga URI: N för VHD-platsen, som refererar till lagringskontot och krävs för OS-disken och eventuella datadiskar.

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

## <a name="managed-disks-template-formatting"></a>Hanterade diskar mall formatering

Med Azure Managed Disks disken blir en resurs på toppnivå och kräver inte längre ett storage-konto som ska skapas av användaren. Hanterade diskar exponerades först i den `2016-04-30-preview` API-versionen som de är tillgängliga i alla efterföljande API-versioner och nu är typ av disk. I följande avsnitt beskriver standardinställningarna och förklarar vi hur att ytterligare anpassa dina diskar.

> [!NOTE]
> Det rekommenderas att använda en API-version senare än `2016-04-30-preview` eftersom det fanns ändringar mellan `2016-04-30-preview` och `2017-03-30`.
>
>

### <a name="default-managed-disk-settings"></a>Standardinställningar för hanterad disk

Om du vill skapa en virtuell dator med hanterade diskar, behöver du inte längre skapa lagringen konto resurs och uppdatera resursen för virtuella datorer på följande sätt. Observera att särskilt den `apiVersion` återspeglar `2017-03-30` och `osDisk` och `dataDisks` inte längre refererar till en specifik URI för den virtuella Hårddisken. När du distribuerar utan att ange ytterligare egenskaper använder disken en lagringstyp baserat på storleken på den virtuella datorn. Till exempel om du använder en Premium kan VM-storlek (storlekar med ”s” i sina namn, till exempel Standard_D2s_v3) använder sedan system Premium_LRS storage. Använd sku-inställningen på disken för att ange en lagringstyp. Om inget namn anges, tar det formatet `<VMName>_OsDisk_1_<randomstring>` för OS-disken och `<VMName>_disk<#>_<randomstring>` för varje datadisk. Azure-diskkryptering är inaktiverat som standard. cachelagring är skrivbara under OS-disken och inget datadiskar. Du kanske ser i exemplet nedan har fortfarande beroende en storage-konto, men detta gäller endast för lagring av diagnostik och behövs inte för disklagring.

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

### <a name="using-a-top-level-managed-disk-resource"></a>Med hjälp av en översta hanterade diskresursen

Som ett alternativ till att ange diskkonfigurationen i det virtuella datorobjektet, kan du skapa en översta diskresurs och koppla den som en del av skapa en virtuell dator. Du kan till exempel skapa en diskresurs enligt följande för att använda som en datadisk.

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

I det Virtuella datorobjektet, referera till diskobjektet ska anslutas. Ange resurs-ID för hanterad disk som skapats i den `managedDisk` egenskap kan den bifogade filen på disken som den virtuella datorn skapas. Den `apiVersion` för den virtuella datorn resurs är inställt på `2017-03-30`. Ett beroende för diskresursen har lagts till för att se till att den har skapats innan du skapa en virtuell dator. 

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

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Skapa hanterade tillgänglighetsuppsättningar med virtuella datorer med hanterade diskar

För att skapa hanterade tillgänglighetsuppsättningar med virtuella datorer med hanterade diskar, lägga till den `sku` objekt tillgängliga resurser och den `name` egenskap `Aligned`. Den här egenskapen säkerställer att diskarna för varje virtuell dator är tillräckligt isolerade från varandra för att undvika enskilda felpunkter. Observera också att den `apiVersion` för tillgänglighetsuppsättningen resursen är inställd på `2017-03-30`.

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

Nedan visas de parametrar som behövs i Resource Manager-mallen för att skapa Standard SSD-diskar:

* *apiVersion* för Microsoft.Compute måste anges som `2018-04-01` (eller senare)
* Ange *managedDisk.storageAccountType* som `StandardSSD_LRS`

I följande exempel visas den *properties.storageProfile.osDisk* avsnittet för en virtuell dator som använder Standard SSD-diskar:

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

En fullständig mall exempel på hur du skapar en Standard SSD-disk med en mall finns i [skapa en virtuell dator från en Windows-avbildning med Standard SSD-Datadiskar](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>Fler scenarier och anpassningar

För att hitta fullständig information om REST API-specifikationerna, granska de [skapa en hanterad disk REST API-dokumentation](/rest/api/manageddisks/disks/disks-create-or-update). Du hittar fler scenarier som standard och godkända värden som kan skickas till API: ets malldistributioner. 

## <a name="next-steps"></a>Nästa steg

* Besök följande länkar för Azure Quickstart-lagringsplatsen för fullständiga mallar som använder hanterade diskar.
    * [Windows VM med en hanterad disk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Linux VM med en hanterad disk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [Fullständig lista över mallar för hanterad disk](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* Gå till den [Azure översikten över Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md) dokumentet för mer information om hanterade diskar.
* Granska referensdokumentationen för mallar för virtuella datorresurser genom att besöka den [Microsoft.Compute/virtualMachines mallreferensen](/azure/templates/microsoft.compute/virtualmachines) dokumentet.
* Granska referensdokumentationen för mallar för diskresurser genom att besöka den [Microsoft.Compute/disks mallreferensen](/azure/templates/microsoft.compute/disks) dokumentet.
* Information om hur du använder hanterade diskar i Azure VM-skalningsuppsättningar finns i [använda datadiskar med skalningsuppsättningar](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks) dokumentet.
