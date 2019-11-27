---
title: Ansluta MetaMask till ett Azure blockchain service-nätverk
description: Anslut till ett Azure blockchain service-nätverk med MetaMask och distribuera ett smart kontrakt.
ms.date: 09/12/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 21e45b80a9a08dd6bdeefb0ab01fadabaa08cbce
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455997"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Snabb start: använda MetaMask för att ansluta och distribuera ett smart kontrakt

I den här snabb starten använder du MetaMask för att ansluta till ett Azure blockchain service-nätverk och använder Remix för att distribuera ett smart kontrakt. Metamask är ett webb läsar tillägg som hanterar en eter-plån bok och utför smarta kontrakts åtgärder.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Slutför [snabb start: skapa en blockchain-medlem med hjälp av Azure Portal](create-member.md) eller [snabb start: skapa en Azure blockchain service blockchain-medlem med Azure CLI](create-member-cli.md)
* Installera [MetaMask Browser-tillägg](https://metamask.io)
* Generera en MetaMask [plån bok](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time)

## <a name="get-endpoint-address"></a>Hämta slut punkts adress

Du behöver Azure blockchain-tjänstens slut punkts adress för att ansluta till blockchain-nätverket. Du kan hitta slut punkts adressen och åtkomst nycklarna i Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Navigera till din Azure blockchain service-medlem. Välj **Transactions-noder** och noden standard transaktions nod.

    ![Välj noden standard transaktion](./media/connect-metamask/transaction-nodes.png)

1. Välj **anslutnings strängar > åtkomst nycklar**.
1. Kopiera slut punkts adressen från **https (åtkomst nyckel 1)** . Du behöver adressen för nästa avsnitt.

    ![Anslutningssträng](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Anslut MetaMask

1. Öppna MetaMask browser extension och logga in.
1. I list rutan nätverk väljer du **anpassad RPC**.

    ![Anpassat RPC](./media/connect-metamask/custom-rpc.png)

1. I **nytt nätverk > ny RPC-URL**anger du din slut punkts adress som kopierats från föregående avsnitt.
1. Välj **Spara**.

    Om anslutningen lyckades visas det privata nätverket i list rutan nätverk.

    ![Nytt nätverk](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Distribuera Smart kontrakt

Remix är en webbläsarbaserad utvecklings miljö. Med MetaMask och Remix tillsammans kan du distribuera och vidta åtgärder för smarta kontrakt.

1. Öppna webbläsaren och navigera till `https://remix.ethereum.org`.
1. Välj **ny fil** på fliken **Start** under **fil**.

    Namnge den nya filen `simple.sol`.

    ![Skapa fil](./media/connect-metamask/create-file.png)

    Välj **OK**.
1. I Remix-redigeraren klistrar du in följande **enkla smarta kontrakt** kod.

    ```solidity
    pragma solidity ^0.5.0;
             
    contract simple {
        uint balance;
                 
        constructor() public{
            balance = 0;
        }
                 
        function add(uint _num) public {
            balance += _num;
        }
                 
        function get() public view returns (uint){
            return balance;
        }
    }
    ```

    Det **enkla kontraktet** deklarerar en tillstånds variabel med namnet **Saldo**. Två funktioner har definierats. Funktionen **Add** lägger till ett tal att **balansera**. Funktionen **Get** returnerar värdet för **saldot**.
1. För att kompilera kontraktet väljer du först fönstret halvledare compiler och väljer sedan **compile Simple. sol**. 

    ![Kompileringsfel](./media/connect-metamask/compile.png)

1. Välj rutan **distribuera & körning** och ange sedan att **miljön** ska **injicera Web3** för att ansluta via MetaMask till din blockchain-medlem.

    ![Fliken kör](./media/connect-metamask/injected-web3.png)

1. Välj det **enkla** kontraktet och **distribuera**.

    ![Distribuera](./media/connect-metamask/deploy.png)


1. Ett MetaMask-meddelande visas som varnar dig om otillräckliga medel för att utföra transaktionen.

    För ett offentligt blockchain nätverk skulle du behöva betala för transaktions kostnaden. Eftersom det här är ett privat nätverk i ett konsortium kan du ställa in gas priset på noll.

1.  Välj **gas avgift > redigera > Avancerat**och Ställ in **gas priset** på 0.

    ![Gas pris](./media/connect-metamask/gas-price.png)

    Välj **Spara**.

1. Välj **Bekräfta** om du vill distribuera det smarta kontraktet till blockchain.
1. I avsnittet **distribuerade kontrakt** expanderar du det **enkla** kontraktet.

    ![Distribuerat kontrakt](./media/connect-metamask/deployed-contract.png)

    Det finns två åtgärder för att **lägga till** och **Hämta** kartan till de funktioner som definierats i kontraktet.

1. Om du vill utföra en **Lägg till** transaktion på blockchain anger du ett nummer som du vill lägga till och väljer **Lägg till**. Du kan få ett fel meddelande om gas uppskattning från Remix. Du skickar transaktionen till en privat blockchain som inte kräver gas. Välj **Skicka transaktion** för att framtvinga transaktionen.
1. Precis som när du distribuerade kontraktet visas ett MetaMask-meddelande som varnar dig om otillräckliga medel för att genomföra transaktionen.

    Eftersom det här är ett privat nätverk i ett konsortium kan vi ställa in gas priset på noll.

1.  Välj **gas avgift > redigera > Avancerat**, ange **gas priset** till 0 och välj **Spara**.
1. Välj **Bekräfta** för att skicka transaktionen till blockchain.
1. Välj **Hämta** åtgärd. Detta är ett anrop för att fråga Node-data. En transaktion behövs inte.
1. I fel söknings fönstret för Remix kan du se information om transaktionerna på blockchain.

    ![Fel söknings historik](./media/connect-metamask/debug.png)

    Du kan se det **enkla** uppdraget att skapa, transaktioner för **enkel. Lägg till**och anropa **enkel. get**.

1. Du kan också se transaktions historik i MetaMask. Öppna webb läsar tillägget MetaMask.
1. I avsnittet **Historik** kan du se en logg över det distribuerade kontraktet och transaktionerna.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du MetaMask Browser-tillägget för att ansluta till en Azure blockchain service Transaction-nod, distribuera ett smart kontrakt och skicka en transaktion till blockchain. Testa nästa självstudie för att använda Azure blockchain Development Kit för Ethereum och Truffle för att skapa, bygga, distribuera och köra en smart kontrakts funktion via en transaktion.

> [!div class="nextstepaction"]
> [Skapa, skapa och distribuera smarta avtal i Azure blockchain-tjänsten](send-transaction.md)