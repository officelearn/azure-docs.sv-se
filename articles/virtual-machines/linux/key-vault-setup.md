---
title: Konfigurera Azure Key Vault för virtuella Linux-datorer | Microsoft Docs
description: Konfigurera Key Vault för användning med en Azure Resource Manager virtuell dator med Azure CLI.
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/24/2017
ms.author: kasing
ms.openlocfilehash: cbc8b6be09fcf4232636b580dc0c62482b83bd60
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002163"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Konfigurera Key Vault för virtuella datorer med Azure CLI

I Azure Resource Manager stack modelleras hemligheter/certifikat som resurser som tillhandahålls av Key Vault. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../../key-vault/key-vault-overview.md) För att Key Vault ska kunna användas med Azure Resource Manager virtuella datorer måste egenskapen *EnabledForDeployment* på Key Vault vara inställd på True. Den här artikeln visar hur du konfigurerar Key Vault för användning med Azure Virtual Machines (VM) med hjälp av Azure CLI. 

För att utföra de här stegen måste du ha den senaste versionen av [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggad på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning
Skapa ett nyckel valv och tilldela distributions principen med [AZ-valv skapa](/cli/azure/keyvault). I följande exempel skapas ett nyckel valv med `myKeyVault` namnet `myResourceGroup` i resurs gruppen:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Uppdatera en Key Vault för användning med virtuella datorer
Ange distributions principen för ett befintligt nyckel valv med [AZ-uppdatering](/cli/azure/keyvault)av nyckel valv. Följande uppdaterar nyckel valvet `myKeyVault` `myResourceGroup` som heter i resurs gruppen:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Använd mallar för att konfigurera Key Vault
När du använder en mall måste du ange `enabledForDeployment` egenskapen till `true` för Key Vault resursen enligt följande:

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
För andra alternativ som du kan konfigurera när du skapar en Key Vault med hjälp av mallar, se [skapa ett nyckel valv](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
