---
title: Kryptera diskar för Azure-skalningsuppsättningar med Azure CLI
description: Lär dig hur du använder Azure PowerShell för att kryptera VM-instanser och anslutna diskar i en windows-skalningsuppsättning för virtuella datorer
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mbaldwin
ms.openlocfilehash: 557d5c023acbc7987d58c9e78bfe11e25f314879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279083"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Kryptera operativsystem och anslutna datadiskar i en skalningsuppsättning för virtuella datorer med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten visar hur du använder Azure CLI för att skapa och kryptera en skalningsuppsättning för virtuella datorer. Mer information om hur du använder Azure Disk-kryptering på en skalningsuppsättning för virtuella datorer finns i [Azure Disk Encryption for Virtual Machine Scale Sets](disk-encryption-overview.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning

Innan du kan skapa en skalningsuppsättning skapar du en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Skapa nu en skalningsuppsättning för en virtuell dator med [az vmss create](/cli/azure/vmss). I följande exempel skapas en skalningsuppsättning med namnet *myScaleSet* som är inställd på att uppdateras automatiskt när ändringar tillämpas och genererar SSH-nycklar om de inte finns i *~/.ssh/id_rsa*. En 32 Gb-datadisk är kopplad till varje VM-instans och Azure [Custom Script Extension](../virtual-machines/linux/extensions-customscript.md) används för att förbereda datadiskarna med az [vmss-tilläggsuppsättning:](/cli/azure/vmss/extension)

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Skapa ett Azure-nyckelvalv aktiverat för diskkryptering

Azure Key Vault kan lagra nycklar, hemligheter eller lösenord som gör att du kan implementera dem på ett säkert sätt i dina program och tjänster. Kryptografiska nycklar lagras i Azure Key Vault med programvaruskydd, eller så kan du importera eller generera dina nycklar i HSM (Hardware Security Modules) som är certifierade enligt FIPS 140-2-standarder. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du behåller kontrollen över dessa kryptografiska nycklar och kan granska deras användning.

Definiera din egen unika *keyvault_name*. Skapa sedan en KeyVault med [az keyvault skapa](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) i samma prenumeration och region som skalningsuppsättningen och ange *principen --enabled-for-disk-kryptering.*

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Använda ett befintligt nyckelvalv

Det här steget krävs bara om du har ett befintligt Nyckelvalv som du vill använda med diskkryptering. Hoppa över det här steget om du har skapat ett nyckelvalv i föregående avsnitt.

Definiera din egen unika *keyvault_name*. Sedan uppdaterade du keyvault-uppdateringen med [az keyvault](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) och anger principen *--enabled-for-disk-encryption.*

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Aktivera kryptering

Om du vill kryptera VM-instanser i en skalningsuppsättning får du först information om resurs-ID:et för Key Vault med [az keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Dessa variabler används för att sedan starta krypteringsprocessen med [az vmss kryptering aktivera:](/cli/azure/vmss/encryption#az-vmss-encryption-enable)

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

Det kan ta en minut eller två för krypteringsprocessen att starta.

Eftersom skalningsprincipen är uppgraderingsprincipen för den skalningsuppsättning som skapats i ett tidigare steg är inställd *på automatisk*startar VM-instanserna krypteringsprocessen automatiskt. Starta krypteringsprincipen på den virtuella datorn-instanser med [az vmss update-instances på skalningsuppsättningar](/cli/azure/vmss#az-vmss-update-instances)där uppgraderingsprincipen ska vara manuell.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Aktivera kryptering med KEK för att slå in nyckeln

Du kan också använda en nyckelkrypteringsnyckel för ökad säkerhet när du krypterar skaluppsättningen för den virtuella datorn.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  Syntaxen för värdet för parametern disk-encryption-keyvault är den fullständiga identifierarsträngen:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> Syntaxen för värdet för parametern key-encryption-key är den fullständiga URI:n till KEK som i:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] https://

## <a name="check-encryption-progress"></a>Kontrollera krypteringsstatus

Om du vill kontrollera status för diskkryptering använder du [az vmss krypteringsvisning:](/cli/azure/vmss/encryption#az-vmss-encryption-show)

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

När VM-instanser krypteras rapporterar statuskoden *EncryptionState/encrypted,* vilket visas i följande exempelutdata:

```bash
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Inaktivera kryptering

Om du inte längre vill använda diskar med krypterade VM-instanser kan du inaktivera kryptering med [az vmss-kryptering inaktivera](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) enligt följande:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Nästa steg

- I den här artikeln använde du Azure CLI för att kryptera en skalningsuppsättning för virtuella datorer. Du kan också använda [Azure PowerShell-](disk-encryption-powershell.md) eller [Azure Resource Manager-mallar](disk-encryption-azure-resource-manager.md).
- Om du vill att Azure Disk Encryption ska tillämpas när ett annat tillägg har etablerats kan du använda [tilläggssekvensering](virtual-machine-scale-sets-extension-sequencing.md). 
- Ett heltäckande kommandofilexempel för linuxskalauppsättningsdiskkryptering finns [här](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). I det här exemplet skapas en resursgrupp, Linux-skaluppsättning, en datadisk på 5 GB och den virtuella datorns skaluppsättning krypteras.
