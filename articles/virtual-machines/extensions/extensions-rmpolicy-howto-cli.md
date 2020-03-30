---
title: Använda Azure Policy för att begränsa installationen av vm-tillägg
description: Använd Azure Policy för att begränsa vm-tilläggsdistributioner.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 3c660f7e05af43c2aad6f7283e32cfc1d85571ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066836"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Använd Azure Policy för att begränsa tilläggsinstallationen på virtuella Linux-datorer

Om du vill förhindra användning eller installation av vissa tillägg på dina virtuella Linux-datorer kan du skapa en Azure-princip med HJÄLP av CLI för att begränsa tillägg för virtuella datorer inom en resursgrupp. 

Den här självstudien använder CLI i Azure Cloud Shell, som ständigt uppdateras till den senaste versionen. Om du vill köra Azure CLI lokalt måste du installera version 2.0.26 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Skapa en regelfil

För att begränsa vilka tillägg som kan installeras måste du ha en [regel](../../governance/policy/concepts/definition-structure.md#policy-rule) för att ange logiken för att identifiera tillägget.

I det här exemplet visas hur du nekar installation av tillägg som publicerats av Microsoft.OSTCExtensions genom att skapa en regelfil i Azure Cloud Shell, men om du arbetar i CLI lokalt kan du också skapa en lokal fil och ersätta sökvägen (~/clouddrive) med sökvägen till den lokala filen på datorn.

I en [bash Cloud Shell](https://shell.azure.com/bash)skriver du:

```bash
vim ~/clouddrive/azurepolicy.rules.json
```

Kopiera och klistra in följande .json i filen.

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

När du är klar trycker du på **Esc-tangenten** och skriver sedan **:wq** för att spara och stänga filen.


## <a name="create-a-parameters-file"></a>Skapa en parameterfil

Du behöver också en [parameterfil](../../governance/policy/concepts/definition-structure.md#parameters) som skapar en struktur som du kan använda för att skicka i en lista över de tillägg som ska blockeras. 

Det här exemplet visar hur du skapar en parameterfil för virtuella Linux-datorer i Cloud Shell, men om du arbetar i CLI lokalt kan du också skapa en lokal fil och ersätta sökvägen (~/clouddrive) med sökvägen till den lokala filen på datorn.

Skriv: [bash Cloud Shell](https://shell.azure.com/bash)

```bash
vim ~/clouddrive/azurepolicy.parameters.json
```

Kopiera och klistra in följande .json i filen.

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

När du är klar trycker du på **Esc-tangenten** och skriver sedan **:wq** för att spara och stänga filen.

## <a name="create-the-policy"></a>Skapa principen

En principdefinition är ett objekt som används för att lagra den konfiguration som du vill använda. Principdefinitionen använder regel- och parameterfilerna för att definiera principen. Skapa principdefinitionen med [az-principdefinition skapa](/cli/azure/role/assignment?view=azure-cli-latest).

I det här exemplet är reglerna och parametrarna de filer som du har skapat och lagrat som .json-filer i molngränssnittet.

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

I det här exemplet tilldelas principen till en resursgrupp med hjälp av [az-principtilldelningsskapande](/cli/azure/policy/assignment). Alla virtuella datorer som skapas i resursgruppen **myResourceGroup** kommer inte att kunna installera Linux VM Access eller Custom Script-tilläggen för Linux. Resursgruppen måste finnas innan du kan tilldela principen.

Använd [az-kontolistan](/cli/azure/account?view=azure-cli-latest) för att få ditt prenumerations-ID att använda i stället för det i exemplet.


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

Testa principen genom att skapa en ny virtuell dator och försöka lägga till en ny användare.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Försök att skapa en ny användare med namnet **myNewUser** med hjälp av vm Access-tillägget.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>Ta bort tilldelningen

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>Ta bort principen

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Azure Policy](../../governance/policy/overview.md).