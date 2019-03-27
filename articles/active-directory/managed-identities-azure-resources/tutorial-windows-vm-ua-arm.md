---
title: Använda en användartilldelad hanterad identitet på virtuell Windows-dator för att få åtkomst till Azure Resource Manager
description: En självstudiekurs som beskriver steg för steg hur du använder en användartilldelad hanterad identitet på en virtuell Windows-dator för att få åtkomst till Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42b0269d783dac17c48553c837ccdc2b3b014790
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58442225"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>Självstudier: Använda en användartilldelad hanterad identitet på en virtuell Windows-dator för åtkomst till Azure Resource Manager

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här självstudien beskriver hur du skapar en användartilldelad identitet, hur du tilldelar den till en virtuell Windows-dator (VM) och hur du sedan använder identiteten för att få åtkomst till Azure Resource Manager-API:et. Hanterade tjänstidentiteter hanteras automatiskt av Azure. Det gör det möjligt att autentisera mot tjänster som stöder Azure AD-autentisering, utan att du behöver bädda in autentiseringsuppgifter i din kod. 

Lär dig att:

> [!div class="checklist"]
> * Skapa en användartilldelad hanterad identitet
> * Tilldela en användartilldelad identitet till din virtuella Windows-dator
> * Ge den användartilldelade identiteten åtkomst till en resursgrupp i Azure Resource Manager 
> * Hämta en åtkomsttoken med hjälp av den användartilldelade identiteten och använd den för att anropa Azure Resource Manager 
> * Läsa egenskaper för en resursgrupp

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Logga in på Azure-portalen](https://portal.azure.com)

- [Skapa en virtuell Windows-dator](/azure/virtual-machines/windows/quick-create-portal)

- Ditt konto måste ha behörigheten ”Ägare” och lämpligt omfång (din prenumeration eller resursgrupp) för att du ska kunna utföra stegen i den här självstudien som beskriver hur du skapar resurser och hanterar roller. Information om rolltilldelning finns i [Använda rollbaserad åtkomstkontroll för att hantera åtkomsten till dina Azure-prenumerationsresurser](/azure/role-based-access-control/role-assignments-portal).
- [Installera den senaste versionen av Azure PowerShell-modulen](/powershell/azure/install-az-ps). 
- Kör `Connect-AzAccount` för att skapa en anslutning med Azure.
- Installera [den senaste versionen av PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
- Kör `Install-Module -Name PowerShellGet -AllowPrerelease` för att hämta förhandsversionen av `PowerShellGet`-modulen (du kan behöva `Exit` ur den aktuella PowerShell-sessionen när du har kört det här kommandot för att installera `Az.ManagedServiceIdentity`-modulen).
- Kör `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` för att installera förhandsversionen av `Az.ManagedServiceIdentity`-modulen för att utföra åtgärderna för användartilldelade identiteter i den här artikeln.

## <a name="create-a-user-assigned-identity"></a>Skapa en användartilldelad identitet

En användartilldelad identitet skapas som en fristående Azure-resurs. Azure skapar med hjälp av [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/get-azuserassignedidentity) en identitet i din Azure AD-klient som kan tilldelas till en eller flera Azure-tjänstinstanser.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

Svaret innehåller information om den användartilldelade identiteten som skapats, liknande den i följande exempel. Anteckna värdena `Id` och `ClientId` för din användartilldelade identitet, eftersom de används i efterföljande steg:

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
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-identity-access-to-a-resource-group-in-azure-resource-manager"></a>Ge den användartilldelade identiteten åtkomst till en resursgrupp i Azure Resource Manager 

Hanterade identiteter för Azure-resurser tillhandahåller identiteter som din kod kan använda för att begära åtkomsttoken för autentisering mot resurs-API:er som stöder Azure AD-autentisering. I den här självstudiekursen använder koden Azure Resource Manager-API:et. 

Innan koden kan komma åt API:et måste du ge identiteten åtkomst till en resurs i Azure Resource Manager. I det här fallet den resursgrupp som den virtuella datorn finns i. Uppdatera värdet för `<SUBSCRIPTION ID>` baserat på din miljö.

```azurepowershell-interactive
$spID = (Get-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
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

5. Använd PowerShells `Invoke-WebRequest` och skicka en begäran till den lokala slutpunkten för hanterade identiteter för Azure-resurser för att hämta en åtkomsttoken för Azure Resource Manager.  Värdet `client_id` är det värde som returneras när du [skapade den användartilldelade hanterade identiteten](#create-a-user-assigned-identity).

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
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
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/myResourceGroupVM","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar en Användartilldelad identitet och kopplar den till en Azure-dator åtkomst till Azure Resource Manager-API.  Mer information om Azure Resource Manager finns här:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)
