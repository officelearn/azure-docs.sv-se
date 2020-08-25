---
title: Azure PowerShell – aktivera kryptering från slut punkt till slut punkt på VM-värden
description: Så här aktiverar du kryptering från slut punkt till slut punkt för dina virtuella Azure-datorer med kryptering på värden.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 6a352ecc2d2b02f03e2b55f7c5896ac905077921
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814802"
---
# <a name="use-the-azure-powershell-module-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Använd Azure PowerShell-modulen för att aktivera kryptering från slut punkt till slut punkt med kryptering på värden

När du aktiverar kryptering på värden krypteras data som lagras på den virtuella dator värden i vila och flöden krypteras till lagrings tjänsten. För konceptuell information om kryptering på värden och andra typer av hanterade disk krypterings typer, se [kryptering på värd-till-slutpunkt-kryptering för dina VM-data](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Regioner som stöds

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>VM-storlekar som stöds

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Du kan också hitta VM-storlekar program mässigt. Information om hur du hämtar dem program mässigt finns i avsnittet [hitta stödda VM-storlekar](#finding-supported-vm-sizes) .

## <a name="prerequisites"></a>Förutsättningar

För att kunna använda kryptering på värden för dina virtuella datorer eller skalnings uppsättningar för virtuella datorer måste du få funktionen aktive rad i din prenumeration. Skicka ett e-postmeddelande till encryptionAtHost@microsoft . com med dina prenumerations-ID: n för att få funktionen aktive rad för dina prenumerationer.

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Skapa en Azure Key Vault-och DiskEncryptionSet

När funktionen är aktive rad måste du konfigurera en Azure Key Vault och en DiskEncryptionSet, om du inte redan gjort det.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-powershell](../../../includes/virtual-machines-disks-encryption-create-key-vault-powershell.md)]

## <a name="enable-encryption-at-host-for-disks-attached-to-vm-and-virtual-machine-scale-sets"></a>Aktivera kryptering på värd för diskar som är anslutna till VM och skalnings uppsättningar för virtuella datorer

Du kan aktivera kryptering på värden genom att ange en ny egenskap EncryptionAtHost under securityProfile för virtuella datorer eller skalnings uppsättningar för virtuella datorer med hjälp av API-version **2020-06-01** och senare.

`"securityProfile": { "encryptionAtHost": "true" }`

## <a name="example-scripts"></a>Exempel skript

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-customer-managed-keys"></a>Aktivera kryptering på värd för diskar som är anslutna till en virtuell dator med Kundhanterade nycklar

Skapa en virtuell dator med hanterade diskar med hjälp av resurs-URI för den DiskEncryptionSet som skapades tidigare.

Ersätt,,,,, `<yourPassword>` `<yourVMName>` `<yourVMSize>` `<yourDESName>` `<yoursubscriptionID>` `<yourResourceGroupName>` och `<yourRegion>` kör sedan skriptet.

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithCMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-diskEncryptionSetId "/subscriptions/<yoursubscriptionID>/resourceGroups/<yourResourceGroupName>/providers/Microsoft.Compute/diskEncryptionSets/<yourDESName>" `
-region "<yourRegion>"
```

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-platform-managed-keys"></a>Aktivera kryptering på värd för diskar som är anslutna till en virtuell dator med plattforms hanterade nycklar

Ersätt `<yourPassword>` ,,, `<yourVMName>` `<yourVMSize>` `<yourResourceGroupName>` och `<yourRegion>` kör sedan skriptet.

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithPMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-region "<yourRegion>"
```

## <a name="finding-supported-vm-sizes"></a>Söker efter VM-storlekar som stöds

Äldre VM-storlekar stöds inte. Du hittar listan över virtuella dator storlekar som stöds genom att antingen:

Anropar [resurs-SKU API](/rest/api/compute/resourceskus/list) och kontrollerar att `EncryptionAtHostSupported` funktionen har angetts till **Sant**.

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

Eller, anropar PowerShell-cmdleten [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku?view=azps-3.8.0) .

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat och konfigurerat dessa resurser kan du använda dem för att skydda dina hanterade diskar. Följande länk innehåller exempel skript, var och en med respektive scenario, som du kan använda för att skydda dina hanterade diskar.

[Azure Resource Manager-mallexempel](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
