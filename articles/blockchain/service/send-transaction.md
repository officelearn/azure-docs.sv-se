---
title: Skicka transaktioner med Azure blockchain-tjänsten
description: Själv studie kurs om hur du använder Azure blockchain-tjänsten för att distribuera ett smart kontrakt och skicka en privat transaktion.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 3cfbbdc5b95d1607738b132980320d2ff7c99788
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698389"
---
# <a name="tutorial-send-transactions-using-azure-blockchain-service"></a>Självstudier: Skicka transaktioner med Azure blockchain-tjänsten

I den här självstudien skapar du transaktions noder för att testa kontrakt och transaktions sekretess.  Du använder Truffle för att skapa en lokal utvecklings miljö och distribuera ett smart kontrakt och skicka en privat transaktion.

Du lär dig följande:

> [!div class="checklist"]
> * Lägg till Transaction Nodes
> * Använda Truffle för att distribuera ett smart kontrakt
> * Skicka en transaktion
> * Validera transaktions sekretess

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Slutför [skapa en blockchain-medlem med hjälp av Azure Portal](create-member.md)
* Slutför [snabb start: Använda Truffle för att ansluta till ett konsortiums nätverk](connect-truffle.md)
* Installera [Truffle](https://github.com/trufflesuite/truffle). Truffle kräver att flera verktyg installeras, inklusive [Node. js](https://nodejs.org), [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Installera [python-2.7.15](https://www.python.org/downloads/release/python-2715/). Python krävs för web3.
* Installera [Visual Studio Code](https://code.visualstudio.com/Download)
* Installera [tillägg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity) engradion

## <a name="create-transaction-nodes"></a>Skapa Transaction Nodes

Som standard har du en transaktions nod. Vi ska lägga till ytterligare två. En av noderna ingår i den privata transaktionen. Den andra ingår inte i den privata transaktionen.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Navigera till din Azure blockchain-medlem och välj **Transaction nodes > Lägg till**.
1. Slutför inställningarna för en ny Transaction-nod med `alpha`namnet.

    ![Skapa Transaction-nod](./media/send-transaction/create-node.png)

    | Inställning | Värde | Beskrivning |
    |---------|-------|-------------|
    | Namn | `alpha` | Nodnamn för transaktion. Namnet används för att skapa DNS-adressen för Transaction Node-slutpunkten. Till exempel `alpha-mymanagedledger.blockchain.azure.com`. |
    | lösenordsinställning | Starkt lösen ord | Lösen ordet används för att komma åt Transaction Node-slutpunkten med grundläggande autentisering.

1. Välj **Skapa**.

    Det tar cirka 10 minuter att tillhandahålla en ny transaktions nod.

1. Upprepa steg 2 till 4 för att lägga till en Transaction `beta`-nod med namnet.

Du kan fortsätta med självstudien när noderna har allokerats. När etableringen har slutförts har du tre transaktions noder.

## <a name="open-truffle-console"></a>Öppna Truffle-konsolen

1. Öppna en Node. js-kommandotolk eller Shell.
1. Ändra sökvägen till Truffle-projekt katalogen från den nödvändiga [snabb starten: Använd Truffle för att ansluta till ett konsortiums nätverk](connect-truffle.md). Exempel:

    ```bash
    cd truffledemo
    ```

1. Använd Truffle-konsolen för att ansluta till noden standard transaktion.

    ``` bash
    truffle console --network defaultnode
    ```

    Truffle ansluter till noden standard transaktion och tillhandahåller en interaktiv konsol.

## <a name="create-ethereum-account"></a>Skapa Ethereum-konto

Använd Web3 för att ansluta till noden standard transaktion och skapa ett Ethereum-konto. Du kan anropa metoder på Web3-objektet för att interagera med din Transaction-nod.

1. Skapa ett nytt konto på noden standard transaktion. Ersätt parametern Password med ditt eget starka lösen ord.

    ```bash
    web3.eth.personal.newAccount("1@myStrongPassword");
    ```

    Anteckna konto adressen som returneras och lösen ordet. Du behöver Ethereum-kontots adress och lösen ord i nästa avsnitt.

1. Avsluta Truffle Development Environment.

    ```bash
    .exit
    ```

## <a name="configure-truffle-project"></a>Konfigurera Truffle-projekt

Om du vill konfigurera Truffle-projektet behöver du en del transaktionsinformation från Azure Portal.

### <a name="transaction-node-public-key"></a>Offentlig nyckel för Transaction Node

Varje Transaction-nod har en offentlig nyckel. Med den offentliga nyckeln kan du skicka en privat transaktion till noden. För att kunna skicka en transaktion från noden standard transaktion till noden för *alpha* -transaktioner, behöver du noden för noden med *alpha* -transaktioner.

Du kan hämta den offentliga nyckeln från listan över transaktions noder. Kopiera den offentliga nyckeln för alpha-noden och spara värdet för senare i självstudien.

![Lista över transaktions noder](./media/send-transaction/node-list.png)

### <a name="transaction-node-endpoint-addresses"></a>Slut punkts adresser för Transaction Node

1. I Azure Portal navigerar du till varje Transaction-nod och väljer **transaktions noder > anslutnings strängar**.
1. Kopiera och spara slut punkts-URL: en från **https (åtkomst nyckel 1)** för varje Transaction-nod. Du behöver slut punkts adresserna för konfigurations filen för smarta kontrakt senare i självstudien.

    ![Slut punkts adress för transaktion](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Redigera konfigurations fil

1. Starta Visual Studio Code och öppna mappen Truffle-projekt katalog med hjälp av menyn **fil > Öppna mapp** .
1. Öppna konfigurations filen `truffle-config.js`för Truffle.
1. Ersätt innehållet i filen med följande konfigurations information. Lägg till variabler som innehåller slut punkts adresserna och konto informationen. Ersätt avsnittet vinkel paren tes med värden som du har samlat in från föregående avsnitt.

    ``` javascript
    var defaultnode = "<default transaction node connection string>";
    var alpha = "<alpha transaction node connection string>";
    var beta = "<beta transaction node connection string>";
    
    var myAccount = "<Ethereum account address>";
    var myPassword = "<Ethereum account password>";
    
    var Web3 = require("web3");
    
    module.exports = {
      networks: {
        defaultnode: {
          provider:(() =>  {
          const AzureBlockchainProvider = new Web3.providers.HttpProvider(defaultnode);
    
          const web3 = new Web3(AzureBlockchainProvider);
          web3.eth.personal.unlockAccount(myAccount, myPassword);
    
          return AzureBlockchainProvider;
          })(),
    
          network_id: "*",
          gasPrice: 0,
          from: myAccount
        },
        alpha: {
          provider: new Web3.providers.HttpProvider(alpha),
          network_id: "*",
        },
        beta: {
          provider: new Web3.providers.HttpProvider(beta),
          network_id: "*",
        }
      },
      compilers: {
        solc: {
          evmVersion: "byzantium"
        }
      }
    }
    ```

1. Spara ändringarna i `truffle-config.js`.

## <a name="create-smart-contract"></a>Skapa ett smart kontrakt

1. I mappen **kontrakt** skapar du en ny fil med namnet `SimpleStorage.sol`. Lägg till följande kod.

    ```solidity
    pragma solidity >=0.4.21 <0.6.0;
    
    contract SimpleStorage {
        string public storedData;
    
        constructor(string memory initVal) public {
            storedData = initVal;
        }
    
        function set(string memory x) public {
            storedData = x;
        }
    
        function get() view public returns (string memory retVal) {
            return storedData;
        }
    }
    ```
    
1. I mappen **migreringar** skapar du en ny fil med namnet `2_deploy_simplestorage.js`. Lägg till följande kod.

    ```solidity
    var SimpleStorage = artifacts.require("SimpleStorage.sol");
    
    module.exports = function(deployer) {
    
      // Pass 42 to the contract as the first constructor parameter
      deployer.deploy(SimpleStorage, "42", {privateFor: ["<alpha node public key>"], from:"<Ethereum account address>"})  
    };
    ```

1. Ersätt värdena i vinkelparenteser.

    | Värde | Beskrivning
    |-------|-------------
    | \<alpha node public key\> | Den offentliga nyckeln för alpha-noden
    | \<Ethereum account address\> | Ethereum-Kontoreferensen som skapats i noden för förvalda transaktioner

    I det här exemplet är det inledande värdet för **Store data** -värdet inställt på 42.

    **privateFor** definierar de noder som kontraktet är tillgängligt för. I det här exemplet kan nodens standardnods konto skicka privata transaktioner till **alpha** -noden. Du lägger till offentliga nycklar för alla deltagare i privata transaktioner. Om du inte inkluderar **privateFor:** och **från:** , är transaktionerna för smarta kontrakt offentliga och kan ses av alla medlemmar i konsortiet.

1. Spara alla filer genom att välja **fil > Spara alla**.

## <a name="deploy-smart-contract"></a>Distribuera Smart kontrakt

Använd Truffle för att `SimpleStorage.sol` distribuera till standard-Transaction Node Network.

```bash
truffle migrate --network defaultnode
```

Truffle kompilerar först och distribuerar sedan det smarta **SimpleStorage** -kontraktet.

Exempel på utdata:

```
admin@desktop:/mnt/c/truffledemo$ truffle migrate --network defaultnode

2_deploy_simplestorage.js
=========================

   Deploying 'SimpleStorage'
   -------------------------
   > transaction hash:    0x3f695ff225e7d11a0239ffcaaab0d5f72adb545912693a77fbfc11c0dbe7ba72
   > Blocks: 2            Seconds: 12
   > contract address:    0x0b15c15C739c1F3C1e041ef70E0011e641C9D763
   > account:             0x1a0B9683B449A8FcAd294A01E881c90c734735C3
   > balance:             0
   > gas used:            0
   > gas price:           0 gwei
   > value sent:          0 ETH
   > total cost:          0 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:                   0 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0 ETH
```

## <a name="validate-contract-privacy"></a>Validera kontrakt sekretess

På grund av kontrakts sekretess kan kontrakt värden bara frågas från noder som vi har deklarerat i **privateFor**. I det här exemplet kan vi fråga noden standardnode eftersom kontot finns i noden. 

1. Anslut till noden förvald transaktion med Truffle-konsolen.

    ```bash
    truffle console --network defaultnode
    ```

1. Kör kod som returnerar värdet för kontrakt instansen i Truffle-konsolen.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Om du har lyckats köra en fråga till noden standard transaktion returneras värdet 42. Exempel:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network defaultnode
    truffle(defaultnode)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Avsluta Truffle-konsolen.

    ```bash
    .exit
    ```

Eftersom vi har deklarerat den här **alpha** -nodens offentliga nyckel i **privateFor**kan vi fråga **alpha** -noden.

1. Använd Truffle-konsolen för att ansluta till **alpha** -noden.

    ```bash
    truffle console --network alpha
    ```

1. Kör kod som returnerar värdet för kontrakt instansen i Truffle-konsolen.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Om du har lyckats skicka en fråga till **alpha** -noden returneras värdet 42. Exempel:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network alpha
    truffle(alpha)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Avsluta Truffle-konsolen.

    ```bash
    .exit
    ```

Eftersom vi inte deklarerade den offentliga nyckeln för **beta** -noden i **privateFor**kan vi inte fråga **beta** -noden på grund av kontrakt sekretess.

1. Använd Truffle-konsolen för att ansluta till **beta** -noden.

    ```bash
    truffle console --network beta
    ```

1. Kör en kod som returnerar värdet för kontrakt instansen.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

1. Det går inte att fråga **beta** -noden eftersom kontraktet är privat. Exempel:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network beta
    truffle(beta)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    Thrown:
    Error: Returned values aren't valid, did it run Out of Gas?
        at XMLHttpRequest._onHttpResponseEnd (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:345:8)
        at XMLHttpRequest._setReadyState (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:219:8)
        at XMLHttpRequestEventTarget.dispatchEvent (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request-event-target.ts:44:13)
        at XMLHttpRequest.request.onreadystatechange (/mnt/c/truffledemo/node_modules/web3-providers-http/src/index.js:96:13)
    ```

1. Avsluta Truffle-konsolen.

    ```bash
    .exit
    ```
    
## <a name="send-a-transaction"></a>Skicka en transaktion

1. Skapa en fil med `sampletx.js`namnet. Spara den i roten för projektet.
1. Följande skript anger värdet för kontrakts **storedData** variabel till 65. Lägg till koden i den nya filen.

    ```javascript
    var SimpleStorage = artifacts.require("SimpleStorage");
    
    module.exports = function(done) {
      console.log("Getting deployed version of SimpleStorage...")
      SimpleStorage.deployed().then(function(instance) {
        console.log("Setting value to 65...");
        return instance.set("65", {privateFor: ["<alpha node public key>"], from:"<Ethereum account address>"});
      }).then(function(result) {
        console.log("Transaction:", result.tx);
        console.log("Finished!");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

    Ersätt värdena i vinkelparenteser och spara sedan filen.

    | Value | Beskrivning
    |-------|-------------
    | \<alpha node public key\> | Den offentliga nyckeln för alpha-noden
    | \<Ethereum account address\> | Ethereum-konto adress som skapats i noden standard transaktion.

    **privateFor** definierar de noder som transaktionen är tillgänglig för. I det här exemplet kan nodens standardnods konto skicka privata transaktioner till **alpha** -noden. Du måste lägga till offentliga nycklar för alla privata transaktions deltagare.

1. Använd Truffle för att köra skriptet för noden för förvalda transaktioner.

    ```bash
    truffle exec sampletx.js --network defaultnode
    ```

1. Kör kod som returnerar värdet för kontrakt instansen i Truffle-konsolen.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Om transaktionen lyckades returneras värdet 65. Exempel:
    
    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```

1. Avsluta Truffle-konsolen.

    ```bash
    .exit
    ```
    
## <a name="validate-transaction-privacy"></a>Validera transaktions sekretess

På grund av transaktions sekretess kan transaktioner endast utföras på noder som vi har deklarerat i **privateFor**. I det här exemplet kan vi utföra transaktioner eftersom vi har deklarerat den offentliga nyckeln för **alpha** -noden i **privateFor**. 

1. Använd Truffle för att köra transaktionen på **alpha** -noden.

    ```bash
    truffle exec sampletx.js --network alpha
    ```
    
1. Kör kod som returnerar värdet för kontrakt instansen.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```
    
    Om transaktionen lyckades returneras värdet 65. Exempel:

    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```
    
1. Avsluta Truffle-konsolen.

    ```bash
    .exit
    ```

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort resurserna genom att ta `myResourceGroup` bort resurs gruppen som du skapade i Azure blockchain-tjänsten.

Ta bort resurs gruppen:

1. I Azure Portal navigerar du till **resurs grupp** i det vänstra navigerings fönstret och väljer den resurs grupp som du vill ta bort.
1. Välj **Ta bort resursgrupp**. Verifiera borttagning genom att ange resurs gruppens namn och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till två transaktionsloggfiler för att demonstrera avtals-och transaktions sekretess. Du använde standard-noden för att distribuera ett privat Smart kontrakt. Du har testat sekretess genom att fråga kontrakts värden och utföra transaktioner på blockchain.

> [!div class="nextstepaction"]
> [Utveckla blockchain-program med Azure blockchain service](develop.md)
