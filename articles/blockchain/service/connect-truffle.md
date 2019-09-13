---
title: Ansluta med Truffle
description: Ansluta till ett Azure blockchain service-nätverk med Truffle
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 08/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: ea64e3fe4789ac0558463ded81c8179db83469d5
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932583"
---
# <a name="quickstart-use-truffle-to-connect-to-a-transaction-node"></a>Snabbstart: Använda Truffle för att ansluta till en transaktionsnod

Truffle är en blockchain utvecklings miljö som du kan använda för att ansluta till en Azure blockchain service Transaction-nod.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Slutför [snabb start: Skapa en blockchain-medlem med hjälp](create-member.md) av [Azure Portal eller snabb start: Skapa en Azure blockchain-tjänst blockchain-medlem med Azure CLI](create-member-cli.md)
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

### <a name="transaction-node-endpoint-addresses"></a>Slut punkts adresser för Transaction Node

1. I Azure Portal navigerar du till noden standardtransaktion och väljer Transaction Nodes **> anslutnings strängar**.
1. Kopiera och spara slut punkts-URL: en från **https (åtkomst nyckel 1)** . Du behöver slut punkts adresserna för konfigurations filen för smarta kontrakt senare i självstudien.

    ![Slut punkts adress för transaktion](./media/connect-truffle/endpoint.png)

### <a name="edit-configuration-file"></a>Redigera konfigurations fil

Sedan måste du uppdatera konfigurations filen för Truffle med Transaction Node-slutpunkten.

1. Öppna konfigurations filen `truffle-config.js` Truffle i en redigerare i **truffledemo** -projektmappen.
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
1. Avsluta Truffle-konsolen.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett Truffle-projekt för att ansluta till din Azure blockchain-tjänst som standard transaktions nod.

Testa nästa självstudie för att använda Azure blockchain Development Kit för Ethereum och Truffle för att skapa, bygga, distribuera och köra en smart kontrakts funktion via en transaktion.

> [!div class="nextstepaction"]
> [Använd Visual Studio Code för att skapa, bygga och distribuera smarta kontrakt](send-transaction.md)
