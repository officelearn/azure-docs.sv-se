---
title: Använda en användartilldelade MSI på en virtuell Windows-dator för att få åtkomst till Azure Resource Manager
description: En självstudiekurs som beskriver steg för steg hur du använder en användartilldelad hanterad tjänstidentitet (MSI) på en virtuell Windows-dator för att få åtkomst till Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: daveba
ms.openlocfilehash: 67bb45f7bd27a142b978bedb48925cc41e8d1287
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37904381"
---
# <a name="tutorial-use-a-user-assigned-managed-service-identity-msi-on-a-windows-vm-to-access-azure-resource-manager"></a>Självstudie: Använda en användartilldelad hanterad tjänstidentitet (MSI) på en virtuell Windows-dator för att få åtkomst till Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här självstudien beskriver hur du skapar en användartilldelad identitet, hur du tilldelar den till en virtuell Windows-dator (VM) och hur du sedan använder identiteten för att få åtkomst till Azure Resource Manager-API:et. Hanterade tjänstidentiteter hanteras automatiskt av Azure. De gör det möjligt att autentisera till tjänster som stöder Azure AD-autentisering, utan att du behöver bädda in autentiseringsuppgifter i din kod. 

Lär dig att:

> [!div class="checklist"]
> * Skapa en virtuell Windows-dator 
> * Skapa en användartilldelad identitet
> * Tilldela en användartilldelad identitet till din virtuella Windows-dator
> * Bevilja den användartilldelade identiteten åtkomst till en resursgrupp i Azure Resource Manager 
> * Skaffa ett åtkomsttoken med hjälp av den användartilldelade identiteten och använd den för att anropa Azure Resource Manager 
> * Läsa egenskaper för en resursgrupp

## <a name="prerequisites"></a>Nödvändiga komponenter

- Om du inte har arbetat med hanterade tjänstidentiteter tidigare rekommenderar vi att du läser den här [översikten](overview.md). **Gå också igenom [skillnaderna mellan system- och användartilldelade identiteter](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- För att kunna utföra stegen som beskriver hur du skapar resurser och hanterar roller i den här självstudiekursen behöver ditt konto ha behörigheten ”Ägare” samt lämpligt omfång (din prenumeration eller resursgrupp). Information om hur du tilldelar roller finns i [Använda rollbaserad åtkomstkontroll för att hantera åtkomsten till dina Azure-prenumerationsresurser](/azure/role-based-access-control/role-assignments-portal).

Om du väljer att installera och använda PowerShell lokalt behöver du ha version 5.7 eller senare av Azure PowerShell-modulen för den här självstudien. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-resource-group"></a>Skapa resursgrupp

I följande exempel skapas en resursgrupp med namnet *myResourceGroupVM* i regionen *EastUS*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell Windows-dator när resursgruppen har skapats.

Ange användarnamnet och lösenordet för administratörskontot på den virtuella datorn med [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```
Skapa den virtuella datorn med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="create-a-user-assigned-identity"></a>Skapa en användartilldelad identitet

En användartilldelad identitet skapas som en fristående Azure-resurs. Azure skapar med hjälp av [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity) en identitet i din Azure AD-klient som kan tilldelas till en eller flera Azure-tjänstinstanser.

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

Svaret innehåller information om den användartilldelade identiteten som skapats, liknande den i följande exempel. Notera `Id`-värdet för din användartilldelade identitet. Du behöver det i nästa steg:

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>Tilldela den användartilldelade identiteten till en virtuell Windows-dator

En användartilldelad identitet kan användas av klienter på flera Azure-resurser. Använd följande kommandon för att tilldela den användartilldelade identiteten till en enskild virtuell dator. Använd egenskapen `Id` som returnerades i föregående steg för `-IdentityID`-parametern.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Ge den användartilldelade hanterade tjänstidentiteten åtkomst till en resursgrupp i Azure Resource Manager 

Hanterade tjänstidentiteter (MSI) tillhandahåller identiteter som din kod kan använda för att begära åtkomsttoken för att autentisera mot resurs-API: er som stöder Azure AD-autentisering. I de här självstudiekursen använder din kod Azure Resource Manager-API:et. 

Innan din kod kan komma åt API:et måste du ge identiteten åtkomst till en resurs i Azure Resource Manager. I det här fallet den resursgrupp som den virtuella datorn finns i. Uppdatera värdet för `<SUBSCRIPTION ID>` baserat på din miljö.

```azurepowershell-interactive
$spID = (Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

Svaret innehåller information om rolltilldelningen som skapats, liknande den i följande exempel:

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Skaffa en åtkomsttoken med hjälp av den virtuella datorns identitet och använd den för att anropa Resource Manager 

Under resten av självstudiekursen arbetar du från den virtuella datorn som vi skapade tidigare.

1. Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com)

2. Gå till **Virtuella datorer** på portalen, gå till den virtuella Windows-datorn och klicka sedan på **Anslut** under **Översikt**.

3. Ange det **användarnamn** och **lösenord** som du använde när du skapade den virtuella Windows-datorn.

4. Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn öppnar du **PowerShell** i fjärrsessionen.

5. Använd PowerShells `Invoke-WebRequest` och skicka en begäran till den lokala MSI-slutpunkten för att hämta en åtkomsttoken för Azure Resource Manager.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=73444643-8088-4d70-9532-c3a0fdc190fz&resource=https://management.azure.com' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>Läsa egenskaper för en resursgrupp

Använd den åtkomsttoken som du hämtade i föregående steg för att komma åt Azure Resource Manager, och läs egenskaperna för den resursgrupp som du gav den användartilldelade identiteten åtkomst till. Ersätt <SUBSCRIPTION ID> med prenumerations-id:t för din miljö.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
Svaret innehåller information om den specifika resursgruppen, liknande den i följande exempel:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/TestRG","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar en användartilldelad identitet och kopplar den till en virtuell Azure-dator för att komma åt Azure Resource Manager-API:et.  Mer information om Azure Resource Manager finns här:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)