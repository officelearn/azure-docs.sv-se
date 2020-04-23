---
title: Skapa och kryptera en virtuell Windows-dator med Azure CLI
description: I den här snabbstarten får du lära dig hur du använder Azure CLI för att skapa och kryptera en virtuell Windows-dator
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: aa0dd0bf55d51800d6a9b4283aa9e653887bf2f1
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082806"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>Snabbstart: Skapa och kryptera en Windows-virtuell dator med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten visar hur du använder Azure CLI för att skapa och kryptera en virtuell dator (Windows Server 2016) (VM).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda Azure CLI lokalt kräver den här snabbstarten att du kör Azure CLI version 2.0.30 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create). I följande exempel skapas en virtuell dator med namnet *myVM*. Det här exemplet använder *azureuser* som administrativt användarnamn och *myPassword12* som lösenord.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
```

Det tar några minuter att skapa den virtuella datorn och stödresurser. Utdataresultatet i följande exempel anger att den virtuella datorn har skapats.

```
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

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Skapa ett nyckelvalv som konfigurerats för krypteringsnycklar

Azure-diskkryptering lagrar sin krypteringsnyckel i ett Azure Key Vault. Skapa ett nyckelvalv med [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create). Om du vill aktivera Nyckelvalvet för att lagra krypteringsnycklar använder du parametern --enabled-for-disk-encryption.
> [!Important]
> Varje Key Vault måste ha ett unikt namn. I följande exempel skapas ett nyckelvalv med namnet *myKV*, men du måste namnge ditt något annat.

```azurecli-interactive
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Kryptera den virtuella datorn

Kryptera din virtuella dator med [az vm-kryptering](/cli/azure/vm/encryption?view=azure-cli-latest)och ange ditt unika Key Vault-namn till parametern --disk-encryption-keyvault.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

Du kan kontrollera att kryptering är aktiverat på din virtuella dator med [az vm show](/cli/azure/vm/encryption#az-vm-encryption-show)

```azurecli-interactive
az vm show --name MyVM -g MyResourceGroup
```

Följande visas i den returnerade utdata:

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda kommandot [az-gruppborttagning](/cli/azure/group) för att ta bort resursgruppen, den virtuella datorn och nyckelvalvet.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en virtuell dator, skapade ett Nyckelvalv som var aktiverat för krypteringsnycklar och krypterade den virtuella datorn.  Gå vidare till nästa artikel om du vill veta mer om Azure Disk Encryption-förhandskrav för virtuella IaaS-datorer.

> [!div class="nextstepaction"]
> [Översikt över Azure Disk-kryptering](disk-encryption-overview.md)
