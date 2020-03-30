---
title: Använda Azure Policy för att begränsa installationen av vm-tillägg
description: Använd Azure Policy för att begränsa tilläggsdistributioner.
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
ms.openlocfilehash: 428db340ce43463939ce71ffadd4188060f3e732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073116"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Använda Azure Policy för att begränsa installationen av tillägg på virtuella datorer i Windows

Om du vill förhindra användning eller installation av vissa tillägg på dina virtuella Windows-datorer kan du skapa en Azure-princip med PowerShell för att begränsa tillägg för virtuella datorer inom en resursgrupp. 

Den här självstudien använder Azure PowerShell i Cloud Shell, som ständigt uppdateras till den senaste versionen. 

 

## <a name="create-a-rules-file"></a>Skapa en regelfil

För att begränsa vilka tillägg som kan installeras måste du ha en [regel](../../governance/policy/concepts/definition-structure.md#policy-rule) för att ange logiken för att identifiera tillägget.

I det här exemplet visas hur du nekar tillägg som publiceras av "Microsoft.Compute" genom att skapa en regelfil i Azure Cloud Shell, men om du arbetar i PowerShell lokalt kan du också skapa en lokal fil och ersätta sökvägen ($home/clouddrive) med sökvägen till den lokala filen på datorn.

I ett [molnskal](https://shell.azure.com/powershell)skriver du:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Kopiera och klistra in följande .json i filen.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

När du är klar trycker du på **Ctrl + O** och **sedan enter** för att spara filen. Tryck **på Ctrl + X** för att stänga filen och avsluta.

## <a name="create-a-parameters-file"></a>Skapa en parameterfil

Du behöver också en [parameterfil](../../governance/policy/concepts/definition-structure.md#parameters) som skapar en struktur som du kan använda för att skicka i en lista över de tillägg som ska blockeras. 

I det här exemplet visas hur du skapar en parameterfil för virtuella datorer i Cloud Shell, men om du arbetar i PowerShell lokalt kan du också skapa en lokal fil och ersätta sökvägen ($home/clouddrive) med sökvägen till den lokala filen på datorn.

I [Cloud Shell](https://shell.azure.com/powershell)skriver du:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Kopiera och klistra in följande .json i filen.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

När du är klar trycker du på **Ctrl + O** och **sedan enter** för att spara filen. Tryck **på Ctrl + X** för att stänga filen och avsluta.

## <a name="create-the-policy"></a>Skapa principen

En principdefinition är ett objekt som används för att lagra den konfiguration som du vill använda. Principdefinitionen använder regel- och parameterfilerna för att definiera principen. Skapa en principdefinition med cmdleten [New-AzPolicyDefinition.](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicydefinition)

 Principregler och parametrar är de filer som du har skapat och lagrat som .json-filer i molngränssnittet.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Tilldela principen

I det här exemplet tilldelas principen till en resursgrupp med [ny-AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment). Alla virtuella datorer som skapas i resursgruppen **myResourceGroup** kan inte installera vm Access Agent- eller Custom Script-tilläggen. 

Använd [Get-AzSubscription | Formatera-tabell-cmdlet](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) för att få ditt prenumerations-ID att använda i stället för det i exemplet.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>Testa principen

Testa principen genom att försöka använda vm Access-tillägget. Följande bör misslyckas med meddelandet "Set-AzVMAccessExtension: Resource 'myVMAccess' inte godkändes av principen."

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

I portalen bör lösenordsändringen misslyckas med "Malldistributionen misslyckades på grund av principöverträdelse". .

## <a name="remove-the-assignment"></a>Ta bort tilldelningen

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Ta bort principen

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Nästa steg
Mer information finns i [Azure Policy](../../governance/policy/overview.md).
