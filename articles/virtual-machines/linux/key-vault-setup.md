---
title: "Konfigurera Azure Key Vault för virtuella Linux-datorer | Microsoft Docs"
description: "Hur du ställer in Key Vault för användning med en virtuell dator i Azure Resource Manager med CLI 2.0."
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
ms.openlocfilehash: 2cc9b4c978e9a4deb0c8443c4b0f9e301a7cf492
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli-20"></a>Hur du ställer in Key Vault för virtuella datorer med Azure CLI 2.0

I Azure Resource Manager-stacken modelleras hemligheter/certifikat som resurser som tillhandahålls av Key Vault. Mer information om Azure Key Vault finns [vad är Azure Key Vault?](../../key-vault/key-vault-whatis.md) För Key Vault som ska användas med Azure Resource Manager VMs den *EnabledForDeployment* egenskapen i Nyckelvalvet måste anges till true. Den här artikeln visar hur du ställer in Key Vault för användning med virtuella Azure-datorer (VM) med hjälp av Azure CLI 2.0. Du kan också utföra dessa steg med [Azure CLI 1.0](key-vault-setup-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Om du vill utföra dessa steg behöver du senast [Azure CLI 2.0](/cli/azure/install-az-cli2) installerad och inloggad till en Azure-konto med hjälp av [az inloggningen](/cli/azure/#login).

## <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning
Skapa en nyckelvalvet och tilldela princip för programdistribution med [az keyvault skapa](/cli/azure/keyvault#create). I följande exempel skapas ett nyckelvalv med namnet `myKeyVault` i den `myResourceGroup` resursgrupp:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Uppdatera ett Nyckelvalv för användning med virtuella datorer
Ange princip för programdistribution på en befintlig nyckel valvet med [az keyvault uppdatering](/cli/azure/keyvault#update). Följande uppdateringar nyckelvalvet med namnet `myKeyVault` i den `myResourceGroup` resursgrupp:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Använda mallar för att ställa in Key Vault
När du använder en mall måste du ange den `enabledForDeployment` egenskapen `true` för nyckeln valvet resurs på följande sätt:

```json
{
    "type": "Microsoft.KeyVault/vaults",
    "name": "ContosoKeyVault",
    "apiVersion": "2015-06-01",
    "location": "<location-of-key-vault>",
    "properties": {
    "enabledForDeployment": "true",
    ....
    ....
    }
}
```

## <a name="next-steps"></a>Nästa steg
Andra alternativ som du kan konfigurera när du skapar ett Nyckelvalv med hjälp av mallar, se [skapa ett nyckelvalv](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
