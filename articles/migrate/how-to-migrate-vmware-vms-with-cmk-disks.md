---
title: Migrera virtuella VMware-datorer till Azure med serverkryptering(SSE) och kundhanterade nycklar(CMK) med Azure Migrate Server Migration
description: Lär dig hur du migrerar virtuella virtuella datorer med VMware till Azure med serverkryptering(SSE) och kundhanterade nycklar(CMK) med Azure Migrera servermigrering
author: bsiva
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 03/12/2020
ms.author: raynew
ms.openlocfilehash: c6b791fda43a018a26204b2b43dc1e581ff3a945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269489"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>Migrera virtuella virtuella datorer med VMware till virtuella Azure-datorer som är aktiverade med kryptering på serversidan och kundhanterade nycklar

This article describes how to migrate VMware VMs to Azure virtual machines with disks encrypted using server-side encryption(SSE) with customer-managed keys(CMK), using Azure Migrate Server Migration (agentless replication).

Azure Migrate Server Migration portal erfarenhet kan du [migrera virtuella virtuella datorer med VMware till Azure med agentless replikering.](tutorial-migrate-vmware.md) Portalupplevelsen erbjuder för närvarande inte möjligheten att aktivera SSE med CMK för dina replikerade diskar i Azure. Möjligheten att aktivera SSE med CMK för de replikerade diskarna är för närvarande endast tillgänglig via REST API. I den här artikeln får du se hur du skapar och distribuerar en [Azure Resource Manager-mall](../azure-resource-manager/templates/overview.md) för att replikera en virtuell VMware-dator och konfigurera de replikerade diskarna i Azure för att använda SSE med CMK.

Exemplen i den här artikeln använder [Azure PowerShell](/powershell/azure/new-azureps-module-az) för att utföra de uppgifter som behövs för att skapa och distribuera Resource Manager-mallen.

[Läs mer](../virtual-machines/windows/disk-encryption.md) om SSE (Server-side encryption) med kundhanterade nycklar (CMK) för hanterade diskar.

## <a name="prerequisites"></a>Krav

- [Granska självstudien](tutorial-migrate-vmware.md) om migrering av virtuella datorer med VMware till Azure med agentless replication för att förstå verktygskrav.
- [Följ dessa instruktioner](how-to-add-tool-first-time.md) för att skapa ett Azure Migrate-projekt och lägga till **verktyget Azure Migrate: Server Migration** i projektet.
- [Följ dessa instruktioner](how-to-set-up-appliance-vmware.md) för att konfigurera Azure Migrate-enheten för VMware i din lokala miljö och fullständig identifiering.

## <a name="prepare-for-replication"></a>Förbered för replikering

När vm-identifieringen är klar visar raden Identifierade servrar på panelen Servermigrering ett antal virtuella datorer som identifierats av enheten.

Innan du kan börja replikera virtuella datorer måste replikeringsinfrastrukturen förberedas.

1. Skapa en Service Bus-instans i målregionen. Servicebussen används av den lokala Azure Migrate-enheten för att kommunicera med tjänsten Servermigrering för att samordna replikering och migrering.
2. Skapa ett lagringskonto för överföring av åtgärdsloggar från replikering.
3. Skapa ett lagringskonto som Azure Migrate-installationen överför replikeringsdata till.
4. Skapa ett nyckelvalv och konfigurera Nyckelvalvet för att hantera signaturtoken för delad åtkomst för blob-åtkomst på lagringskonton som skapats i steg 3 och 4.
5. Generera en signaturtoken för delad åtkomst för servicebussen som skapats i steg 1 och skapa en hemlighet för token i Nyckelvalvet som skapades i föregående steg.
6. Skapa en nyckelvalvsprincip för att ge den lokala Azure Migrate-enheten (med hjälp av appen AAD- och servermigreringstjänsten åtkomst till Key Vault.
7. Skapa en replikeringsprincip och konfigurera tjänsten Servermigrering med information om replikeringsinfrastrukturen som skapades i föregående steg.

Replikeringsinfrastrukturen måste skapas i regionen Target Azure för migreringen och i den Azure-prenumeration som de virtuella datorerna migreras till.

Servermigreringsportalens upplevelse förenklar förberedelsen av replikeringsinfrastrukturen genom att automatiskt göra detta åt dig när du replikerar en virtuell dator för första gången i ett projekt. I den här artikeln antar vi att du redan har replikerat en eller flera virtuella datorer med hjälp av portalupplevelsen och att replikeringsinfrastrukturen redan har skapats. Vi ska titta på hur du identifierar information om den befintliga replikeringsinfrastrukturen och hur du använder dessa information som indata till Resource Manager-mallen som ska användas för att konfigurera replikering med CMK.

### <a name="identifying-replication-infrastructure-components"></a>Identifiera komponenter i replikeringsinfrastruktur

1. På Azure-portalen går du till sidan resursgrupper och väljer resursgruppen där Azure Migrate-projektet skapades.
2. Välj **Distributioner** från den vänstra menyn och sök efter ett distributionsnamn som börjar med strängen *"Microsoft.MigrateV2.VMwareV2EnableMigrate".* Du ser en lista över Resource Manager-mallar som skapats av portalupplevelsen för att konfigurera replikering för virtuella datorer i det här projektet. Vi hämtar en sådan mall och använder den som bas för att förbereda mallen för replikering med CMK.
3. Om du vill hämta mallen väljer du en distribution som matchar strängmönstret i föregående steg > väljer **Mall** på den vänstra menyn > Klicka på **Hämta** på den övre menyn. Spara filen template.json lokalt. Du redigerar den här mallfilen i det sista steget.

## <a name="create-a-disk-encryption-set"></a>Skapa en diskkrypteringsuppsättning

Ett diskkrypteringsobjekt mappar hanterade diskar till ett nyckelvalv som innehåller CMK som ska användas för SSE. Om du vill replikera virtuella datorer med CMK skapar du en diskkrypteringsuppsättning och skickar den som en indata till replikeringsåtgärden.

Följ exemplet [här](../virtual-machines/windows/disk-encryption.md#powershell) för att skapa en diskkrypteringsuppsättning med Azure PowerShell. Kontrollera att diskkrypteringsuppsättningen skapas i målprenumerationen som virtuella datorer migreras till och i azure-regionen för målet för migreringen.

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>Få information om den virtuella datorn för VMware att migrera

I det här steget använder du Azure PowerShell för att få information om den virtuella datorn som måste migreras. Dessa uppgifter används för att skapa Resource Manager-mallen för replikering. Närmare bestämt är de två fastigheter av intresse:

- Datorns resurs-ID för de identifierade virtuella datorerna.
- Listan över diskar för den virtuella datorn och deras diskidentifierare.

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

Kopiera värdet för Den SiteId-sträng som motsvarar Azure Migrate-enheten som den virtuella datorn identifieras via. I exemplet ovan är SiteId *"/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"*

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

Kopiera ResourceId-värdena, namn- och disk uuid-värdena för den dator som ska migreras.
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>Skapa resurshanterarens mall för replikering

- Öppna mallfilen för Resource Manager som du hämtade i steget **Identifiera replikeringsinfrastrukturkomponenter** i en valfri redigerare.
- Ta bort alla resursdefinitioner från mallen utom resurser av typen *"Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems"*
- Om det finns flera resursdefinitioner av ovanstående typ tar du bort alla utom en. Ta bort eventuella **berorPå** egenskapsdefinitioner från resursdefinitionen.
- I slutet av det här steget bör du ha en fil som ser ut som exemplet nedan och har samma uppsättning egenskaper.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- Redigera **name** namnegenskapen i resursdefinitionen. Ersätt strängen som följer den senaste "/" i egenskapen name med värdet *$machine. Namn*( från föregående steg).
- Ändra värdet för egenskapen **properties.providerSpecificDetails.vmwareMachineId** med värdet *$machine. ResourceId*(från föregående steg).
- Ange värdena för **targetResourceGroupId**, **targetNetworkId**, **targetSubnetName** till målresursgrupp-ID, målresurs-ID för virtuella nätverk och målundernätsnamn.
- Ange värdet för **licenseType** till "WindowsServer" för att tillämpa Azure Hybrid Benefit för den här virtuella datorn. Om den här virtuella datorn inte är berättigad till Azure Hybrid Benefit anger du värdet för **licenseType** to NoLicenseType.
- Ändra värdet för egenskapen **targetVmName** till önskat Azure-namn för virtuella datorer för den migrerade virtuella datorn.
- Du kan också lägga till en egenskap med namnet **targetVmSize** under egenskapen **targetVmName.** Ange värdet för egenskapen **targetVmSize** till önskad Azure-storlek för den migrerade virtuella datorn.
- Egenskapen **disksToInclude** är en lista över diskindata för replikering med varje listobjekt som representerar en lokal disk. Skapa lika många listobjekt som antalet diskar på den lokala virtuella datorn. Ersätt **egenskapen diskId** i listobjektet till uuid för de diskar som identifierades i föregående steg. Ange **värdet isOSDisk** till "true" för OS-disken på den virtuella datorn och "false" för alla andra diskar. Lämna egenskaperna **logStorageAccountId** och **logStorageAccountSasSecretName** oförändrade. Ange **diskType-värdet** till typen Azure Managed Disk (*Standard_LRS, Premium_LRS, StandardSSD_LRS*) som ska användas för disken. För diskarna som behöver krypteras med CMK lägger du till en egenskap med namnet **diskEncryptionSetId** och anger värdet till resurs-ID för den diskkrypteringsuppsättning som skapats(**$des. Id**) i steget *Skapa en diskkrypteringsuppsättning*
- Spara den redigerade mallfilen. I exemplet ovan ser den redigerade mallfilen ut på följande sätt:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>Konfigurera replikering

Du kan nu distribuera den redigerade Resource Manager-mallen till projektresursgruppen för att ställa in replikering för den virtuella datorn. Lär dig hur du [distribuerar resurser med Azure Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>Nästa steg

[Övervaka replikeringsstatus](tutorial-migrate-vmware.md#track-and-monitor) via portalupplevelsen och utför Testmigreringar och migrering.
