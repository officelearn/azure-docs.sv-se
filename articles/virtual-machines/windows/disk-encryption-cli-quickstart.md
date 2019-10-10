---
title: Skapa och kryptera en virtuell Windows-dator med Azure CLI
description: I den här snabb starten får du lära dig hur du använder Azure CLI för att skapa och kryptera en virtuell Windows-dator
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 9bbe74bd2f3137443b4e239201c604d9de52582e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72246069"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>Snabb start: skapa och kryptera en virtuell Windows-dator med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabb starten visar hur du använder Azure CLI för att skapa och kryptera en virtuell Windows Server 2016-dator (VM).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.30 eller senare under den här snabbstarten. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create). Följande exempel skapar en virtuell dator med namnet *myVM*. Det här exemplet använder *azureuser* som administrativt användarnamn och *myPassword12* som lösenord. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
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

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Skapa en Key Vault konfigurerad för krypterings nycklar

Azure Disk Encryption lagrar sin krypterings nyckel i en Azure Key Vault. Skapa en Key Vault med [AZ-valv skapa](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create). Om du vill aktivera Key Vault att lagra krypterings nycklar använder du parametern--enabled-for-Disk-Encryption.
> [!Important]
> Varje Key Vault måste ha ett unikt namn. I följande exempel skapas en Key Vault med namnet *myKV*, men du måste namnge något annat.

```azurecli
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Kryptera den virtuella datorn

Kryptera din virtuella dator med [AZ VM-kryptering](/cli/azure/vm/encryption?view=azure-cli-latest)och ange ditt unika Key Vault namn till parametern--Disk-Encryption-nyckel.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

Du kan kontrol lera att kryptering är aktiverat på den virtuella datorn med [AZ VM show](/cli/azure/vm/encryption#az-vm-encryption-show)

```azurecli-interactive
az vm show --name MyVM -g MyResourceGroup
```

Följande visas i utdata som returneras:

```azurecli-interactive
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group) för att ta bort resurs gruppen, den virtuella datorn och Key Vault. 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en virtuell dator, skapade en Key Vault som aktiverades för krypterings nycklar och krypterade den virtuella datorn.  Gå vidare till nästa artikel om du vill veta mer om Azure Disk Encryption-förhandskrav för virtuella IaaS-datorer.

> [!div class="nextstepaction"]
> [Översikt över Azure Disk Encryption](disk-encryption-overview.md)

