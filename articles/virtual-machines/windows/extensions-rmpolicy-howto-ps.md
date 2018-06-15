---
title: Använda Azure för att begränsa VM-tillägget installation | Microsoft Docs
description: Använda Azure för att begränsa tillägget distributioner.
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
ms.openlocfilehash: da5b0db997ba1aa0e998f6fe2645e955b490951d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
ms.locfileid: "30248179"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Använda Azure för att begränsa tillägg installation på virtuella Windows-datorer

Om du vill förhindra användning eller installationen av vissa tillägg på Windows-datorer kan du skapa en Azure-princip med PowerShell för att begränsa tillägg för virtuella datorer inom en resursgrupp. 

Den här kursen används Azure PowerShell i molnet gränssnittet, som uppdateras till den senaste versionen. Om du väljer att installera och använda PowerShell lokalt kräver den här självstudiekursen Azure PowerShell Modulversion 3,6 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). 

## <a name="create-a-rules-file"></a>Skapa en regelfil

För att begränsa vilka tillägg som kan installeras, måste du ha en [regeln](/azure/azure-policy/policy-definition#policy-rule) att tillhandahålla logik för att identifiera tillägget.

Det här exemplet visar hur du neka tillägg som publicerats av 'Microsoft.Compute' genom att skapa en regelfil i Azure Cloud-gränssnittet, men om du arbetar lokalt i PowerShell kan du skapa en lokal fil och ersätta sökvägen ($home/clouddrive) med sökvägen till den lokal fil på din dator.

I en [moln Shell](https://shell.azure.com/powershell), typ:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Kopiera och klistra in följande JSON i filen.

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

När du är klar i **Ctrl + O** och sedan **RETUR** att spara filen. Träffa **Ctrl + X** Stäng filen och avsluta.

## <a name="create-a-parameters-file"></a>Skapa en fil med parametrar

Du måste också en [parametrar](/azure/azure-policy/policy-definition#parameters) -fil som skapar en struktur som du kan använda för att skicka in en lista över tillägg som ska blockeras. 

Det här exemplet illustrerar hur du skapar en fil med parametrar för virtuella datorer i molnet Shell, men om du arbetar lokalt i PowerShell kan du skapa en lokal fil och ersätta sökvägen ($home/clouddrive) med sökvägen till den lokala filen på din dator.

I [moln Shell](https://shell.azure.com/powershell), typ:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Kopiera och klistra in följande JSON i filen.

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

När du är klar i **Ctrl + O** och sedan **RETUR** att spara filen. Träffa **Ctrl + X** Stäng filen och avsluta.

## <a name="create-the-policy"></a>Skapa principen

En principdefinition är ett objekt som används för att lagra den konfiguration som du vill använda. Principdefinitionen använder filerna regler och parametrar för att definiera principen. Skapa en princip definition med den [ny AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) cmdlet.

 Principregler och parametrar är filer som du skapat och lagras som JSON-filer i molnet-gränssnittet.


```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Tilldela principen

Det här exemplet tilldelar principen till en resurs med [ny AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment). Någon virtuell dator skapas i den **myResourceGroup** resursgruppen kommer inte att kunna installera tillägg för Virtuella Access Agent eller anpassade skript. 

Använd den [Get-AzureRMSubscription | Format-Table](/powershell/module/azurerm.profile/get-azurermsubscription) för att hämta ditt prenumerations-ID som ska användas istället för det i exemplet.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzureRMPolicyAssignment `
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

Försök att använda tillägget för virtuell dator åtkomst om du vill testa principen. Följande bör misslyckas med meddelandet ”Set-AzureRmVMAccessExtension: princip förhindrar resurs 'myVMAccess'”.

```azurepowershell-interactive
Set-AzureRmVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

I portalen får ändra lösenordet misslyckas med den ”malldistributionen misslyckades på grund av principöverträdelsen”. Meddelande.

## <a name="remove-the-assignment"></a>Tar bort tilldelningen

```azurepowershell-interactive
Remove-AzureRMPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Ta bort principen

```azurepowershell-interactive
Remove-AzureRmPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Nästa steg
Mer information finns i [Azure princip](../../azure-policy/azure-policy-introduction.md).
