---
title: Självstudie – skapa en anpassad roll med Azure PowerShell | Microsoft Docs
description: Kom igång med att skapa en anpassad roll med Azure PowerShell.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/12/2018
ms.author: rolyon
ms.openlocfilehash: f49f6f03b6d9f1c51cada58ae782bbc364fc9d66
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427295"
---
# <a name="tutorial-create-a-custom-role-using-azure-powershell"></a>Självstudier: Skapa en anpassad roll med hjälp av Azure PowerShell

Om de [inbyggda rollerna](built-in-roles.md) inte uppfyller organisationens specifika krav kan du skapa egna, anpassade roller. För den här självstudien skapar du en anpassad roll med namnet Reader Support Tickets (Läsare av supportbegäranden) med hjälp av Azure PowerShell. Med den här anpassade rollen kan användare visa allt i prenumerationen samt öppna supportbegäranden.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en anpassad roll
> * Lista anpassade roller
> * Uppdatera en anpassad roll
> * Ta bort en anpassad roll

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna genomföra den här kursen behöver du följande:

- Behörigheter att skapa anpassade roller som [Owner](built-in-roles.md#owner) (Ägare) eller [User Access Administrator](built-in-roles.md#user-access-administrator) (Administratör för användaråtkomst)
- Lokalt installerat [Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps)

## <a name="sign-in-to-azure-powershell"></a>Logga in till Azure PowerShell

Logga in till [Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Det enklaste sättet att skapa en anpassad roll är att utgå från en inbyggd roll, redigera den och sedan skapa en ny roll.

1. I PowerShell använder du kommandot [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) för att hämta listan över åtgärder för resursprovidern Microsoft.Support. Det är bra att känna till de åtgärder som är tillgängliga för att skapa dina behörigheter. Det finns även en lista över alla åtgärder på [Azure Resource Manager resource provider operations](resource-provider-operations.md#microsoftsupport) (Åtgärder för Azure Resource Manager-resursprovider).

    ```azurepowershell
    Get-AzureRMProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. Använd kommandot [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) för att mata ut rollen [Reader](built-in-roles.md#reader) (Läsare) i JSON-format.

    ```azurepowershell
    Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. Öppna filen **ReaderSupportRole.json** i en textredigerare.

    Följande visar JSON-utdata. Information om de olika egenskaperna finns i [Custom roles](custom-roles.md) (Anpassade roller).

    ```json
    {
        "Name":  "Reader",
        "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "IsCustom":  false,
        "Description":  "Lets you view everything, but not make any changes.",
        "Actions":  [
                        "*/read"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/"
                             ]
    }
    ```
    
1. Redigera JSON-filen för att lägga till `"Microsoft.Support/*"`-åtgärden till `Actions`-egenskapen. Se till att inkludera ett kommatecken efter läsåtgärden. Den här åtgärden tillåter att användare skapar supportbegäranden.

1. Hämta ID för din prenumeration med hjälp av kommandot [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

    ```azurepowershell
    Get-AzureRmSubscription
    ```

1. I `AssignableScopes` lägger du till ditt prenumerations-ID i följande format: `"/subscriptions/00000000-0000-0000-0000-000000000000"`

    Du måste lägga till explicita prenumerations-ID:n; annars tillåts du inte importera rollen i din prenumeration.

1. Ta bort egenskapsraden `Id` och ändra egenskapen `IsCustom` till `true`.

1. Ändra egenskaperna `Name` och `Description` till ”Reader Support Tickets” (Läsare av supportbegäranden) och ”View everything in the subscription and also open support tickets” (Visa allt i prenumerationen och även öppna supportbegäranden).

    Din JSON-fil ska se ut så här:

    ```json
    {
        "Name":  "Reader Support Tickets",
        "IsCustom":  true,
        "Description":  "View everything in the subscription and also open support tickets.",
        "Actions":  [
                        "*/read",
                        "Microsoft.Support/*"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/subscriptions/00000000-0000-0000-0000-000000000000"
                             ]
    }
    ```
    
1. För att skapa en ny anpassad roll använder du kommandot [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) och anger definitionsfilen för JSON-rollen.

    ```azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    Den nya anpassade rollen är nu tillgänglig i Azure-portalen och kan tilldelas till användare, grupper eller tjänstens huvudnamn precis som inbyggda roller.

## <a name="list-custom-roles"></a>Lista anpassade roller

- Om du vill lista alla dina anpassade roller använder du kommandot [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

    ```azurepowershell
    Get-AzureRmRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
    ```

    ```Output
    Name                   IsCustom
    ----                   --------
    Reader Support Tickets     True
    ```
    
    Du kan även visa den anpassade rollen i Azure-portalen.

    ![skärmbild av anpassad roll som importerats i Azure-portalen](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Uppdatera en anpassad roll

Om du vill uppdatera den anpassade rollen kan du uppdatera JSON-filen eller använda objektet `PSRoleDefinition`.

1. För att uppdatera JSON-filen använder du kommandot [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) för att mata ut den anpassade rollen i JSON-format.

    ```azurepowershell
    Get-AzureRmRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. Öppna filen i en textredigerare.

1. I `Actions` lägger du till åtgärden för att skapa och hantera resursgruppsdistributioner `"Microsoft.Resources/deployments/*"`.

    Din uppdaterade JSON-fil ska se ut så här:

    ```json
    {
        "Name":  "Reader Support Tickets",
        "Id":  "22222222-2222-2222-2222-222222222222",
        "IsCustom":  true,
        "Description":  "View everything in the subscription and also open support tickets.",
        "Actions":  [
                        "*/read",
                        "Microsoft.Support/*",
                        "Microsoft.Resources/deployments/*"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/subscriptions/00000000-0000-0000-0000-000000000000"
                             ]
    }
    ```
        
1. För att uppdatera den anpassade rollen använder du kommandot [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) och anger den uppdaterade JSON-filen.

    ```azurepowershell
    Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. Om du vill använda objektet `PSRoleDefintion` för att uppdatera din anpassade roll använder du först kommandot [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) för att hämta rollen.

    ```azurepowershell
    $role = Get-AzureRmRoleDefinition "Reader Support Tickets"
    ```
    
1. Anropa metoden `Add` för att lägga till åtgärden för att läsa diagnostikinställningar.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. Använd kommandot [Set AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) för att uppdatera rollen.

    ```azurepowershell
    Set-AzureRmRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## <a name="delete-a-custom-role"></a>Ta bort en anpassad roll

1. Använd kommandot [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) för att hämta ID för den anpassade rollen.

    ```azurepowershell
    Get-AzureRmRoleDefinition "Reader Support Tickets"
    ```

1. Använd kommandot [Remove-AzureRmRoleDefinition](/powershell/module/azurerm.resources/remove-azurermroledefinition) och ange roll-ID för att ta bort den anpassade rollen.

    ```azurepowershell
    Remove-AzureRmRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. När du ombeds bekräfta skriver du **Y**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa anpassade roller med PowerShell](custom-roles-powershell.md)
