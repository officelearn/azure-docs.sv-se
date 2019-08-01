---
title: Ansluta med Truffle
description: Ansluta till ett Azure blockchain service-nätverk med Truffle
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 9154bc749f7db337de67f501d5e5049dfd466156
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698474"
---
# <a name="quickstart-use-truffle-to-connect-to-an-azure-blockchain-service-network"></a>Snabbstart: Använda Truffle för att ansluta till ett Azure blockchain service-nätverk

Truffle är en blockchain utvecklings miljö som du kan använda för att ansluta till en Azure blockchain service-nod.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* [Skapa en Azure blockchain-medlem](create-member.md)
* Installera [Truffle](https://github.com/trufflesuite/truffle). Truffle kräver att flera verktyg installeras, inklusive [Node. js](https://nodejs.org), [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Installera [python-2.7.15](https://www.python.org/downloads/release/python-2715/). Python krävs för web3.
* Installera [Visual Studio Code](https://code.visualstudio.com/download).
* Installera [Visual Studio Code fastity-tillägg](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity).

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

### <a name="transaction-node-endpoint-addresses"></a>Slut punkts adresser för Transaction Node

1. I Azure Portal navigerar du till varje Transaction-nod och väljer **transaktions noder > anslutnings strängar**.
1. Kopiera och spara slut punkts-URL: en från **https (åtkomst nyckel 1)** för varje Transaction-nod. Du behöver slut punkts adresserna för konfigurations filen för smarta kontrakt senare i självstudien.

    ![Slut punkts adress för transaktion](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Redigera konfigurations fil

1. Starta Visual Studio Code och öppna mappen Truffle-projekt katalog med hjälp av menyn **fil > Öppna mapp** .
1. Öppna konfigurations filen `truffle-config.js`för Truffle.
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

1. Använd Truffle-konsolen för att ansluta till noden standard transaktion.

    ``` bash
    truffle console --network defaultnode
    ```

    Truffle ansluter till noden standard transaktion och tillhandahåller en interaktiv konsol.

    Du kan anropa metoder på **Web3** -objektet för att interagera med din Transaction-nod.

1. Anropa **getBlockNumber** -metoden för att returnera det aktuella block numret.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Exempel på utdata:

    ```bash
    truffle(defaultnode)> web3.eth.getBlockNumber();
    18567
    ```
1. Avsluta Truffle Development-konsolen.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett Truffle-projekt för att ansluta till din Azure blockchain-tjänst som standard transaktions nod.

Testa nästa självstudie om du vill använda Truffle för att skicka en transaktion till ditt konsortium blockchain-nätverk.

> [!div class="nextstepaction"]
> [Skicka en transaktion](send-transaction.md)
