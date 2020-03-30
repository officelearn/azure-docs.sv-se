---
title: Skapa och kryptera en virtuell Linux-dator med Azure CLI
description: I den här snabbstarten får du lära dig hur du använder Azure CLI för att skapa och kryptera en virtuell Linux-dator
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 1362844de0a6b5d8cee4555c3d24833affe71640
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385154"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>Snabbstart: Skapa och kryptera en Virtuell Linux-dator med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten visar hur du använder Azure CLI för att skapa och kryptera en virtuell Linux-dator (VM).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

Om du väljer att installera och använda Azure CLI lokalt kräver den här snabbstarten att du kör Azure CLI version 2.0.30 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

```azurecli-interactive
az group create --name "myResourceGroup" --location "eastus"
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). I följande exempel skapas en virtuell dator med namnet *myVM*.

```azurecli-interactive
az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
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
> Varje nyckelvalv måste ha ett namn som är unikt i Azure. I exemplen nedan ersätter du <ditt unika nyckelval-namn> med det namn du väljer.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Kryptera den virtuella datorn

Kryptera din virtuella dator med [az vm-kryptering](/cli/azure/vm/encryption?view=azure-cli-latest)och ange ditt unika Key Vault-namn till parametern --disk-encryption-keyvault.

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

Efter ett ögonblick kommer processen att återvända, "Krypteringsbegäran accepterades. Använd kommandot Visa för att övervaka förloppet.". Kommandot "visa" är [az vm show](/cli/azure/vm/encryption#az-vm-encryption-show).

```azurecli-interactive
az vm show --name "myVM" -g "MyResourceGroup"
```

När kryptering är aktiverat visas följande i den returnerade utdata:

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda kommandot [az-gruppborttagning](/cli/azure/group) för att ta bort resursgruppen, den virtuella datorn och nyckelvalvet. 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en virtuell dator, skapade ett Nyckelvalv som var aktiverat för krypteringsnycklar och krypterade den virtuella datorn.  Gå vidare till nästa artikel om du vill veta mer om mer Azure Disk Encryption för virtuella Linux-datorer.

> [!div class="nextstepaction"]
> [Översikt över Azure Disk-kryptering](disk-encryption-overview.md)