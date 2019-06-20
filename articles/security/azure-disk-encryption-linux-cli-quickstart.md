---
title: Skapa och kryptera en Linux VM med Azure CLI
description: I den här snabbstarten får du lära dig hur du använder Azure CLI för att skapa och kryptera en virtuell Linux-dator
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 3272c18e3f1851b45efdfac05fc0e8471865fa09
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275228"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>Snabbstart: Skapa och kryptera en Linux VM med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten visar hur du använder Azure CLI för att skapa och kryptera en Linux-dator (VM).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.30 eller senare under den här snabbstarten. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med [az vm create](https://docs.microsoft.com/en-us/cli/azure/vm?view=azure-cli-latest#az-vm-create). Följande exempel skapar en virtuell dator med namnet *myVM*.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --size Standard_D2S_V3 \
```

Det tar några minuter att skapa den virtuella datorn och stödresurser. Utdataresultatet i följande exempel anger att den virtuella datorn har skapats.

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Skapa ett Nyckelvalv som konfigurerats för krypteringsnycklar

Azure-diskkryptering lagrar dess krypteringsnyckeln i ett Azure Key Vault. Skapa ett Key Vault med [az keyvault skapa](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create). Om du vill aktivera Key Vault för att lagra krypteringsnycklar, Använd parametern--aktiverat-för--diskkryptering.

> [!Important]
> För varje Key Vault måste ha ett unikt namn. I följande exempel skapas ett Key Vault med namnet *myKV*, men du måste namnge din något annat.

```azurecli
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Kryptera den virtuella datorn

Kryptera din virtuella dator med [az vm encryption](/cli/azure/vm/encryption?view=azure-cli-latest), vilket ger unika Key Vault namn om du vill--disk-kryptering-keyvault-parametern.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

Efter en stund processen returnerar ”kryptering begäran accepterades. Använd Visa kommando för att övervaka förloppet ”. Kommandot ”Visa” är [az vm show](/cli/azure/vm/encryption#az-vm-encryption-show).

```azurecli-interactive
az vm show --name MyVM -g MyResourceGroup
```

När kryptering är aktiverat visas följande i den returnerade utdatan:

```azurecli-interactive
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behövs kan du använda den [az group delete](/cli/azure/group) till att ta bort resursgruppen, virtuell dator och Key Vault. 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en virtuell dator och skapat ett Nyckelvalv som har aktivera för krypteringsnycklar och krypteras den virtuella datorn.  Gå vidare till nästa artikel om du vill veta mer om Azure Disk Encryption-förhandskrav för virtuella IaaS-datorer.

> [!div class="nextstepaction"]
> [Krav för Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)
