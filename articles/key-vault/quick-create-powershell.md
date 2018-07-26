---
title: Azure-snabbstart – Skapa ett nyckelvalv via PowerShell | Microsoft Docs
description: ''
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 1126f665-2e6c-4cca-897e-7d61842e8334
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 05/10/2018
ms.author: barclayn
ms.openlocfilehash: 4acd8286cb8635f9a76815c936328a7c441e3115
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187156"
---
# <a name="quickstart-create-an-azure-key-vault-using-powershell"></a>Snabbstart: Skapa ett nyckelvalv med hjälp av PowerShell

Azure Key Vault är en molntjänst som fungerar som säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Mer information om Key Vault finns i [översikten](key-vault-overview.md). I den här snabbstarten använder du PowerShell till att skapa ett nyckelvalv. Sedan lagrar du en hemlighet i valvet du skapade.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt måste du ha version 5.1.1 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

```azurepowershell
Login-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

```azurepowershell
New-AzureRmResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning

Sedan skapar du ett nyckelvalv. När du utför det här steget behöver du en del information:

Även om vi använder ”Contoso KeyVault2” som namn på vårt nyckelvalv i den här snabbstarten måste du använda ett unikt namn.

- **Valvnamn** Contoso-Vault2.
- **Resursgruppnamn** ContosoResourceGroup.
- **Plats** Östra USA.

```azurepowershell
New-AzureRmKeyVault -VaultName 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

Utdata från denna cmdlet visar egenskaper för nyckelvalvet du precis skapade. Anteckna de två egenskaperna som visas nedan:

* **Valvnamn**: I det här exemplet är namnet **Contoso-vault2**. Du ska använda det här namnet för andra Key Vault-cmdlets.
* **Valvets URI**: I det här exemplet är det https://contosokeyvault.vault.azure.net/. Program som använder ditt valv via dess REST-API måste använda denna URI.

När du har skapat valvet så är ditt Azure-konto det enda kontot med behörighet att göra någonting i valvet.

![Utdata när kommandot för att skapa Key Vault har slutförts](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Lägga till en hemlighet i nyckelvalvet

När du ska lägga till en hemlighet i valvet behöver du bara utföra ett fåtal steg. I det här fallet lägger du till ett lösenord som kan användas av ett program. Lösenordet kallas **ExamplePassword** och vi lagrar värdet **Pa$$w0rd** i det.

Konvertera först värdet Pa$$w0rd till en säker sträng genom att skriva följande:

```azurepowershell
$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force
```

Skriv sedan PowerShell-kommandona nedan för att skapa en hemlighet i nyckelvalvet med namnet **ExamplePassword**, som har värdet **Pa$$w0rd**:

```azurepowershell
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'ExamplePassword' -SecretValue $secretvalue
```

Så här visar du värdet som finns i hemligheten som oformaterad text:

```azurepowershell
(Get-AzureKeyVaultSecret -vaultName "Contosokeyvault" -name "ExamplePassword").SecretValueText
```

Nu har du skapat ett nyckelvalv, lagrat en hemlighet och hämtat den.

## <a name="clean-up-resources"></a>Rensa resurser

 De andra snabbstarterna och självstudierna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta med andra snabbstarter och självstudier kan du lämna kvar de här resurserna.

När du inte behöver resursgruppen längre kan du använda kommandot [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) till att ta bort resursgruppen, nyckelvalvet och alla relaterade resurser.

```azurepowershell
Remove-AzureRmResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett nyckelvalv och lagrat en programnyckel i det. Om du vill lära dig mer om Key Vault och hur du kan använda det med dina program fortsätter du till självstudien om att använda webbprogram med Key Vault.

> [!div class="nextstepaction"]
> Om du vill lära dig att läsa en hemlighet från Key Vault via en webbapp genom att använda hanterade tjänstidentiteter fortsätter du med självstudien [Konfigurera en Azure-webbapp att läsa en hemlighet från Key Vault](tutorial-web-application-keyvault.md)
