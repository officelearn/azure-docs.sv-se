---
title: Använda Truffle för att ansluta till Azure blockchain-tjänsten
description: Ansluta till ett Azure blockchain service-nätverk med Truffle
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 9ea96aa32072775fe6fb9563442f5e3564d37ea5
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455790"
---
# <a name="quickstart-use-truffle-to-connect-to-azure-blockchain-service"></a>Snabb start: använda Truffle för att ansluta till Azure blockchain-tjänsten

I den här snabb starten använder du Truffle Anslut till en Azure blockchain service Transaction-nod. Sedan använder du den interaktiva konsolen Truffle för att anropa **Web3** -metoder för att interagera med ditt blockchain-nätverk.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Slutför [snabb start: skapa en blockchain-medlem med hjälp av Azure Portal](create-member.md) eller [snabb start: skapa en Azure blockchain service blockchain-medlem med Azure CLI](create-member-cli.md)
* Installera [Truffle](https://github.com/trufflesuite/truffle). Truffle kräver att flera verktyg installeras, inklusive [Node. js](https://nodejs.org), [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Installera [python-2.7.15](https://www.python.org/downloads/release/python-2715/). Python krävs för web3.

## <a name="create-truffle-project"></a>Skapa Truffle-projekt

1. Öppna en Node. js-kommandotolk eller Shell.
1. Ändra katalogen till den plats där du vill skapa Truffle-projekt katalogen.
1. Skapa en katalog för projektet och ändra sökvägen till den nya katalogen. Exempel:

    ``` bash
    mkdir truffledemo
    cd truffledemo
    ```

1. Initiera Truffle-projektet.

    ``` bash
    truffle init
    ```

1. Installera Ethereum Java Script API Web3 i projektmappen. För närvarande krävs version Web3 version 1.0.0 – beta. 37.

    ``` bash
    npm install web3@1.0.0-beta.37
    ```

    Du kan få NPM-varningar under installationen.
    
## <a name="configure-truffle-project"></a>Konfigurera Truffle-projekt

Om du vill konfigurera Truffle-projektet behöver du en del transaktionsinformation från Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till din Azure blockchain service-medlem. Välj **Transactions-noder** och noden standard transaktions nod.

    ![Välj noden standard transaktion](./media/connect-truffle/transaction-nodes.png)

1. Välj **anslutnings strängar**.
1. Kopiera anslutnings strängen från **https (åtkomst nyckel 1)** . Du behöver strängen för nästa avsnitt.

    ![Anslutningssträng](./media/connect-truffle/connection-string.png)

### <a name="edit-configuration-file"></a>Redigera konfigurations fil

Sedan måste du uppdatera konfigurations filen för Truffle med Transaction Node-slutpunkten.

1. Öppna Truffle-konfigurationsfilen `truffle-config.js` i en redigerare i mappen **truffledemo** -projekt.
1. Ersätt innehållet i filen med följande konfigurations information. Lägg till en variabel som innehåller slut punkts adressen. Ersätt vinkel paren med värden som du har samlat in från föregående avsnitt.

    ``` javascript
    var defaultnode = "<default transaction node connection string>";   
    var Web3 = require("web3");
    
    module.exports = {
      networks: {
        defaultnode: {
          provider: new Web3.providers.HttpProvider(defaultnode),
          network_id: "*"
        }
      }
    }
    ```

1. Spara ändringarna i `truffle-config.js`.

## <a name="connect-to-transaction-node"></a>Ansluta till transaktionsnod

Använd *Web3* för att ansluta till Transaction-noden.

1. Använd Truffle-konsolen för att ansluta till noden standard transaktion. Kör följande kommando i en kommando tolk eller ett gränssnitt:

    ``` bash
    truffle console --network defaultnode
    ```

    Truffle ansluter till noden standard transaktion och tillhandahåller en interaktiv konsol.

    Du kan anropa metoder på **Web3** -objektet för att interagera med ditt blockchain-nätverk.

1. Anropa **getBlockNumber** -metoden för att returnera det aktuella block numret.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Exempel på utdata:

    ```bash
    truffle(defaultnode)> web3.eth.getBlockNumber();
    18567
    ```
1. Avsluta Truffle-konsolen.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du Truffle Anslut till en Azure blockchain service-standardnod och använde den interaktiva konsolen för att returnera det aktuella blockchain block-numret.

Testa nästa självstudie för att använda Azure blockchain Development Kit för Ethereum för att skapa, bygga, distribuera och köra en smart kontrakts funktion via en transaktion.

> [!div class="nextstepaction"]
> [Skapa, skapa och distribuera smarta avtal i Azure blockchain-tjänsten](send-transaction.md)
