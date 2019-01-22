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
ms.openlocfilehash: 82b01cec892f15f7f85f6b5f822475114b5b73c6
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434997"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Använda Azure Policy för att begränsa installation av tillägg på virtuella Windows-datorer

Du kan skapa en Azure policy som använder PowerShell för att begränsa tillägg för virtuella datorer i en resursgrupp om du vill förhindra användning eller installationen av vissa tillägg på dina virtuella Windows-datorer. 

Den här självstudien används Azure PowerShell i Cloud Shell, som uppdateras till den senaste versionen. Om du väljer att installera och använda PowerShell lokalt behöver du ha version 3.6 eller senare av Azure PowerShell-modulen för den här självstudien. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul). 

## <a name="create-a-rules-file"></a>Skapa en regelfil

För att begränsa vilka tillägg som kan installeras men, du måste ha en [regeln](/azure/azure-policy/policy-definition#policy-rule) att tillhandahålla logik för att identifiera tillägget.

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

Du behöver också en [parametrar](/azure/azure-policy/policy-definition#parameters) -fil som skapar en struktur som du kan använda för att skicka en lista över tillägg ska blockeras. 

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

En principdefinition är ett objekt som används för att lagra den konfiguration som du vill använda. Principdefinitionen använder filerna regler och parametrar för att definiera principen. Skapa en principdefinition med den [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) cmdlet.

 Hanteringsprincipregler (MPR) och parametrar är filer som du skapade och lagras som JSON-filer i cloudshell.


```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Tilldela principen

Det här exemplet tilldelar principen till en resurs med [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment). Alla virtuella datorer som skapats i den **myResourceGroup** resursgrupp kommer inte att kunna installera tillägg för VM-agenten för åtkomst eller anpassade skript. 

Använd den [Get-AzureRMSubscription | Format-Table](/powershell/module/azurerm.profile/get-azurermsubscription) cmdlet för att hämta ditt prenumerations-ID du använder i stället för den i det här exemplet.

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

Om du vill testa principen som du försök använda VM Access-tillägg. Följande bör misslyckas med meddelandet ”Set-AzureRmVMAccessExtension: Resursen 'myVMAccess' förhindrades av principen ”.

```azurepowershell-interactive
Set-AzureRmVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

I portalen lösenordsändringen bör inte fungera med den ”malldistributionen misslyckades på grund av principöverträdelse”. meddelande.

## <a name="remove-the-assignment"></a>Ta bort tilldelningen

```azurepowershell-interactive
Remove-AzureRMPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Ta bort principen

```azurepowershell-interactive
Remove-AzureRmPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Nästa steg
Mer information finns i [Azure Policy](../../azure-policy/azure-policy-introduction.md).
