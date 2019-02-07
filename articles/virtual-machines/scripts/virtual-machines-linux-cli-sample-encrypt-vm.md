---
title: Skriptexempel för Azure CLI – Kryptera en virtuell Linux-dator | Microsoft Docs
description: Skriptexempel för Azure CLI – Kryptera en virtuell Linux-dator
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 8f3e032d6723faf149faad039e3c3faff3e6d83a
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731503"
---
# <a name="encrypt-a-linux-virtual-machine-in-azure"></a>Kryptera en virtuell Linux-dator i Azure

Det här skriptet skapar ett säkert Azure Key Vault, krypteringsnycklar, Azure Active Directory-tjänstens huvudnamn och en virtuell Linux-dator (VM). Den virtuella datorn krypteras sedan med hjälp av krypteringsnyckeln från Key Vault och autentiseringsuppgifterna för tjänstens huvudnamn.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/encrypt-disks/encrypt_vm.sh "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, Azure Key Vault, tjänstens huvudnamn, en virtuell dator och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault) | Skapar ett Azure Key Vault för lagring av säkra data, till exempel krypteringsnycklar. |
| [az keyvault key create](https://docs.microsoft.com/cli/azure/keyvault/key) | Skapar en krypteringsnyckel i Key Vault. |
| [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp) | Skapar ett huvudnamn för Azure Active Directory-tjänsten för att autentisera och kontrollera åtkomst till krypteringsnycklarna på ett säkert sätt. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault) | Anger behörigheter för Key Vault så att tjänstens huvudnamn får åtkomst till krypteringsnycklarna. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Skapar den virtuella datorn och ansluter den till nätverkskortet, ett virtuellt nätverk, ett undernät och en NSG. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter.  |
| [az vm encryption enable](https://docs.microsoft.com/cli/azure/vm/encryption) | Aktiverar kryptering på en virtuell dator med hjälp av autentiseringsuppgifterna för tjänstens huvudnamn och krypteringsnyckeln. |
| [az vm encryption show](https://docs.microsoft.com/cli/azure/vm/encryption) | Visar status för krypteringsprocessen för den virtuella datorn. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Linux-datorer](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
