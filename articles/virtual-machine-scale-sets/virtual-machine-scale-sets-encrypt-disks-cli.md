---
title: Kryptera diskar för Azure skalningsuppsättningar med Azure CLI | Microsoft Docs
description: Lär dig hur du använder Azure CLI för att kryptera VM-instanser och anslutna diskar i en Linux VM-skalningsuppsättning
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: cynthn
ms.openlocfilehash: 1264c7e4ebaf5e948e624fa49dc5fb0b4cdb31f0
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869061"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Kryptera OS och anslutna datadiskar i en VM-skalningsuppsättning med Azure CLI

För att skydda och skydda data i vila med branschstandardiserad krypteringsteknik, stöd VM-skalningsuppsättningar för Azure Disk Encryption (ADE). Kryptering kan aktiveras för Linux och Windows VM-skalningsuppsättningar. Mer information finns i [Azure Disk Encryption för Linux och Windows](../security/azure-security-disk-encryption.md).

Azure-diskkryptering stöds:
- för att skala uppsättningar skapas med hanterade diskar och stöds inte för inbyggda (eller ohanterade) disk scale sets.
- för OS- och datavolymer i Windows-skalningsuppsättningar. Inaktivera kryptering stöds för OS- och datavolymer för Windows-skalningsuppsättningar.
- för datavolymer i Linux-skalningsuppsättningar. OS-diskkryptering stöds inte för Linux-skalningsuppsättningar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI version 2.0.31 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning

Innan du kan skapa en skalningsuppsättning skapar du en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Skapa nu en skalningsuppsättning för en virtuell dator med [az vmss create](/cli/azure/vmss). I följande exempel skapas en skalningsuppsättning med namnet *myScaleSet* som är inställd på att uppdateras automatiskt när ändringar tillämpas och genererar SSH-nycklar om de inte finns i *~/.ssh/id_rsa*. En datadisk på 32Gb är kopplade till varje virtuell datorinstans, samt Azure [tillägget för anpassat skript](../virtual-machines/linux/extensions-customscript.md) används för att förbereda datadiskar med [az vmss-tilläggsuppsättningen](/cli/azure/vmss/extension):

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

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Skapa ett Azure key vault aktiverat för diskkryptering

Azure Key Vault kan lagra nycklar, hemligheter eller lösenord så att du kan implementera dem på ett säkert sätt i dina program och tjänster. Kryptografiska nycklar lagras i Azure Key Vault med hjälp av software protection eller kan du importera eller generera dina nycklar i Maskinvarusäkerhetsmoduler (HSM) som är certifierade enligt standarderna FIPS 140-2 nivå 2-standarder. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du kan behålla kontrollen över dessa kryptografiska nycklar och kan granska deras användning.

Definiera dina egna unika *keyvault_name*. Skapa sedan en KeyVault med [az keyvault skapa](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) i samma prenumeration och region som skalning, och den *--aktiverat-för--diskkryptering* princip.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Använd en befintlig Key Vault

Det här steget är bara obligatoriskt om du har ett befintligt Nyckelvalv som du vill använda med diskkryptering. Hoppa över det här steget om du har skapat ett Nyckelvalv i föregående avsnitt.

Definiera dina egna unika *keyvault_name*. Uppdateras ditt KeyVault med [az keyvault update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) och ange den *--aktiverat-för--diskkryptering* princip.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Aktivera kryptering

För att kryptera Virtuella datorinstanser i en skalningsuppsättning, först hämta viss information på resurs-ID för Key Vault med [az keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Dessa variabler används för att starta krypteringsprocessen med [az vmss-kryptering aktiverar](/cli/azure/vmss/encryption#az-vmss-encryption-enable):

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

Det kan ta en minut eller två att starta krypteringsprocessen.

Eftersom skalningsuppsättningen är uppgraderingsprincip på skalningsuppsättningen som skapades i ett tidigare steg har angetts till *automatisk*, VM-instanser automatiskt starta krypteringsprocessen. Scale sets där uppgraderingsprincipen är att manuellt starta krypteringsprincipen på VM-instanser med [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances).

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Aktivera kryptering med hjälp av KEK du omsluter nyckeln

Du kan också använda en krypteringsnyckel för ökad säkerhet när du krypterar virtuella datorns skalningsuppsättning.

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
>  Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng:</br>
/ subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Kontrollera förloppet för kryptering

Du kan kontrollera statusen för diskkryptering genom att använda [az vmss kryptering show](/cli/azure/vmss/encryption#az-vmss-encryption-show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

När VM-instanser är krypterade, statuskoden rapporterar *EncryptionState/krypterad*, vilket visas i följande Exempelutdata:

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

Om du inte längre vill använda krypterade diskar för VM-instanser kan du inaktivera kryptering med [az vmss kryptering inaktivera](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) på följande sätt:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Nästa steg

- I den här artikeln används Azure CLI för att kryptera en VM-skalningsuppsättning. Du kan också använda [Azure PowerShell](virtual-machine-scale-sets-encrypt-disks-ps.md) eller mallar för [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) eller [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
- Om du vill att Azure Disk Encryption tillämpas när ytterligare ett tillägg har etablerats kan du använda [ordningsföljd för](virtual-machine-scale-sets-extension-sequencing.md). Du kan använda [dessa exempel](../security/azure-security-disk-encryption-extension-sequencing.md#sample-azure-templates) att komma igång.
- En slutpunkt till batch-filen exempel på disken för Linux scale set datakryptering finns [här](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). Det här exemplet skapar en resursgrupp, en Linux-skalningsuppsättning, monterar en datadisk på 5 GB och krypterar virtuella datorns skalningsuppsättning.
