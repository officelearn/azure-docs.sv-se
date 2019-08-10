---
title: Azure snabb start – skapa ett Azure Key Vault och en hemlighet med hjälp av Azure Resource Manager mall | Microsoft Docs
description: Snabb start visar hur du skapar Azure Key Vault och lägger till hemligheter i valven med hjälp av Azure Resource Manager mall.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 05/22/2019
ms.author: jgao
ms.openlocfilehash: b27caa3d91d67ad63bfbf5e7c549d690980cdd7b
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934444"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Snabbstart: Ange och hämta en hemlighet från Azure Key Vault med Resource Manager-mall

[Azure Key Vault](./key-vault-overview.md) är en moln tjänst som ger en säker lagring för hemligheter, till exempel nycklar, lösen ord, certifikat och andra hemligheter. Den här snabb starten fokuserar på processen att distribuera en Resource Manager-mall för att skapa ett nyckel valv och en hemlighet. Mer information om hur du utvecklar Resource Manager-mallar finns i [Resource Manager-dokumentation](/azure/azure-resource-manager/) och [mallen referens](/azure/templates/microsoft.keyvault/allversions).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa stegen i den här artikeln behöver du:

* Mallen behöver ditt användarobjekts-ID för Azure AD för att konfigurera behörigheter. Följande procedur hämtar objekt-ID:t (GUID).

    1. Kör följande Azure PowerShell-eller Azure CLI-kommando genom att välja **testa**och klistra in skriptet i rutan Shell. Om du vill klistra in skriptet högerklickar du på gränssnittet och väljer **Klistra in**. 

        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --upn-or-object-id $upn --query "objectId" 
        ```

        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        ```

    2. Anteckna objekt-ID:t. Du behöver det i nästa avsnitt i den här snabb starten.

## <a name="create-a-vault-and-a-secret"></a>Skapa ett valv och en hemlighet

Mallen som används i den här snabb starten är från [Azure snabb starts-mallar](https://azure.microsoft.com/resources/templates/101-key-vault-create/). Fler Azure Key Vault mall-exempel finns [här](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett nyckelvalv och en hemlighet.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Välj eller ange följande värden.  

    ![Resource Manager-mall för distributionsportal för Key Vault-integrering](./media/quick-create-template/create-key-vault-using-template-portal.png)

    Om den inte anges använder du standardvärdet för att skapa nyckel valvet och en hemlighet.

    * **Prenumeration**: välj en Azure-prenumeration.
    * **Resurs grupp**: Välj **Skapa ny**, ange ett unikt namn för resurs gruppen och klicka sedan på **OK**. 
    * **Plats**: välj en plats.  Välj till exempel **USA, centrala**.
    * **Key Vault namn**: Ange ett namn för nyckel valvet som måste vara globalt unikt inom namn området. Vault.Azure.net.  
    * **Klient-ID**: mallfunktionen hämtar ditt klient-ID automatiskt.  Ändra inte standardvärdet.
    * **AD-användar-ID**: Ange ditt användar objekt-ID för Azure AD som du hämtade från [krav](#prerequisites).
    * **Hemligt namn**: Ange ett namn för hemligheten som du lagrar i nyckel valvet.  Till exempel **AdminPassword**.
    * **Hemligt värde**: Ange det hemliga värdet.  Om du lagrar ett lösen ord rekommenderar vi att du använder det genererade lösen ordet som du skapade i krav.
    * **Jag godkänner villkoren ovan**: Välj.
3. Välj **Köp**.

## <a name="validate-the-deployment"></a>Verifiera distributionen

Du kan antingen använda Azure Portal för att kontrol lera nyckel valvet och hemligheten, eller använda följande Azure CLI-eller Azure PowerShell skript för att visa en lista över de hemliga dem som skapats.

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName
```

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
```

## <a name="clean-up-resources"></a>Rensa resurser

Andra snabbstarter och självstudier om Key Vault bygger på den här snabbstarten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.
När du inte behöver resursgruppen längre så tar du bort den. Då tas även nyckelvalvet och relaterade resurser bort. Ta bort resurs gruppen med hjälp av Azure CLI eller Azure PowerShell:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName 
```
```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName 
```

## <a name="next-steps"></a>Nästa steg

* [Startsida för Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Dokumentation om Azure Key Vault](index.yml)
* [Azure SDK för Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Azure REST API-referens](https://docs.microsoft.com/rest/api/keyvault/)
