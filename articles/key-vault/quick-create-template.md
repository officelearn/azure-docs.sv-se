---
title: Azure snabb start – skapa ett Azure Key Vault och en hemlighet med hjälp av Azure Resource Manager mall | Microsoft Docs
description: Snabb start visar hur du skapar Azure Key Vault och lägger till hemligheter i valven med hjälp av Azure Resource Manager mall.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/27/2020
ms.author: jgao
ms.openlocfilehash: 2e4e812ec13c089508fea890df2af1f8372bbe37
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190423"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Snabb start: Ange och hämta en hemlighet från Azure Key Vault med Resource Manager-mall

[Azure Key Vault](./key-vault-overview.md) är en moln tjänst som ger en säker lagring för hemligheter, till exempel nycklar, lösen ord, certifikat och andra hemligheter. Den här snabb starten fokuserar på processen att distribuera en Resource Manager-mall för att skapa ett nyckel valv och en hemlighet.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa stegen i den här artikeln behöver du:

* Mallen behöver ditt användarobjekts-ID för Azure AD för att konfigurera behörigheter. Följande procedur hämtar objekt-ID:t (GUID).

    1. Kör följande Azure PowerShell-eller Azure CLI-kommando genom att välja **testa**och klistra in skriptet i rutan Shell. Om du vill klistra in skriptet högerklickar du på gränssnittet och väljer **Klistra in**.

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

    2. Anteckna objekt-ID:t. Du behöver det i nästa avsnitt i den här snabb starten.

## <a name="create-a-vault-and-a-secret"></a>Skapa ett valv och en hemlighet

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure snabb starts-mallar](https://azure.microsoft.com/resources/templates/101-key-vault-create/).

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json" range="1-150" highlight="107-148":::

Två Azure-resurser definieras i mallen:

* [**Microsoft. Key Vault/valv**](/azure/templates/microsoft.keyvault/vaults): skapa ett Azure Key Vault.
* [**Microsoft. Key Vault/valv/hemligheter**](/azure/templates/microsoft.keyvault/vaults/secrets): skapa en nyckel valvs hemlighet.

Fler Azure Key Vault mall-exempel finns [här](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

### <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett nyckelvalv och en hemlighet.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Välj eller ange följande värden.

    ![Resource Manager-mall, Key Vault integrering, distribuera Portal](./media/quick-create-template/create-key-vault-using-template-portal.png)

    Om den inte anges använder du standardvärdet för att skapa nyckel valvet och en hemlighet.

    * **Prenumeration**: välj en Azure-prenumeration.
    * **Resurs grupp**: Välj **Skapa ny**, ange ett unikt namn för resurs gruppen och klicka sedan på **OK**.
    * **Plats**: välj en plats.  Välj till exempel **USA, centrala**.
    * **Key Vault namn**: Ange ett namn för nyckel valvet som måste vara globalt unikt inom namn området. Vault.Azure.net. Du behöver namnet i nästa avsnitt när du validerar distributionen.
    * **Klient-ID**: funktionen mall hämtar automatiskt ditt klient-ID.  Ändra inte standardvärdet.
    * **AD-användar-ID**: Ange ditt användar objekt-ID för Azure AD som du hämtade från [krav](#prerequisites).
    * **Hemligt namn**: Ange ett namn för hemligheten som du lagrar i nyckel valvet.  Till exempel **AdminPassword**.
    * **Hemligt värde**: Ange det hemliga värdet.  Om du lagrar ett lösen ord rekommenderar vi att du använder det genererade lösen ordet som du skapade i krav.
    * **Jag godkänner villkoren ovan**: Välj.
3. Välj **Köp**. När nyckel valvet har distribuerats får du ett meddelande:

    ![Resource Manager-mall, Key Vault integrering, distribuera Portal meddelande](./media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Azure Portal används för att distribuera mallen. Förutom Azure Portal kan du också använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Du kan antingen använda Azure Portal för att kontrol lera nyckel valvet och hemligheten, eller använda följande Azure CLI-eller Azure PowerShell skript för att visa en lista över de hemliga dem som skapats.

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

Utdata ser ut ungefär så här:

# <a name="cli"></a>[CLI](#tab/CLI)

![Resource Manager-mall, Key Vault-integrering, distribuera Portal validering av utdata](./media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![Resource Manager-mall, Key Vault-integrering, distribuera Portal validering av utdata](./media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---
## <a name="clean-up-resources"></a>Rensa resurser

Andra snabbstarter och självstudier om Key Vault bygger på den här snabbstarten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.
När du inte behöver resursgruppen längre så tar du bort den. Då tas även nyckelvalvet och relaterade resurser bort. Ta bort resurs gruppen med hjälp av Azure CLI eller Azure PowerShell:

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

I den här snabb starten skapade du ett nyckel valv och en hemlighet med hjälp av en Azure Resource Manager-mall och verifierade distributionen. Om du vill veta mer om Key Vault och Azure Resource Manager fortsätter du till artiklarna nedan.

- Läs en [Översikt över Azure Key Vault](key-vault-overview.md)
- Läs mer om [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Få mer information om [nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
- Granska [Azure Key Vault bästa praxis](key-vault-best-practices.md)
