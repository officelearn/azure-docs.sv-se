---
title: Azure Quickstart – Skapa ett Azure-nyckelvalv och en hemlighet med hjälp av Azure Resource Manager-mallen | Microsoft-dokument
description: Snabbstart som visar hur du skapar Azure-nyckelvalv och lägger till hemligheter i valven med hjälp av Azure Resource Manager-mallen.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 02/27/2020
ms.author: jgao
ms.openlocfilehash: 273a467f5db2201015352aaf4a232f5a42e29673
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618093"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Snabbstart: Ange och hämta en hemlighet från Azure Key Vault med hjälp av Resource Manager-mall

[Azure Key Vault](../general/overview.md) är en molntjänst som tillhandahåller ett säkert arkiv för hemligheter, till exempel nycklar, lösenord, certifikat och andra hemligheter. Den här snabbstarten fokuserar på hur du distribuerar en Resource Manager-mall för att skapa ett nyckelvalv och en hemlighet.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Mallen behöver ditt användarobjekts-ID för Azure AD för att konfigurera behörigheter. Följande procedur hämtar objekt-ID:t (GUID).

    1. Kör följande Azure PowerShell- eller Azure CLI-kommando genom att välja **Prova det**och klistra sedan in skriptet i skalfönstret. Om du vill klistra in skriptet högerklickar du på skalet och väljer sedan **Klistra in**.

        # <a name="cli"></a>[CLI](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. Anteckna objekt-ID:t. Du behöver det i nästa avsnitt av den här snabbstarten.

## <a name="create-a-vault-and-a-secret"></a>Skapa ett valv och en hemlighet

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure Quickstart-mallar](https://azure.microsoft.com/resources/templates/101-key-vault-create/).

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json" range="1-150" highlight="107-148":::

Två Azure-resurser definieras i mallen:

* [**Microsoft.KeyVault/valv**](/azure/templates/microsoft.keyvault/vaults): skapa ett Azure-nyckelvalv.
* [**Microsoft.KeyVault/vaults/secrets**](/azure/templates/microsoft.keyvault/vaults/secrets): skapa en nyckelvalvshemlighet.

Fler azure key vault-mallexempel hittar du [här](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

### <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett nyckelvalv och en hemlighet.

    [![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. Välj eller ange följande värden.

    ![Resource Manager-mall, Key Vault-integrering, distributionsportal](../media/quick-create-template/create-key-vault-using-template-portal.png)

    Om det inte anges använder du standardvärdet för att skapa nyckelvalvet och en hemlighet.

    * **Prenumeration:** välj en Azure-prenumeration.
    * **Resursgrupp:** Välj **Skapa nytt,** ange ett unikt namn för resursgruppen och klicka sedan på **OK**.
    * **Plats**: välj en plats.  Välj till exempel **USA, centrala**.
    * **Nyckelvalvnamn:** Ange ett namn för nyckelvalvet, som måste vara globalt unikt inom .vault.azure.net namnområde. Du behöver namnet i nästa avsnitt när du validerar distributionen.
    * **Klient-ID:** Mallfunktionen hämtar automatiskt ditt klient-ID.  Ändra inte standardvärdet.
    * **Annonsanvändar-ID:** Ange ditt Azure AD-användarobjekt-ID som du hämtade från [förutsättningar](#prerequisites).
    * **Hemligt namn:** ange ett namn på hemligheten som du lagrar i nyckelvalvet.  Till exempel **adminpassword**.
    * **Hemligt värde:** ange det hemliga värdet.  Om du lagrar ett lösenord rekommenderar vi att du använder det genererade lösenordet som du skapade i Förutsättningar.
    * **Jag godkänner villkoren ovan**: Välj.
3. Välj **Köp**. När nyckelvalvet har distribuerats får du ett meddelande:

    ![Resource Manager-mall, Key Vault-integrering, distribuera portalmeddelanden](../media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Azure-portalen används för att distribuera mallen. Förutom Azure-portalen kan du även använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributionsmetoder finns i [Distribuera mallar](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Du kan antingen använda Azure-portalen för att kontrollera nyckelvalvet och hemligheten, eller använda följande Azure CLI- eller Azure PowerShell-skript för att lista det hemliga som skapats.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

Utdata liknar:

# <a name="cli"></a>[CLI](#tab/CLI)

![Resource Manager-mall, Key Vault-integrering, distribuera portalvalideringsutdata](../media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![Resource Manager-mall, Key Vault-integrering, distribuera portalvalideringsutdata](../media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---
## <a name="clean-up-resources"></a>Rensa resurser

Andra snabbstarter och självstudier om Key Vault bygger på den här snabbstarten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.
När du inte behöver resursgruppen längre så tar du bort den. Då tas även nyckelvalvet och relaterade resurser bort. Så här tar du bort resursgruppen med hjälp av Azure CLI eller Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett nyckelvalv och en hemlighet med hjälp av en Azure Resource Manager-mall och validerade distributionen. Om du vill veta mer om Key Vault och Azure Resource Manager fortsätter du till artiklarna nedan.

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Läs mer om [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Granska [metodtips för Azure Key Vault](../general/best-practices.md)
