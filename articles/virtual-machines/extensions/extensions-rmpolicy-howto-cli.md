---
title: Använda Azure för att begränsa VM-tillägget installation | Microsoft Docs
description: Använda Azure för att begränsa VM-tillägget distributioner.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: danis;cynthn
ms.openlocfilehash: 8e65b82730884947633688db9ed50080b96e0b8e
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Använda Azure för att begränsa tillägg installation på virtuella Linux-datorer

Om du vill förhindra användning eller installationen av vissa tillägg på din virtuella Linux-datorer kan du skapa en Azure-princip med hjälp av CLI för att begränsa tillägg för virtuella datorer inom en resursgrupp. 

Den här kursen använder CLI i Azure Cloud gränssnittet uppdateras till den senaste versionen. Om du vill köra Azure CLI lokalt du behöver installera version 2.0.26 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Skapa en regelfil

För att begränsa vilka tillägg som kan installeras, måste du ha en [regeln](/azure/azure-policy/policy-definition#policy-rule) att tillhandahålla logik för att identifiera tillägget.

Det här exemplet visar hur du neka installera tillägg som publicerats av 'Microsoft.OSTCExtensions' genom att skapa en regelfil i Azure Cloud-gränssnittet, men om du arbetar i CLI lokalt, du kan också skapa en lokal fil och ersätter sökväg (~/clouddrive) med sökvägen till den lokala filen på din dator.

I en [bash molnet Shell](https://shell.azure.com/bash), typ:

```azurecli-interactive 
vim ~/clouddrive/azurepolicy.rules.json
```

Kopiera och klistra in följande JSON i filen.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.OSTCExtensions/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/publisher",
                "equals": "Microsoft.OSTCExtensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

När du är klar i **Esc** nyckeln och skriv sedan **: wq** att spara och stäng filen.


## <a name="create-a-parameters-file"></a>Skapa en fil med parametrar

Du måste också en [parametrar](/azure/azure-policy/policy-definition#parameters) -fil som skapar en struktur som du kan använda för att skicka in en lista över tillägg som ska blockeras. 

Det här exemplet illustrerar hur du skapar en fil med parametrar för virtuella Linux-datorer i molnet Shell, men om du arbetar i CLI lokalt, du kan också skapa en lokal fil och ersätter sökväg (~/clouddrive) med sökvägen till den lokala filen på din dator.

I den [bash molnet Shell](https://shell.azure.com/bash), typ:

```azurecli-interactive
vim ~/clouddrive/azurepolicy.parameters.json
```

Kopiera och klistra in följande JSON i filen.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied. Example: CustomScriptForLinux, VMAccessForLinux etc.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

När du är klar i **Esc** nyckeln och skriv sedan **: wq** att spara och stäng filen.

## <a name="create-the-policy"></a>Skapa principen

En principdefinition är ett objekt som används för att lagra den konfiguration som du vill använda. Principdefinitionen använder filerna regler och parametrar för att definiera principen. Skapa princip definition med [az principdefinitionen skapa](/cli/azure/role/assignment?view=azure-cli-latest#az_role_assignment_create).

I det här exemplet är filer som du skapas och lagras som JSON-filer i molnet gränssnittet av regler och parametrar.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>Tilldela principen

Det här exemplet tilldelar principen till en resurs med [az principtilldelningens skapa](/cli/azure/policy/assignment#az_policy_assignment_create). Någon virtuell dator skapas i den **myResourceGroup** resursgruppen kommer inte att kunna installera Linux VM-åtkomst eller tillägg för anpassat skript för Linux. Resursgruppens namn måste finnas innan du kan tilldela principen.

Använd [listan över az](/cli/azure/account?view=azure-cli-latest#az_account_list) att hämta ditt prenumerations-ID som ska användas istället för det i exemplet.


```azurecli-interactive
az policy assignment create \
   --name 'not-allowed-vmextension-linux' \
   --scope /subscriptions/<subscription Id>/resourceGroups/myResourceGroup \
   --policy "not-allowed-vmextension-linux" \
   --params '{
        "notAllowedExtensions": {
            "value": [
                "VMAccessForLinux",
                "CustomScriptForLinux"
            ]
        }
    }'
```

## <a name="test-the-policy"></a>Testa principen

Testa principen genom att skapa en ny virtuell dator och försök att lägga till en ny användare.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Försök att skapa en ny användare med namnet **myNewUser** med VM-åtkomst.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>Tar bort tilldelningen

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>Ta bort principen

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```


## <a name="next-steps"></a>Nästa steg
Mer information finns i [Azure princip](../../azure-policy/azure-policy-introduction.md).
