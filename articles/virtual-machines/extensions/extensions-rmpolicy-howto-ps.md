---
title: Använda Azure för att begränsa installation av VM-tillägg | Microsoft Docs
description: Använda Azure för att begränsa tillägget distributioner.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz;cynthn
ms.openlocfilehash: 4dc68127f2d19426c372be027634bb2563dbfa6c
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341665"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Använda Azure Policy för att begränsa installation av tillägg på virtuella Windows-datorer

Du kan skapa en Azure policy som använder PowerShell för att begränsa tillägg för virtuella datorer i en resursgrupp om du vill förhindra användning eller installationen av vissa tillägg på dina virtuella Windows-datorer. 

Den här självstudien används Azure PowerShell i Cloud Shell, som uppdateras till den senaste versionen. 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="create-a-rules-file"></a>Skapa en regelfil

För att begränsa vilka tillägg som kan installeras men, du måste ha en [regeln](../../governance/policy/concepts/definition-structure.md#policy-rule) att tillhandahålla logik för att identifiera tillägget.

Det här exemplet visar hur du neka tillägg som publicerats av ”Microsoft.Compute” genom att skapa en regelfil i Azure Cloud Shell, men om du arbetar lokalt i PowerShell kan du också skapa en lokal fil och ersätta sökvägen ($home/clouddrive) med sökvägen till den lokal fil på din dator.

I en [Cloud Shell](https://shell.azure.com/powershell), typ:

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

När du är klar trycker du på den **Ctrl + O** och sedan **RETUR** att spara filen. Tryck på **Ctrl + X** att Stäng filen och avsluta.

## <a name="create-a-parameters-file"></a>Skapa en fil med parametrar

Du behöver också en [parametrar](../../governance/policy/concepts/definition-structure.md#parameters) -fil som skapar en struktur som du kan använda för att skicka en lista över tillägg ska blockeras. 

Det här exemplet visar hur du skapar en fil med parametrar för virtuella datorer i Cloud Shell, men om du arbetar lokalt i PowerShell kan du också skapa en lokal fil och ersätta sökvägen ($home/clouddrive) med sökvägen till den lokala filen på din dator.

I [Cloud Shell](https://shell.azure.com/powershell), typ:

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

När du är klar trycker du på den **Ctrl + O** och sedan **RETUR** att spara filen. Tryck på **Ctrl + X** att Stäng filen och avsluta.

## <a name="create-the-policy"></a>Skapa principen

En principdefinition är ett objekt som används för att lagra den konfiguration som du vill använda. Principdefinitionen använder filerna regler och parametrar för att definiera principen. Skapa en principdefinition med den [New AzPolicyDefinition](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicydefinition) cmdlet.

 Hanteringsprincipregler (MPR) och parametrar är filer som du skapade och lagras som JSON-filer i cloudshell.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Tilldela principen

Det här exemplet tilldelar principen till en resurs med [New AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment). Alla virtuella datorer som skapats i den **myResourceGroup** resursgrupp kommer inte att kunna installera tillägg för VM-agenten för åtkomst eller anpassade skript. 

Använd den [Get-AzSubscription | Format-Table](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) cmdlet för att hämta ditt prenumerations-ID du använder i stället för den i det här exemplet.

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

Om du vill testa principen som du försök använda VM Access-tillägg. Följande bör misslyckas med meddelandet ”Set-AzVMAccessExtension: Resursen 'myVMAccess' förhindrades av principen ”.

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

I portalen lösenordsändringen bör inte fungera med den ”malldistributionen misslyckades på grund av principöverträdelse”. meddelande.

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
