---
title: Skapa en Azure blockchain service-medlem – Azure CLI
description: Skapa en Azure blockchain service-medlem för ett blockchain-konsortium med hjälp av Azure CLI.
ms.date: 03/30/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.openlocfilehash: 1561f485917ecbb64d51ffbe045ab4120fbf58ad
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116833"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Snabb start: skapa en Azure blockchain-tjänst blockchain-medlem med Azure CLI

I den här snabb starten distribuerar du en ny blockchain-medlem och konsortiet i Azure blockchain-tjänsten med hjälp av Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Inga.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att [https://shell.azure.com/bash](https://shell.azure.com/bash)gå till. Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Om du föredrar att installera och använda CLI lokalt kräver den här snabb starten Azure CLI version 2.0.51 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](https://docs.microsoft.com/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli-interactive
az group create \
                 --name myResourceGroup \
                 --location westus2
```

## <a name="create-a-blockchain-member"></a>Skapa en blockchain-medlem

En Azure blockchain-tjänst medlem är en blockchain-nod i ett privat konsortium blockchain-nätverk. När du konfigurerar en medlem kan du skapa eller ansluta till ett konsortiums nätverk. Du behöver minst en medlem för ett konsortiums nätverk. Antalet blockchain-medlemmar som krävs av deltagarna beror på ditt scenario. Konsortiet deltagare kan ha en eller flera blockchain-medlemmar eller de kan dela medlemmar med andra deltagare. Mer information om konsortier finns i [Azure blockchain service Consortium](consortium.md).

Det finns flera parametrar och egenskaper som du måste skicka. Ersätt exempel parametrarna med dina värden.

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
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser skapas. Använd den resurs grupp som du skapade i föregående avsnitt.
| **Namn** | Ett unikt namn som identifierar din Azure blockchain service blockchain-medlem. Namnet används för den offentliga slut punktens adress. Till exempel `myblockchainmember.blockchain.azure.com`.
| **sökvägen** | Azure-region där blockchain-medlemmen skapas. Till exempel `westus2`. Välj den plats som är närmast dina användare eller dina andra Azure-program.
| **lösenord** | Lösen ordet för medlemmens standard transaktions nod. Använd lösen ordet för grundläggande autentisering vid anslutning till blockchain-medlemmens offentliga standard transaktions nod.
| **Consortium** | Namnet på konsortiet att ansluta till eller skapa. Mer information om konsortier finns i [Azure blockchain service Consortium](consortium.md).
| **consortiumAccountPassword** | Konto lösen ordet för konsortiet kallas även medlems kontots lösen ord. Medlems kontots lösen ord används för att kryptera den privata nyckeln för det Ethereum-konto som skapas för din medlem. Du använder medlems kontot och medlems kontots lösen ord för hantering av konsortier.
| **skuName** | Nivå typ. Använd S0 för standard och B0 för Basic. Använd *Basic* -nivån för utveckling, testning och bevis på koncept. Använd *standard* nivån för distributioner av produktions nivåer. Du bör också använda *standard* nivån om du använder blockchain Data Manager eller när du skickar en stor mängd privata transaktioner. Det finns inte stöd för att ändra pris nivån mellan Basic och standard när medlems skapande har skapats.

Det tar cirka 10 minuter att skapa blockchain-medlemmen och de resurser som stöds.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda den blockchain-medlem som du skapade för nästa snabb start eller självstudier. När de inte längre behövs kan du ta bort resurserna genom att `myResourceGroup` ta bort resurs gruppen som du skapade för snabb starten.

Kör följande kommando för att ta bort resurs gruppen och alla relaterade resurser.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat en Azure blockchain service-medlem och ett nytt konsortium. Prova nästa snabb start för att använda Azure blockchain Development Kit för Ethereum för att ansluta till en Azure blockchain-tjänstemedlem.

> [!div class="nextstepaction"]
> [Använd Visual Studio Code för att ansluta till Azure blockchain-tjänsten](connect-vscode.md)
