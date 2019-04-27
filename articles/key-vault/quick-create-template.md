---
title: Azure Snabbstart – skapa ett Azure key vault och en hemlighet med hjälp av Azure Resource Manager-mall | Microsoft Docs
description: Snabbstart som visar hur du skapar Azure-nyckelvalv och Lägg till hemligheter till valv med hjälp av Azure Resource Manager-mall.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/04/2019
ms.author: jgao
ms.openlocfilehash: 4b774e5b0a5c64a9af9a5a54ba264c6226558a24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60777760"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Snabbstart: Ange och hämta en hemlighet från Azure Key Vault med hjälp av Resource Manager-mall

[Azure Key Vault](./key-vault-overview.md) är en molnbaserad tjänst som tillhandahåller en säker lagring för hemligheter, t.ex nycklar, lösenord, certifikat, och andra hemligheter. Den här snabbstarten fokuserar på hur du distribuerar en Resource Manager-mall för att skapa ett nyckelvalv och en hemlighet. Mer information om hur du utvecklar Resource Manager-mallar finns i [Resource Manager-dokumentation](/azure/azure-resource-manager/) och [mallreferensen](/azure/templates/microsoft.keyvault/allversions).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa stegen i den här artikeln behöver du:

* Mallen behöver ditt användarobjekts-ID för Azure AD för att konfigurera behörigheter. Följande procedur hämtar objekt-ID:t (GUID).

    1. Kör följande kommando för Azure PowerShell eller Azure CLI genom att välja **prova**, och sedan klistra in skriptet i fönstret shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**. 

        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --upn-or-object-id $upn --query "objectId" 
        ```

        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        ```

    2. Anteckna objekt-ID:t. Du behöver det i nästa avsnitt i den här snabbstarten.

## <a name="create-a-vault-and-a-secret"></a>Skapa ett valv och en hemlighet

Den mall som användes i den här snabbstarten är från [Azure-snabbstartmallar](https://azure.microsoft.com/resources/templates/101-key-vault-create/). Mer Azure Key Vault-mallexempel finns [här](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett nyckelvalv och en hemlighet.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Välj eller ange följande värden.  

    ![Resource Manager-mall för distributionsportal för Key Vault-integrering](./media/quick-create-template/create-key-vault-using-template-portal.png)

    Om den har angetts kan du använda standardvärdet för att skapa nyckelvalvet och en hemlighet.

    * **Prenumeration**: välj en Azure-prenumeration.
    * **Resursgrupp**: Välj **Skapa nytt**, ange ett unikt namn för resursgruppen och klicka sedan på **OK**. 
    * **Plats**: välj en plats.  Välj till exempel **USA, centrala**.
    * **Namn på Key Vault**: Ange ett unikt namn för nyckelvalvet.  
    * **Klient-ID**: mallfunktionen hämtar ditt klient-ID automatiskt.  Ändra inte standardvärdet
    * **Användar-Id för AD**: Ange din Azure AD användarens objekt-ID som du hämtade från [krav](#prerequisites).
    * **Namn på hemlighet**: Ange ett namn för den hemlighet som du lagrar i nyckelvalvet.  Till exempel **adminpassword**
    * **Hemligt värde**: Ange värdet för hemligheten.  Om du sparar ett lösenord, rekommenderas att använda genererade lösenordet du skapade i krav.
    * **Jag godkänner villkoren ovan**: Välj.
3. Välj **Köp**.

## <a name="validate-the-deployment"></a>Verifiera distributionen

Du kan använda Azure-portalen för att kontrollera nyckelvalvet och hemligheten eller använder följande Azure CLI eller Azure PowerShell-skript för att lista hemlighet som har skapats.

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
När du inte behöver resursgruppen längre så tar du bort den. Då tas även nyckelvalvet och relaterade resurser bort. Ta bort resursgruppen med hjälp av Azure CLI eller Azure Powershell:

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
* [Dokumentation om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
* [Azure SDK för Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Azure REST API-referens](https://docs.microsoft.com/rest/api/keyvault/)
