---
title: Konfigurera Azure Key Vault för virtuella Linux-datorer | Microsoft Docs
description: Hur du ställer in Key Vault för användning med en Azure Resource Manager-dator med Azure CLI.
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
ms.openlocfilehash: c38d901be627d9ad2f18ebe708c7a1fcaa63cc15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613918"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Hur du ställer in Key Vault för virtuella datorer med Azure CLI

I Azure Resource Manager-stacken modelleras hemligheter/certifikat som resurser som tillhandahålls av Key Vault. Läs mer om Azure Key Vault i [vad är Azure Key Vault?](../../key-vault/key-vault-whatis.md) För Key Vault som ska användas med virtuella Azure Resource Manager-datorer i *EnabledForDeployment* egenskapen Key Vault måste anges till true. Den här artikeln visar hur du ställer in Key Vault för användning med Azure-datorer (VM) med hjälp av Azure CLI. 

Om du vill utföra dessa steg du behöver senast [Azure CLI](/cli/azure/install-az-cli2) installerat och loggat in till en Azure-konto med hjälp av [az-inloggning](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning
Skapa ett nyckelvalv och tilldela princip för programdistribution med [az keyvault skapa](/cli/azure/keyvault). I följande exempel skapas ett nyckelvalv med namnet `myKeyVault` i den `myResourceGroup` resursgrupp:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Uppdatera ett Nyckelvalv för användning med virtuella datorer
Ange princip för programdistribution på en befintlig nyckel nyckelvalv med [az keyvault update](/cli/azure/keyvault). Följande uppdateringar i key vault med namnet `myKeyVault` i den `myResourceGroup` resursgrupp:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Använd mallar för att konfigurera Key Vault
När du använder en mall kan du behöva ange den `enabledForDeployment` egenskap `true` för nyckeln valvet resurs på följande sätt:

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
Andra alternativ som du kan konfigurera när du skapar ett Nyckelvalv med hjälp av mallar finns i [skapa key vault](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
