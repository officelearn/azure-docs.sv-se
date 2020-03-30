---
title: Ansluta MetaMask till ett Azure Blockchain-tjänstnätverk
description: Anslut till ett Azure Blockchain Service-nätverk med MetaMask och distribuera ett smart kontrakt.
ms.date: 09/12/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: c0bad9efde44ce53f6b0656af3ac4af32ffe051d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79205101"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Snabbstart: Använd MetaMask för att ansluta och distribuera ett smart kontrakt

I den här snabbstarten använder du MetaMask för att ansluta till ett Azure Blockchain Service-nätverk och använda Remix för att distribuera ett smart kontrakt. Metamask är ett webbläsartillägg för att hantera en Ether-plånbok och utföra smarta kontraktsåtgärder.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

* Fullständig [snabbstart: Skapa en blockchain-medlem med Azure-portalen](create-member.md) eller [Snabbstart: Skapa en Blockchain Blockchain-tjänst-blockkedjemedlem med Azure CLI](create-member-cli.md)
* [Installera webbläsartillägg för MetaMask](https://metamask.io)
* Generera en [MetaMask-plånbok](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time)

## <a name="get-endpoint-address"></a>Hämta slutpunktsadress

Du behöver slutpunktsadressen för Azure Blockchain-tjänsten för att ansluta till blockchain-nätverket. Slutpunktsadressen och åtkomstnycklarna finns i Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Navigera till din Azure Blockchain Service-medlem.
1. Välj **Transaktionsnoder** och standardlänken för transaktionsnoder.

    ![Välj standardtransaktionsnod](./media/connect-metamask/transaction-nodes.png)

1. Välj **Anslutningssträngar > Access-tangenterna**.
1. Kopiera slutpunktsadressen från **HTTPS (Access-tangent 1)**.

    ![Anslutningssträng](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Anslut metamask

1. Öppna MetaMasks webbläsartillägg och logga in.
1. Välj **Anpassad RPC**i nätverksrullgardinsmenyn .

    ![Anpassad RPC](./media/connect-metamask/custom-rpc.png)

1. I **Ny nätverksadress > Ny RPC-URL**klistrar du in slutpunktsadressen som du kopierade ovan.
1. Välj **Spara**.

    Om anslutningen lyckades visas det privata nätverket i nätverksrullgardinsmenyn.

    ![Nytt nätverk](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Distribuera smart kontrakt

Remix är en webbläsarbaserad soliditetsutvecklingsmiljö. Med MetaMask och Remix tillsammans kan du distribuera och vidta åtgärder för smarta kontrakt.

1. Öppna webbläsaren och navigera till `https://remix.ethereum.org`.
1. Välj **Ny fil** på fliken **Start** under **Arkiv**.

    Ge den `simple.sol`nya filen ett namn .

    ![Skapa fil](./media/connect-metamask/create-file.png)

    Välj **OK**.
1. I Remix editor, klistra in följande **enkla smarta kontrakt** kod.

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

    Det **enkla kontraktet** deklarerar en tillståndsvariabel med namnet **saldo**. Det finns två funktioner definierade. Funktionen **Lägg till** lägger till ett tal i **saldot**. Funktionen **Hämta** returnerar värdet för **saldot**.
1. Om du vill kompilera kontraktet väljer du först kompilatorfönstret Soliditet och väljer sedan **kompilera simple.sol**.

    ![Kompilera](./media/connect-metamask/compile.png)

1. Välj fönstret **Distribuera & kör** och ange sedan att **miljön** ska **injiceras Web3** för att ansluta via MetaMask till din blockchain-medlem.

    ![Fliken Kör](./media/connect-metamask/injected-web3.png)

1. Välj det **enkla** kontraktet och **distribuera**sedan .

    ![Distribuera](./media/connect-metamask/deploy.png)


1. En MetaMask-meddelande varnar dig om otillräckliga medel för att utföra transaktionen.

    För ett offentligt blockchain-nätverk behöver du Ether för att betala transaktionskostnaden. Eftersom detta är ett privat nätverk i ett konsortium kan du ange gaspriset till noll.

1.  Välj **Gasavgift > Redigera > Advanced**anger du **gaspriset** till 0.

    ![Gaspris](./media/connect-metamask/gas-price.png)

    Välj **Spara**.

1. Välj **Bekräfta** om du vill distribuera det smarta kontraktet till blockkedjan.
1. Expandera det **enkla** kontraktet i avsnittet **Distribuerade kontrakt.**

    ![Distribuerat kontrakt](./media/connect-metamask/deployed-contract.png)

    Två åtgärder, **lägg till** och **hämta**, mappa till de funktioner som definieras i kontraktet.

1. Om du vill utföra en tilläggstransaktion på blockkedjan anger du ett nummer att lägga till och väljer sedan **lägg till**. **add** Du kan få en gas uppskattning misslyckande meddelande från Remix: "Du skickar transaktionen till en privat blockchain som inte kräver gas." Välj **Skicka transaktion** för att tvinga transaktionen.
1. I likhet med när du distribuerade kontraktet varnar en MetaMask-meddelande dig om otillräckliga medel för att utföra transaktionen.

    Eftersom detta är ett privat nätverk i ett konsortium kan vi sätta gaspriset till noll.

1. Välj **Gasavgift > Redigera > Avancerat,** ange **gaspriset** till 0 och välj **Spara**.
1. Välj **Bekräfta** om du vill skicka transaktionen till blockkedjan.
1. Välj **hämta** åtgärd. Det här är ett anrop till frågenoddata. En transaktion behövs inte.

Felsökningsfönstret i Remix visar information om transaktionerna på blockkedjan:

    ![Debug history](./media/connect-metamask/debug.png)

    You can see the **simple** contract creation, transaction for **simple.add**, and call to **simple.get**.

Om du vill visa transaktionshistorik i MetaMask öppnar du webbläsartillägget MetaMask och letar i avsnittet **Historik** efter en logg över det distribuerade kontraktet och transaktionerna.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du webbläsartillägget MetaMask för att ansluta till en Azure Blockchain Service-transaktionsnod, distribuera ett smart kontrakt och skicka en transaktion till blockchain. Prova nästa självstudiekurs för att använda Azure Blockchain Development Kit för Ethereum och Tryffel för att skapa, skapa, distribuera och köra en smart kontraktsfunktion via en transaktion.

> [!div class="nextstepaction"]
> [Skapa, skapa och distribuera smarta kontrakt på Azure Blockchain Service](send-transaction.md)