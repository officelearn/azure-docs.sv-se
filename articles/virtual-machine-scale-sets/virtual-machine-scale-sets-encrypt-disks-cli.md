---
title: Kryptera diskar för Azure skalningsuppsättningarna med Azure CLI | Microsoft Docs
description: Lär dig hur du använder Azure CLI 2.0 för att kryptera VM-instanser och anslutna diskar i en skaluppsättning för Linux virtuella dator
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: iainfou
ms.openlocfilehash: 22d3c763317def137b4e0beb155f28585d7c6ae1
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli-20-preview"></a>Kryptera OS och anslutna diskar i en virtuell dator-skala med Azure CLI 2.0 (förhandsgranskning)

Om du vill skydda och skydda data i viloläge med hjälp industry standard krypteringsteknik stöder skalningsuppsättningar i virtuella datorer Azure Disk Encryption (ADE). Kryptering kan aktiveras för Linux och Windows virtuella skala anger. Mer information finns i [Azure Disk Encryption för Linux och Windows](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  Azure disk encryption för skalningsuppsättningar i virtuella datorer är för närvarande i förhandsversion, tillgänglig i alla offentliga Azure-regioner.

Azure disk encryption stöds:
- för att skala anger skapas med hanterade diskar och stöds inte för inbyggda (eller ohanterad) disk skaluppsättningar.
- för OS- och datavolymer i skalningsuppsättningar i Windows. Inaktivera kryptering stöds av Operativsystemet och datavolymer för skalningsuppsättningar i Windows.
- för datavolymer i skalningsuppsättningar i Linux. OS-diskkryptering stöds inte i den aktuella förhandsvisningen för skalningsuppsättningar i Linux.

Ange VM avbildningsåterställning och uppgradering skalningsåtgärder stöds inte i den aktuella förhandsvisningen. Azure-diskkryptering för virtuella skala anger preview rekommenderas endast i testmiljöer. I förhandsgranskningen gör inte diskkryptering i produktionsmiljöer där du kan behöva uppgradera en OS-avbildning i en skaluppsättning för krypterade.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="register-for-disk-encryption-preview"></a>Registrera dig för förhandsgranskning för kryptering av disk

Azure disk encryption för den virtuella datorn anger preview måste du registrera prenumerationen med [az funktionen registrera](/cli/azure/feature#az_feature_register). Du behöver bara utför följande steg första gången du använder funktionen disk encryption förhandsgranskning:

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

Det kan ta upp till 10 minuter för att sprida registrering förfrågan. Du kan kontrollera registreringstillstånd med [az funktionen Visa](/cli/azure/feature#az_feature_show). När den `State` rapporter *registrerade*, registrera den *Mirosoft.Compute* provider med [az providern registrera](/cli/azure/provider#az_provider_register):

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning

Innan du kan skapa en skalningsuppsättning skapar du en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Skapa nu en skalningsuppsättning för en virtuell dator med [az vmss create](/cli/azure/vmss#az_vmss_create). I följande exempel skapas en skalningsuppsättning med namnet *myScaleSet* som är inställd på att uppdateras automatiskt när ändringar tillämpas och genererar SSH-nycklar om de inte finns i *~/.ssh/id_rsa*. En disk på 32Gb data är kopplade till varje VM-instans och Azure [tillägget för anpassat skript](../virtual-machines/linux/extensions-customscript.md) används för att förbereda datadiskar med [az vmss tillägget set](/cli/azure/vmss/extension#az_vmss_extension_set):

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

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Skapa ett Azure nyckelvalv aktiverad för diskkryptering av

Azure Key Vault kan lagra nycklar, hemligheter eller lösenord som gör det möjligt att implementera dem på ett säkert sätt i dina program och tjänster. Kryptografiska nycklar lagras i Azure Key Vault med skydd av programvara eller du kan importera eller generera dina nycklar i Maskinvarusäkerhetsmoduler (HSM) certifierade FIPS 140-2 level 2-standarder. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du behålla kontrollen över dessa kryptografiska nycklar och granska deras användning.

Definiera dina egna unika *keyvault_name*. Skapa sedan en KeyVault med [az keyvault skapa](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) i samma prenumeration och region som skala och den *--aktiverat-för--diskkryptering* princip.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Använd en befintlig Key Vault

Det här steget är bara obligatoriskt om du har en befintlig Nyckelvalv som du vill använda med diskkryptering. Hoppa över det här steget om du har skapat ett Nyckelvalv i föregående avsnitt.

Definiera dina egna unika *keyvault_name*. Sedan uppdatera din KeyVault med [az keyvault uppdatering](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) och ange den *--aktiverat-för--diskkryptering* princip.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Aktivera kryptering

Om du vill kryptera VM-instanser i en skaluppsättning först hämta viss information om resurs-ID för Nyckelvalvet med [az keyvault visa](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Dessa variabler används för att starta krypteringsprocessen med [az vmss kryptering aktivera](/cli/azure/vmss/encryption#az-vmss-encryption-enable):

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

Eftersom skaluppsättning princip för versionsuppgradering på skalan som angetts i tidigare har angetts till *automatisk*, VM-instanser startar automatiskt krypteringsprocessen. Skaluppsättningar där principen för versionsuppgradering är manuell, starta krypteringsprincipen på VM-instanser med [az vmss update-instanser](/cli/azure/vmss#az-vmss-update-instances).

## <a name="check-encryption-progress"></a>Kontrollera kryptering pågår

Om du vill kontrollera statusen för diskkryptering [az vmss kryptering visa](/cli/azure/vmss/encryption#az-vmss-encryption-show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

När VM-instanser som är krypterade statuskoden rapporterar *EncryptionState/krypterade*som visas i följande exempel utdata:

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

Om du inte längre vill använda krypterade VM-instanser diskar kan du inaktivera kryptering med [az vmss kryptering inaktivera](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) på följande sätt:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln används Azure CLI 2.0 för att kryptera en skaluppsättning för virtuell dator. Du kan också använda [Azure PowerShell](virtual-machine-scale-sets-encrypt-disks-ps.md) eller mallar för [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) eller [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).

Ett exempel på slutpunkt till slutpunkt batch för disken för Linux scale set datakryptering hittar [här](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). Det här exemplet skapar en resursgrupp, Linux skaluppsättning monterar en datadisk med 5 GB och krypterar skaluppsättning för virtuell dator.