---
title: Använd en Windows VM-användare som tilldelats MSI att komma åt Azure Resource Manager
description: En självstudiekurs som vägleder dig genom processen att använda en användaren tilldelas hanterade tjänsten identitet (MSI) på en Windows-VM för att komma åt Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: arluca
ms.openlocfilehash: d80e0fc35b8c20bd61a78a68542f3311c1a40952
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-windows-vm-to-access-azure-resource-manager"></a>Använd en användaren tilldelas hanterade tjänsten identitet (MSI) på en Windows-VM för att komma åt Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Den här självstudiekursen beskrivs hur du skapar en användare som tilldelats identitet, tilldela den till en Windows virtuell dator (VM) och sedan använda identitet för åtkomst till Azure Resource Manager API. Hanterade Tjänsteidentiteter hanteras automatiskt av Azure. De aktivera autentisering för tjänster som stöder Azure AD-autentisering utan att behöva bädda in autentiseringsuppgifter i din kod. 

Lär dig att:

> [!div class="checklist"]
> * Skapa en virtuell Windows-dator 
> * Skapa en användare som tilldelats identitet
> * Tilldela dina användare som tilldelats Windows VM identitet
> * Ge åtkomst för användaren som har tilldelats identitet till en resursgrupp i Azure Resource Manager 
> * Få en åtkomsttoken med hjälp av användaren som har tilldelats identitet och använda den för att anropa Azure Resource Manager 
> * Läsa egenskaperna för en resursgrupp

## <a name="prerequisites"></a>Förutsättningar

- Om din är bekant med hanterade tjänstidentiteten checka ut den [översikt](overview.md) avsnitt. **Se till att granska den [skillnaderna mellan system- och tilldelade identiteter](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Skapa en nödvändig resurs och rollen hanteringsåtgärder utföra i den här självstudiekursen, måste ditt konto ”ägare” behörighet för definitionsområdet lämpliga (arbetsgruppen prenumerationen eller resursen). Om du behöver hjälp med rolltilldelningen finns [Use Role-Based behörighet att hantera åtkomst till resurserna i Azure-prenumeration](/azure/role-based-access-control/role-assignments-portal).

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudiekursen Azure PowerShell Modulversion 5.7 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-resource-group"></a>Skapa resursgrupp

I följande exempel visas en resursgrupp med namnet *myResourceGroupVM* skapas i den *EastUS* region.

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

## <a name="create-a-user-assigned-identity"></a>Skapa en användare som tilldelats identitet

En användare som tilldelats identitet skapas som en fristående Azure-resurs. Med hjälp av den [ny AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity), Azure skapar en identitet i Azure AD-klienten som kan tilldelas till en eller flera instanser av Azure-tjänsten.

> [!IMPORTANT]
> Skapa användartilldelade identiteter med specialtecken (dvs understreck) i namnet stöds inte för närvarande. Använd alfanumeriska tecken. Sök igen efter uppdateringar.  Mer information finns i [vanliga frågor och kända problem](known-issues.md)

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

Svaret innehåller information om användaren som har tilldelats identitet skapas, liknande exemplet nedan. Observera den `Id` värdet för användaren som har tilldelats-identitet som den ska användas i nästa steg:

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

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>Tilldela användaren som har tilldelats identitet till en virtuell Windows-dator

En användare som tilldelats identitet kan användas av klienter på Azure-resurser. Använd följande kommandon för att tilldela användaridentitet tilldelad till en enda virtuell dator. Använd den `Id` egenskapen returneras i föregående steg för den `-IdentityID` parameter.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Bevilja användaren tilldelas en resursgrupp i Azure Resource Manager MSI-åtkomst 

Hanterad Service identitet (MSI) ger identiteter som din kod kan använda för att begära åtkomst-token för att autentisera till resurs-API: er som stöder Azure AD-autentisering. I den här självstudiekursen kommer koden åtkomst till Azure Resource Manager API. 

Innan din kod kan komma åt API: et, måste du bevilja åtkomst till en resurs i Azure Resource Manager identitet. I det här fallet resursgruppen där den virtuella datorn finns. Uppdatera värdet för `<SUBSCRIPTION ID>` som passar din miljö.

```azurepowershell-interactive
$spID = (Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

Svaret innehåller information om rolltilldelning skapas, liknande exemplet nedan:

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

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Få en åtkomsttoken med hjälp av den Virtuella datorns identitet och använda den för att anropa Resource Manager 

Under resten av kursen fungerar från den virtuella datorn som vi skapade tidigare.

1. Logga in på Azure portal [https://portal.azure.com](https://portal.azure.com)

2. I portalen, går du till **virtuella datorer** och gå till Windows-dator och i den **översikt**, klickar du på **Anslut**.

3. Ange den **användarnamn** och **lösenord** du använde när du skapade den virtuella Windows-datorn.

4. Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn, öppna **PowerShell** i fjärrsessionen.

5. Med hjälp av Powershell's `Invoke-WebRequest`, gör en begäran till den lokala MSI-slutpunkten för att hämta ett åtkomsttoken för Azure Resource Manager.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=73444643-8088-4d70-9532-c3a0fdc190fz&resource=https://management.azure.com' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>Läsa egenskaperna för en resursgrupp

Använd den åtkomst-token som hämtades i föregående steg att få åtkomst till Azure Resource Manager och läsa egenskaper för resursen gruppen du beviljas din identitet åtkomst för användaren som har tilldelats. Ersätt <SUBSCRIPTION ID> med prenumerations-id för din miljö.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
Svaret innehåller den specifika resursgruppen informationen liknar följande exempel:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/TestRG","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Nästa steg

- En översikt över MSI finns [hanterade tjänstidentiteten översikt](overview.md).
