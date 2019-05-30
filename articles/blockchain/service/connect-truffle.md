---
title: Ansluta med Truffle
description: Ansluta till ett nätverk för Azure Blockchain-tjänsten med hjälp av Truffle
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 692bac0e0474ead7a0acd0887c3c10055edf8553
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399990"
---
# <a name="quickstart-use-truffle-to-connect-to-a-an-azure-blockchain-service-network"></a>Snabbstart: Använd Truffle för att ansluta till en tjänstnätverk Azure Blockchain

Truffle är en blockchain-utvecklingsmiljö som du kan använda för att ansluta till en Azure Blockchain Service-nod.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Skapa en medlem i Azure Blockchain](create-member.md)
* Installera [Truffle](https://github.com/trufflesuite/truffle). Truffle kräver flera verktyg installeras inklusive [Node.js](https://nodejs.org), [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Installera [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python krävs för Web3.

## <a name="create-truffle-project"></a>Skapa Truffle projekt

1. Öppna en kommandotolk för Node.js eller shell.
1. Gå till katalogen där du vill skapa Truffle projektkatalogen.
1. Skapa en katalog för projektet och ändra sökvägen till den nya katalogen. Exempel:

    ``` bash
    mkdir truffledemo
    cd truffledemo
    ```

1. Initiera Truffle-projektet.

    ``` bash
    truffle init
    ```

1. Installera Ethereum JavaScript API web3 i projektmappen. För närvarande krävs version web3 version 1.0.0-beta.37.

    ``` bash
    npm install web3@1.0.0-beta.37
    ```

    Du kan få npm varningar under installationen.

1. Starta Truffle interaktiv distribuering konsol.

    ``` bash
    truffle develop
    ```

    Truffle skapar en lokal utveckling blockchain och en interaktiv konsol.

## <a name="connect-to-transaction-node"></a>Ansluta till transaktionsnod

Använd *Web3* att ansluta till noden transaktion. Du kan hämta den *Web3* anslutningssträngen från Azure-portalen.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Gå till din Azure Blockchain Service medlem. Välj **transaktion noder** och länken standard transaktion noden.

    ![Välj standard transaktion nod](./media/connect-truffle/transaction-nodes.png)

1. Välj **exempelkoden > Web3**.
1. Kopiera JavaScript från **HTTPS (åtkomstnyckel 1)** . Du måste koden för Truffles interaktiv distribuering-konsolen.

    ![Web3 kod](./media/connect-truffle/web3-code.png)

1. Klistra in JavaScript-koden från föregående steg i konsolen Truffle interaktiv distribuering. Koden skapar ett web3-objekt som är ansluten till din Azure Blockchain Service transaktion nod.

    Exempel på utdata:

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    ```

    Du kan anropa metoder på det **web3** objekt för att interagera med din nod för transaktionen.

1. Anropa den **getBlockNumber** metod för att returnera den aktuella blocknummer.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Exempel på utdata:

    ```bash
    truffle(develop)> web3.eth.getBlockNumber();
    18567
    ```
1. Avsluta Truffle utveckling-konsolen.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har skapat du ett Truffle-projekt för att ansluta till noden Azure Blockchain Service standard transaktion.

Prova nästa självstudie om du vill använda Truffle för att skicka en transaktion till nätverket consortium blockchain.

> [!div class="nextstepaction"]
> [Skicka en transaktion](send-transaction.md)