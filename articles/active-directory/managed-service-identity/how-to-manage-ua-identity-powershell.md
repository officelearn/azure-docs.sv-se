---
title: Skapa, visa och ta bort en användare som tilldelats (MSI) med hjälp av Azure PowerShell
description: Steg för steg tilldelas instruktioner om hur du skapar, visa och ta bort användaren hanterade tjänstidentiteten som använder Azure PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: cc9b1b002c882a847d0ba2359caf4a193ea8d648
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-azure-powershell"></a>Skapa, visa eller ta bort en som tilldelats användaridentitet med hjälp av Azure PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterade tjänstidentiteten ger Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i din kod. 

I den här artikeln lär du dig att skapa, visa och ta bort en som tilldelats användaridentitet med hjälp av Azure PowerShell.

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade tjänstidentiteten kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan ett system som har tilldelats och användaren som har tilldelats identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Installera [den senaste versionen av Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) om du inte redan har gjort.
- Om du väljer att installera och använda PowerShell lokalt kursen krävs av Azure PowerShell Modulversion 5.7.0 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-a-user-assigned-identity"></a>Skapa en användare som tilldelats identitet

Använd för att skapa en identitet för användaren som har tilldelats den [ny AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) kommando. Den `ResourceGroupName` parametern anger resursgruppen var att skapa den tilldelade identitet och `-Name` parametern anger dess namn. Ersätt den `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` parametervärden med egna värden:

> [!IMPORTANT]
> Skapa tilldelade användaridentiteter stöder endast alfanumeriska och bindestreck (0-9 eller a-z eller A-Z eller -) tecken. Namnet bör dessutom begränsas till 24 tecken för tilldelning till VM/VMSS ska fungera korrekt. Sök igen efter uppdateringar. Mer information finns i [vanliga frågor och kända problem](known-issues.md)

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Lista över användare som tilldelats identiteter

Använd om du vill visa en lista med användaridentiteter tilldelas den [Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity) kommando.  Den `-ResourceGroupName` parametern anger resursgruppen där användaren som har tilldelats identitet har skapats.  Ersätt den `<RESOURCE GROUP>` med ett eget värde:

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
I svaret, användaridentiteter har `"Microsoft.ManagedIdentity/userAssignedIdentities"` returvärdet för nyckeln, `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-identity"></a>Tar bort en användare som tilldelats identitet

Ta bort en användaridentitet genom att använda den [ta bort AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity) kommando.  Den `-ResourceGroupName` parametern anger resursgruppen där användaren som har tilldelats identitet har skapats och `-Name` parametern anger dess namn.  Ersätt den `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` värden för parametrar med egna värden:

 ```azurecli-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Ta bort en användare som tilldelats identitet tas inte bort referens, från alla resurser som den tilldelats. Identitet tilldelningar måste tas bort separat.

## <a name="related-content"></a>Relaterat innehåll

Mer information om Azure PowerShell MSI-kommandon och en fullständig lista finns [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity).


 
