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
ms.openlocfilehash: 126b488d2bb59e2904bee646301240efe6fe71a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76037946"
---
Det här dokumentet går igenom skillnaderna mellan hanterade och ohanterade diskar när du använder Azure Resource Manager-mallar för att etablera virtuella datorer. Exemplen hjälper dig att uppdatera befintliga mallar som använder ohanterade diskar till hanterade diskar. Som referens använder vi mallen [101-vm-simple-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) som en guide. Du kan se mallen med både [hanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) och en tidigare version med [ohanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) om du vill jämföra dem direkt.

## <a name="unmanaged-disks-template-formatting"></a>Formhanterat diskmallformatering

Till att börja med ska vi ta en titt på hur ohanterad diskar distribueras. När du skapar ohanterat diskar behöver du ett lagringskonto för att kunna lagra VHD-filerna. Du kan skapa ett nytt lagringskonto eller använda ett som redan finns. Den här artikeln visar hur du skapar ett nytt lagringskonto. Skapa en lagringskontoresurs i resursblocket enligt nedan.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

Lägg till ett beroende av lagringskontot i objektet för den virtuella datorn för att säkerställa att det skapas före den virtuella datorn. I `storageProfile` avsnittet anger du den fullständiga URI-platsen för VHD-platsen, som refererar till lagringskontot och som behövs för OS-disken och eventuella datadiskar.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
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

## <a name="managed-disks-template-formatting"></a>Formatering av mallar för hanterade diskar

Med Azure Managed Disks blir disken en resurs på den högsta nivån och kräver inte längre ett lagringskonto som ska skapas av användaren. Hanterade diskar exponerades `2016-04-30-preview` först i API-versionen, de är tillgängliga i alla efterföljande API-versioner och är nu standarddisktypen. I följande avsnitt går du igenom standardinställningarna och beskriver hur du anpassar diskarna ytterligare.

> [!NOTE]
> Vi rekommenderar att du använder `2016-04-30-preview` en API-version `2016-04-30-preview` senare `2017-03-30`än när ändringar mellan och .
>
>

### <a name="default-managed-disk-settings"></a>Standardinställningar för hanterade disk

Om du vill skapa en virtuell dator med hanterade diskar behöver du inte längre skapa lagringskontoresursen. Med hänvisning till mallexemplet nedan finns det vissa skillnader från de tidigare ohanterade diskexemplen för att notera:

- Det `apiVersion` är en version som stöder hanterade diskar.
- `osDisk`och `dataDisks` inte längre hänvisa till en specifik URI för den virtuella hårddisken.
- När du distribuerar utan att ange ytterligare egenskaper använder disken en lagringstyp baserat på storleken på den virtuella datorn. Om du till exempel använder en vm-storlek som stöder premiumlagring (storlekar med "s" i namnet, till exempel Standard_D2s_v3) konfigureras premiumdiskar som standard. Du kan ändra detta genom att använda diskens sku-inställning för att ange en lagringstyp.
- Om inget namn på disken anges sker `<VMName>_OsDisk_1_<randomstring>` formatet för OS-disken och `<VMName>_disk<#>_<randomstring>` för varje datadisk.
  - Om en virtuell dator skapas från en anpassad avbildning hämtas standardinställningarna för lagringskontotyp och disknamn från diskegenskaperna som definierats i den anpassade avbildningsresursen. Dessa kan åsidosättas genom att ange värden för dessa i mallen.
- Som standard är Azure-diskkryptering inaktiverad.
- Som standard är diskcachening Läs/Skriv för OS-disken och Ingen för datadiskar.
- I exemplet nedan finns det fortfarande ett beroende av lagringskonto, men detta är bara för lagring av diagnostik och behövs inte för disklagring.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
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

### <a name="using-a-top-level-managed-disk-resource"></a>Använda en hanterad diskresurs på den högsta nivån

Som ett alternativ till att ange diskkonfigurationen i objektet för den virtuella datorn kan du skapa en diskresurs på den översta nivån och bifoga den som en del av skapandet av den virtuella datorn. Du kan till exempel skapa en diskresurs på följande sätt som ska användas som en datadisk.

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2018-06-01",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
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

I VM-objektet refererar du till det diskobjekt som ska kopplas. Om du anger resurs-ID:et `managedDisk` för den hanterade disken som skapats i egenskapen kan den bifogade filen till disken när den virtuella datorn skapas. För `apiVersion` vm-resursen är `2017-03-30`inställd på . Ett beroende av diskresursen läggs till för att säkerställa att den skapas innan den virtuella datorn skapas. 

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
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

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Skapa uppsättningar med hanterad tillgänglighet med virtuella datorer med hanterade diskar

Om du vill skapa hanterade tillgänglighetsuppsättningar med `sku` virtuella datorer med hanterade diskar lägger du till objektet i tillgänglighetsuppsättningsresursen och anger egenskapen `name` till `Aligned`. Den här egenskapen säkerställer att diskarna för varje virtuell dator är tillräckligt isolerade från varandra för att undvika enskilda felpunkter. Observera också `apiVersion` att resursen för tillgänglighetsuppsättningen är inställd på `2018-10-01`.

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2018-10-01",
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

### <a name="standard-ssd-disks"></a>Standard-SSD-diskar

Nedan finns de parametrar som behövs i resource manager-mallen för att skapa Standard SSD-diskar:

* *apiVersion* för Microsoft.Compute måste `2018-04-01` anges som (eller senare)
* Ange *managedDisk.storageAccountType* som`StandardSSD_LRS`

I följande exempel visas avsnittet *properties.storageProfile.osDisk* för en virtuell dator som använder Standard SSD-diskar:

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

Ett fullständigt mallexempel på hur du skapar en Standard SSD-disk med en mall finns i [Skapa en virtuell dator från en Windows-avbildning med standard-SSD-datadiskar](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>Ytterligare scenarier och anpassningar

Om du vill hitta fullständig information om REST API-specifikationerna läser du [dokumentationen för att skapa en hanterad REST API](/rest/api/manageddisks/disks/disks-create-or-update)för disk . Du hittar ytterligare scenarier samt standardvärden och godtagbara värden som kan skickas till API:et via malldistributioner. 

## <a name="next-steps"></a>Nästa steg

* För fullständiga mallar som använder hanterade diskar besök följande Azure Quickstart Repo-länkar.
    * [Windows VM med hanterad disk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Linux VM med hanterad disk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* Besök [Azure Managed Disks Overview-dokumentet](../articles/virtual-machines/windows/managed-disks-overview.md) om du vill veta mer om hanterade diskar.
* Granska mallreferensdokumentationen för virtuella datorresurser genom att besöka [mallreferensdokumentet för Microsoft.Compute/virtualMachines.](/azure/templates/microsoft.compute/virtualmachines)
* Granska mallreferensdokumentationen för diskresurser genom att besöka [referensdokumentet för Microsoft.Compute/disks-mallen.](/azure/templates/microsoft.compute/disks)
* Information om hur du använder hanterade diskar i Azure-skalningsuppsättningar för virtuella datorer finns i dokumentet [Använd datadiskar med skaluppsättningar.](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks)
