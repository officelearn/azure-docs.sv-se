---
title: Create an Azure Blockchain Service member - Azure CLI
description: Create an Azure Blockchain Service member for a blockchain consortium using the Azure CLI.
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 6a9673431c3da21b3ce645b9aff30506be1012f3
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455959"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Quickstart: Create an Azure Blockchain Service blockchain member using Azure CLI

In this quickstart, you deploy a new blockchain member and consortium in Azure Blockchain Service using Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i en separat webbläsarflik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash). Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

If you prefer to install and use the CLI locally, this quickstart requires Azure CLI version 2.0.51 or later. Kör `az --version` för att hitta versionen. If you need to install or upgrade, see [install Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](https://docs.microsoft.com/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive
az group create \
                 --name myResourceGroup \
                 --location westus2
```

## <a name="create-a-blockchain-member"></a>Skapa en blockkedjemedlem

Create a blockchain member in Azure Blockchain Service that runs the Quorum ledger protocol in a new consortium. There are several parameters and properties you need to pass. Replace the example parameters with your values.

```azurecli-interactive
az resource create \
                    --resource-group myResourceGroup \
                    --name myblockchainmember \
                    --resource-type Microsoft.Blockchain/blockchainMembers \
                    --is-full-object \
                    --properties '{"location":"westus2", "properties":{"password":"strongMemberAccountPassword@1", "protocol":"Quorum", "consortium":"myConsortiumName", "consortiumManagementAccountPassword":"strongConsortiumManagementPassword@1"}, "sku":{"name":"S0"}}'
```

| Parameter | Beskrivning |
|---------|-------------|
| **resource-group** | Resource group name where Azure Blockchain Service resources are created. Use the resource group you created in the previous section.
| **name** | A unique name that identifies your Azure Blockchain Service blockchain member. The name is used for the public endpoint address. Till exempel `myblockchainmember.blockchain.azure.com`.
| **Plats** | Azure region where the blockchain member is created. Till exempel `westus2`. Välj den plats som är närmast dina användare eller dina andra Azure-program.
| **password** | The password for the member's default transaction node. Use the password for basic authentication when connecting to blockchain member's default transaction node public endpoint.
| **consortium** | Name of the consortium to join or create.
| **consortiumAccountPassword** | The consortium account password is also known as the member account password. The member account password is used to encrypt the private key for the Ethereum account that is created for your member. You use the member account and member account password for consortium management.
| **skuName** | Tier type. Use S0 for Standard and B0 for Basic.

It takes about 10 minutes to create the blockchain member and supporting resources.

## <a name="clean-up-resources"></a>Rensa resurser

You can use the blockchain member you created for the next quickstart or tutorial. When no longer needed, you can delete the resources by deleting the `myResourceGroup` resource group you created for the quickstart.

Run the following command to remove the resource group and all related resources.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Nästa steg

In this quickstart, you deployed an Azure Blockchain Service member and a new consortium. Try the next quickstart to use  Azure Blockchain Development Kit for Ethereum to attach to a consortium on Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Use Visual Studio Code to connect to Azure Blockchain Service](connect-vscode.md)
