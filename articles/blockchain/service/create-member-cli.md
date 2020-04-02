---
title: Skapa en Azure Blockchain-tjänstmedlem - Azure CLI
description: Skapa en Azure Blockchain Service-medlem för ett blockchain-konsortium med Hjälp av Azure CLI.
ms.date: 03/30/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.openlocfilehash: 322b1884726b6dfe322560032ed1b8a98c600154
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529627"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Snabbstart: Skapa en Blockchain-tjänstmedlem för Azure Blockchain-tjänst med Azure CLI

I den här snabbstarten distribuerar du en ny blockchain-medlem och konsortium i Azure Blockchain Service med Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i [https://shell.azure.com/bash](https://shell.azure.com/bash)en separat webbläsarflik genom att gå till . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Om du föredrar att installera och använda CLI lokalt kräver den här snabbstarten Azure CLI version 2.0.51 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera läser du [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](https://docs.microsoft.com/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

```azurecli-interactive
az group create \
                 --name myResourceGroup \
                 --location westus2
```

## <a name="create-a-blockchain-member"></a>Skapa en blockchain-medlem

En Azure Blockchain Service-medlem är en blockchain-nod i ett privat konsortium blockchain-nätverk. När du etablerar en medlem kan du skapa eller gå med i ett konsortiumnätverk. Du behöver minst en medlem för ett konsortiumnätverk. Antalet blockchain-medlemmar som deltagarna behöver beror på ditt scenario. Konsortiedeltagare kan ha en eller flera blockchain-medlemmar eller så kan de dela medlemmar med andra deltagare. Mer information om konsortier finns i [Azure Blockchain Service consortium](consortium.md).

Det finns flera parametrar och egenskaper som du behöver passera. Ersätt exempelparametrarna med dina värden.

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
| **resursgrupp** | Resursgruppsnamn där Azure Blockchain Service-resurser skapas. Använd resursgruppen som du skapade i föregående avsnitt.
| **Namn** | Ett unikt namn som identifierar din Azure Blockchain Service blockchain-medlem. Namnet används för den offentliga slutpunktsadressen. Till exempel `myblockchainmember.blockchain.azure.com`.
| **Plats** | Azure-region där blockchain-medlemmen skapas. Till exempel `westus2`. Välj den plats som är närmast dina användare eller dina andra Azure-program.
| **lösenord** | Lösenordet för medlemmens standardtransaktionsnod. Använd lösenordet för grundläggande autentisering när du ansluter till blockchain-medlemmens offentliga slutpunkt för standardtransaktionsnod.
| **Konsortium** | Namn på konsortiet att gå med i eller skapa. Mer information om konsortier finns i [Azure Blockchain Service consortium](consortium.md).
| **konsortietOrd** | Lösenordet för konsortiekontot kallas även medlemskontots lösenord. Medlemskontots lösenord används för att kryptera den privata nyckeln för Ethereum-kontot som skapas för din medlem. Du använder medlemskontot och medlemskontots lösenord för konsortiehantering.
| **skuNamn** | Nivåtyp. Använd S0 för Standard och B0 för Basic. Använd *basic-nivån* för utveckling, testning och bevis på begrepp. Använd *standardnivån* för distributioner av produktionsresultat. Du bör också använda *standardnivån* om du använder Blockchain Data Manager eller skickar en stor mängd privata transaktioner. Det går inte att ändra prisnivån mellan grundläggande och standard när medlemmen har skapats.

Det tar cirka 10 minuter att skapa blockchain-medlemmen och stödja resurser.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda blockchain-medlemmen som du skapade för nästa snabbstart eller självstudiekurs. När du inte längre behövs kan du `myResourceGroup` ta bort resurserna genom att ta bort resursgruppen som du skapade för snabbstarten.

Kör följande kommando för att ta bort resursgruppen och alla relaterade resurser.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat en Azure Blockchain Service-medlem och ett nytt konsortium. Prova nästa snabbstart för att använda Azure Blockchain Development Kit för Ethereum för att ansluta till en Azure Blockchain Service-medlem.

> [!div class="nextstepaction"]
> [Använda Visual Studio-kod för att ansluta till Azure Blockchain Service](connect-vscode.md)
