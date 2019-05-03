---
title: Skicka transaktioner med Azure Blockchain-tjänsten
description: Självstudier om hur du använder Azure Blockchain-tjänsten för att distribuera ett smarta kontrakt och skicka en privat transaktion.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: d3ad4cdfe33948c04c278ed3dfef7aa6fda637ab
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026811"
---
# <a name="tutorial-send-transactions-using-azure-blockchain-service"></a>Självstudier: Skicka transaktioner med Azure Blockchain-tjänsten

I den här självstudien måste du skapa transaktionen noder om du vill testa kontrakt och transaktionen sekretess.  Du använder Truffle för att skapa en lokal utvecklingsmiljö och distribuera ett smarta kontrakt och skicka en privat transaktion.

Du lär dig följande:

> [!div class="checklist"]
> * Lägga till noder i transaktion
> * Använda Truffle för att distribuera ett smarta kontrakt
> * Skicka en transaktion
> * Verifiera transaktion sekretess

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* Fullständig [skapa medlem blockchain med Azure portal](create-member.md)
* Fullständig [Snabbstart: Använd Truffle för att ansluta till ett konsortienätverk](connect-truffle.md)
* Truffle kräver flera verktyg installeras inklusive [Node.js](https://nodejs.org), [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git), och [Truffle](https://github.com/trufflesuite/truffle).

    Om du vill komma igång snabbt på Windows 10, installera [Ubuntu för Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6) för en terminal Unix Bash shell installera [Truffle](https://github.com/trufflesuite/truffle). Ubuntu för Windows-distribution innehåller Node.js- och Git.

* Installera [Visual Studio Code](https://code.visualstudio.com/Download)
* Installera [Solidity för Visual Studio Code-tillägg](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity)

## <a name="create-transaction-nodes"></a>Skapa transaktion noder

Som standard har en transaktion nod. Vi ska lägga till ytterligare två. En av noderna deltar i privata transaktionen. Den andra ingår inte i den privata transaktionen.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Gå till din Azure Blockchain-medlem och välj **transaktion noder > Lägg till**.
1. Slutföra inställningarna för en ny transaktion nod med namnet `alpha`.

    ![Skapa transaktion nod](./media/send-transaction/create-node.png)

    | Inställning | Värde | Beskrivning |
    |---------|-------|-------------|
    | Namn | `alpha` | Nodnamnet för transaktionen. Namnet används för att skapa DNS-adressen för transaktionen noden slutpunkten. Till exempel `alpha-mymanagedledger.blockchain.azure.com`. |
    | Lösenord | starkt lösenord | Lösenordet används för att komma åt noden transaktion slutpunkten med grundläggande autentisering.

1. Välj **Skapa**.

    Etablera en ny transaktion nod tar cirka 10 minuter.

1. Upprepa steg 2 till 4 för att lägga till en transaktion nod med namnet `beta`.

Du kan fortsätta med självstudiekursen medan noderna har etablerats. När etableringen är klar har du tre noder för transaktionen.

## <a name="open-truffle-project"></a>Öppna Truffle projekt

1. Öppna en Bash shell-terminal.
1. Ändra sökvägen till projektkatalogen Truffle från nödvändiga [Snabbstart: Använd Truffle för att ansluta till ett konsortienätverk](connect-truffle.md). Exempel:

    ```bash
    cd truffledemo
    ```

1. Starta Truffle interaktiv distribuering konsol.

    ``` bash
    truffle develop
    ```

    Truffle skapar en lokal utveckling blockchain och en interaktiv konsol.

## <a name="connect-to-transaction-node"></a>Ansluta till noden för transaktion

Använd Web3 för att ansluta till noden standard transaktion och skapa ett konto. Du kan hämta Web3 anslutningssträngen från Azure-portalen.

1. I Azure-portalen går du till noden standard transaktion och välj **transaktion noder > exempelkoden > Web3**.
1. Kopiera JavaScript från **HTTPS (åtkomstnyckel 1)** ![Web3 exempelkod](./media/send-transaction/web3-code.png)

1. Klistra in Web3 JavaScript-koden för transaktionen standardnoden i konsolen Truffle interaktiv distribuering. Koden skapar ett Web3-objekt som är ansluten till din Azure Blockchain Service transaktion nod.

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    ```

    Du kan anropa metoder på Web3-objektet för att interagera med din nod för transaktionen.

1. Skapa ett nytt konto på transaktion standardnoden. Ersätt Lösenordsparametern med din egen starkt lösenord.

    ```bash
    web3.eth.personal.newAccount("1@myStrongPassword");
    ```

    Kontrollera anteckna den returnerade postadress och lösenord som du använde för nästa avsnitt.

1. Avsluta Truffle-utvecklingsmiljö.

    ```bash
    .exit
    ```

## <a name="configure-truffle-project"></a>Konfigurera Truffle projekt

Om du vill konfigurera Truffle projektet måste vissa transaktion nodinformation från Azure-portalen.

### <a name="transaction-node-public-key"></a>Offentlig nyckel för noden för transaktion

Varje transaktion-nod har en offentlig nyckel. Den offentliga nyckeln kan du skicka en privat transaktion till noden. För att kunna skicka en transaktion från transaktionen standardnoden till den *alpha* transaktion nod, måste den *alpha* transaktion nodens offentlig nyckel.

Du kan hämta den offentliga nyckeln från transaktionslistan för noden. Kopiera den offentliga nyckeln för noden alpha och spara värdet för senare under kursen.

![Lista över nod](./media/send-transaction/node-list.png)

### <a name="transaction-node-endpoint-addresses"></a>Slutpunktsadresser för transaktionen nod

1. Gå till varje transaktion nod i Azure-portalen och välj **transaktion noder > anslutningssträngar**.
1. Kopiera och spara slutpunkts-URL från ** HTTPS (åtkomstnyckel 1) för varje transaktion-nod. Du behöver slutpunktsadresser för konfigurationsfilen smarta kontrakt senare under kursen.

    ![Slutpunktsadress för transaktion](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Redigera konfigurationsfilen

1. Starta Visual Studio Code och öppna Truffle projektet directory mappen med den **fil > Öppna mappen** menyn.
1. Öppna konfigurationsfilen Truffle `truffle-config.js`.
1. Ersätt innehållet i filen med följande konfigurationsinformation. Lägg till variabler som innehåller slutpunkter adresser och kontoinformation. Ersätt vinkelparentes avsnitt med värden som du samlat in från föregående avsnitt.

``` javascript
var defaultnode = "<default transaction node connection string>";
var alpha = "<alpha transaction node connection string>";
var beta = "<beta transaction node connection string>";

var myAccount = "<account address>";
var myPassword = "<account password>";

var Web3 = require("web3");
```

Lägg till av konfigurationskod i den **module.exports** avsnittet av konfigurationen.

```javascript
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
      gas: 0,
      gasPrice: 0,
      from: myAccount
    },
    alpha: {
      provider: new Web3.providers.HttpProvider(alpha),
      network_id: "*",
      gas: 0,
      gasPrice: 0
    },
    beta: {
      provider: new Web3.providers.HttpProvider(beta),
      network_id: "*",
      gas: 0,
      gasPrice: 0
    }
  }
}
```

## <a name="create-smart-contract"></a>Skapa smarta kontrakt

I mappen **kontrakt**, skapa en ny fil med namnet `SimpleStorage.sol`. Lägg till följande kod.

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

I mappen **migreringar**, skapa en ny fil med namnet `2_deploy_simplestorage.js`. Lägg till följande kod.

```solidity
var SimpleStorage = artifacts.require("SimpleStorage.sol");

module.exports = function(deployer) {

  // Pass 42 to the contract as the first constructor parameter
  deployer.deploy(SimpleStorage, "42", {privateFor: ["<alpha node public key>"], from:"<Account address>"})  
};
```

Ersätt värdena i hakparenteser.

| Värde | Beskrivning
|-------|-------------
| \<Alpha noden offentlig nyckel\> | Offentlig nyckel för noden alpha
| \<Kontoadress\> | Kontoadress som skapats i transaktionen standardnoden.

I det här exemplet är det första värdet i den **storeData** värdet är inställt på 42.

**privateFor** definierar noderna som kontraktet är tillgänglig. I det här exemplet standard transaktion nodens konto kan omvandla privata transaktionerna till den **alpha** noden. Du måste lägga till offentliga nycklar för alla privata transaktion deltagare. Om du inte anger **privateFor:** och **från:**, smarta kontrakt transaktioner är offentliga och kan ses av alla medlemmarna.

Spara alla filer genom att välja **fil > Spara allt**.

## <a name="deploy-smart-contract"></a>Distribuera smarta kontrakt

Använda Truffle för att distribuera `SimpleStorage.sol` till standardnätverk transaktion noden.

```bash
truffle migrate --network defaultnode
```

Truffle först kompilerar och distribuerar sedan den **SimpleStorage** smarta kontrakt.

Exempel på utdata:

```
pat@DESKTOP:/mnt/c/truffledemo$ truffle migrate --network defaultnode

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

## <a name="validate-contract-privacy"></a>Verifiera kontrakt sekretess

På grund av kontrakt sekretess kontraktsvärden endast kan efterfrågas från noder som vi har deklarerat i **privateFor**. I det här exemplet frågar vi transaktion standardnoden eftersom kontot finns i noden. Anslut till transaktion standardnoden med Truffle-konsolen.

```bash
truffle console --network defaultnode
```

Köra ett kommando som returnerar värdet för kontrakt-instans.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

Om frågor transaktion standardnoden lyckas, returneras värdet 42.

Exempel på utdata:

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network defaultnode
truffle(defaultnode)> SimpleStorage.deployed().then(function(instance){return instance.get();})
'42'
```

Avsluta konsolen.

```bash
.exit
```

Eftersom vi har deklarerat **alpha** nodens offentliga nyckeln i **privateFor**, vi kan fråga den **alpha** noden. Med hjälp av konsolen Truffle ansluta till den **alpha** noden.

```bash
truffle console --network alpha
```

Köra ett kommando som returnerar värdet för kontrakt-instans.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

Om frågar den **alpha** noden lyckas, returneras värdet 42.

Exempel på utdata:

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network alpha
truffle(alpha)> SimpleStorage.deployed().then(function(instance){return instance.get();})
'42'
```

Avsluta konsolen.

```bash
.exit
```

Eftersom vi inte har deklarera **beta** nodens offentliga nyckeln i **privateFor**, vi kommer inte att fråga den **beta** noden på grund av kontrakt sekretess. Med hjälp av konsolen Truffle ansluta till den **beta** noden.

```bash
truffle console --network beta
```

Köra ett kommando som returnerar värdet för kontrakt-instans.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

Fråga den **beta** nod misslyckas eftersom kontraktet är privat.

Exempel på utdata:

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network beta
truffle(beta)> SimpleStorage.deployed().then(function(instance){return instance.get();})
Thrown:
Error: Returned values aren't valid, did it run Out of Gas?
    at XMLHttpRequest._onHttpResponseEnd (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:345:8)
    at XMLHttpRequest._setReadyState (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:219:8)
    at XMLHttpRequestEventTarget.dispatchEvent (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request-event-target.ts:44:13)
    at XMLHttpRequest.request.onreadystatechange (/mnt/c/truffledemo/node_modules/web3-providers-http/src/index.js:96:13)
```

Avsluta konsolen.

```bash
.exit
```

## <a name="send-a-transaction"></a>Skicka en transaktion

Skapa en fil med namnet `sampletx.js`. Spara den i roten av projektet.

Det här skriptet anger kontraktet **storedData** variabelvärde till 65. Lägg till kod till den nya filen.

```javascript
var SimpleStorage = artifacts.require("SimpleStorage");

module.exports = function(done) {
  console.log("Getting deployed version of SimpleStorage...")
  SimpleStorage.deployed().then(function(instance) {
    console.log("Setting value to 65...");
    return instance.set("65", {privateFor: ["<alpha node public key>"], from:"<Account address>"});
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

Ersätt värdena i hakparenteser och spara sedan filen.

| Värde | Beskrivning
|-------|-------------
| \<Alpha noden offentlig nyckel\> | Offentlig nyckel för noden alpha
| \<Kontoadress\> | Kontoadress som skapats i transaktionen standardnoden.

**privateFor** definierar noderna som transaktionen är tillgänglig. I det här exemplet standard transaktion nodens konto kan omvandla privata transaktionerna till den **alpha** noden. Du måste lägga till offentliga nycklar för alla privata transaktion deltagare.

Använd Truffle för att köra skriptet för transaktionen standardnoden.

```bash
truffle exec sampletx.js --network defaultnode
```

Köra ett kommando som returnerar värdet för kontrakt-instans.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

Om transaktionen lyckades returneras värdet 65.

Exempel på utdata:

```
Getting deployed version of SimpleStorage...
Setting value to 65...
Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
Finished!
```

Avsluta konsolen.

```bash
.exit
```

## <a name="validate-transaction-privacy"></a>Verifiera transaktion sekretess

På grund av transaktionen sekretess transaktioner bara kan utföras på noder som vi har deklarerat i **privateFor**. I det här exemplet vi utföra transaktioner eftersom vi har deklarerat **alpha** nodens offentliga nyckeln i **privateFor**. Använda Truffle för att köra transaktionen på den **alpha** noden.

```bash
truffle exec sampletx.js --network alpha
```

Köra ett kommando som returnerar värdet för kontrakt-instans.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

Om transaktionen lyckades returneras värdet 65.

Exempel på utdata:

```
Getting deployed version of SimpleStorage...
Setting value to 65...
Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
Finished!
```

Avsluta konsolen.

```bash
.exit
```

I den här självstudien får du har lagt till två transaktion noder för att demonstrera kontrakt och transaktionen sekretess. Du använde standardnoden för att distribuera ett privat smarta kontrakt. Du har testat sekretess genom förfrågningar till kontraktsvärden och utför transaktioner på blockchain.

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort resurserna genom att ta bort den `myResourceGroup` resursgruppen du skapade med Azure Blockchain-tjänsten.

Ta bort resursgruppen:

1. I Azure-portalen går du till **resursgrupp** i det vänstra navigeringsfönstret och välj den resursgrupp som du vill ta bort.
1. Välj **Ta bort resursgrupp**. Kontrollera borttagningen genom att ange resursgruppens namn och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utveckla blockchain-program med Azure Blockchain-tjänsten](develop.md)
