---
title: Så här visar du tjänstens huvudnamn för en hanterad identitet med hjälp av PowerShell
description: Stegvisa instruktioner för att visa tjänstens huvudnamn för en hanterad identitet med hjälp av PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: daveba
ms.openlocfilehash: 0cf1915e4013451dbb09f2c4af3df2bad6166475
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438957"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Visa tjänstens huvudnamn för en hanterad identitet med hjälp av PowerShell

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får lära du att visa tjänstens huvudnamn för en hanterad identitet med hjälp av PowerShell.

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md).
- Om du inte redan har ett Azure-konto, [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).
- Aktivera [systemtilldelade identiteter på en virtuell dator](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) eller [program](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Om du väljer att installera och använda PowerShell lokalt måste du ha version 5.7.0 eller senare av Azure PowerShell-modulen. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul). 
- Om du kör PowerShell lokalt behöver du även göra följande: 
    - Kör `Login-AzureRmAccount` för att skapa en anslutning med Azure.
    - Installera [den senaste versionen av PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Kör `Install-Module -Name PowerShellGet -AllowPrerelease` för att hämta förhandsversionen av `PowerShellGet`-modulen (du kan behöva `Exit` ur den aktuella PowerShell-sessionen när du har kört det här kommandot för att installera `AzureRM.ManagedServiceIdentity`-modulen).
    - Kör `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease` du installerar förhandsversionen av den `AzureRM.ManagedServiceIdentity` modulen för att utföra den användartilldelade hanterad identitet åtgärder i den här artikeln.

## <a name="view-the-service-principal"></a>Visa tjänstens huvudnamn

Den här följande kommando visar hur du visa tjänstens huvudnamn för en virtuell dator eller ett program med systemtilldelade identiteter aktiverat. Ersätt `<VM or application name>` med dina egna värden.

```PowerShell
Get-AzureRmADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du visar tjänsthuvudnamn i Azure AD med hjälp av PowerShell finns i [Get-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal).


