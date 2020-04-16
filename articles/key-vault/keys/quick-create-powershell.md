---
title: 'Snabbstart: Ange och hämta en nyckel från Azure Key Vault'
description: Snabbstart som visar hur du anger och hämtar en nyckel från Azure Key Vault med Azure PowerShell
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.openlocfilehash: c407c10327a80de6b3df18a3db3978468c9f8da0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424190"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-powershell"></a>Snabbstart: Ange och hämta en nyckel från Azure Key Vault med Azure PowerShell

I den här snabbstarten skapar du ett nyckelvalv i Azure Key Vault med Azure PowerShell. Azure Key Vault är en molntjänst som fungerar som säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Om du vill ha mer information om Key Vault kan du läsa [översikten](../general/overview.md). Azure PowerShell används för att skapa och hantera Azure-resurser med hjälp av kommandon eller skript. När du har slutfört det, kommer du att lagra en nyckel.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 1.0.0 eller senare. Skriv `$PSVersionTable.PSVersion` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzAccount` för att skapa en anslutning till Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning

Sedan skapar du ett nyckelvalv. När du utför det här steget behöver du en del information:

Även om vi använder "Contoso KeyVault2" som namn för vårt Key Vault under hela den här snabbstarten, måste du använda ett unikt namn.

- **Valvnamn** Contoso-Vault2.
- **Namn på resursgrupp** ContosoResourceGroup.
- **Plats** Östra USA.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

Utdata från denna cmdlet visar egenskaper för nyckelvalvet du precis skapade. Anteckna de två egenskaperna som visas nedan:

* **Valvnamn**: I det här exemplet är namnet **Contoso-vault2**. Du ska använda det här namnet för andra Key Vault-cmdlets.
* **Valvets URI**: I det här exemplet är det https://Contoso-Vault2.vault.azure.net/. Program som använder ditt valv via dess REST-API måste använda denna URI.

När du har skapat valvet så är ditt Azure-konto det enda kontot med behörighet att göra någonting i valvet.

![Utdata när kommandot för att skapa Key Vault har slutförts](../media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="add-a-key-to-key-vault"></a>Lägga till en nyckel i Key Vault

Om du vill lägga till en nyckel i valvet behöver du bara vidta några ytterligare steg. Den här nyckeln kan användas av ett program. 

Skriv kommandona nedan för att skapa en så kallad **ExampleKey:**

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName 'Contoso-Vault2' -Name 'ExampleKey' -Destination 'Software'
```

Du kan nu referera till den här nyckeln som du har lagt till i Azure Key Vault med hjälp av dess URI. Används **https://Contoso-Vault2.vault.azure.net/keys/ExampleKey** för att hämta den aktuella versionen. 

Så här visar du tidigare lagrad nyckel:

```azurepowershell-interactive
Get-AzKeyVaultKey -VaultName 'Contoso-Vault2' -KeyName 'ExampleKey'
```

Nu har du skapat ett Key Vault, lagrat en nyckel och hämtat den.

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna och självstudierna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.
När det inte längre behövs kan du använda kommandot [Ta bort AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen och alla relaterade resurser. Så här tar du bort resurserna:

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Nyckelvalv och sparade ett certifikat i det. Om du vill veta mer om Key Vault och hur du integrerar det med dina program fortsätter du med artiklarna nedan.

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Se referensen för [cmdlets](/powershell/module/az.keyvault/) för Azure PowerShell Key Vault
- Granska [metodtips för Azure Key Vault](../general/best-practices.md)
