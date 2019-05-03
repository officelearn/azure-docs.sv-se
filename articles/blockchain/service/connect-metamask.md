---
title: Ansluta MetaMask till ett nätverk för Azure Blockchain Service
description: Anslut till ett Azure Blockchain Service-nätverk med MetaMask och distribuera ett smarta kontrakt.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: db029cee6edcd14d29c83964e5bf75aa45077e7e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026902"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Snabbstart: Använd MetaMask att ansluta och distribuera ett smarta kontrakt

I den här snabbstarten ska du använda MetaMask för att ansluta till ett nätverk med Azure Blockchain Service och använda Remix för att distribuera ett smarta kontrakt. Metamask är ett webbläsartillägg att hantera en Ether wallet och utföra åtgärder för smarta kontrakt.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Skapa en medlem i Azure Blockchain](create-member.md)
* Installera [MetaMask webbläsartillägg](https://metamask.io)
* Generera en MetaMask [wallet](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time)

## <a name="get-endpoint-address"></a>Hämta slutpunktsadress

Du behöver Azure Blockchain Service slutpunktsadress att ansluta till blockchain-nätverk. Du hittar slutpunkten adress och åtkomstnycklar i Azure-portalen.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Gå till din Azure Blockchain Service medlem. Välj **transaktion noder** och länken standard transaktion noden.

    ![Välj standard transaktion nod](./media/connect-metamask/transaction-nodes.png)

1. Välj **anslutningssträngar > åtkomstnycklar**.
1. Kopiera slutpunktsadressen från **HTTPS (åtkomstnyckel 1)**. Du behöver adressen för nästa avsnitt.

    ![Anslutningssträng](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Ansluta MetaMask

1. Öppna MetaMask webbläsartillägget och logga in.
1. Välj i listrutan nätverk **anpassad RPC**.

    ![Anpassad RPC](./media/connect-metamask/custom-rpc.png)

1. I **nytt nätverk > nya RPC-URL: en**, ange din slutpunktsadress som kopieras från föregående avsnitt.
1. Välj **Spara**.

    Om anslutningen har upprättats visas det privata nätverket i listrutan nätverk.

    ![Nytt nätverk](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Distribuera smarta kontrakt

REMIX är en webbläsarbaserad Solidity-utvecklingsmiljö. Använda MetaMask och Remix tillsammans kan du distribuera och vidta åtgärder på smarta kontrakt.

1. Öppna webbläsaren och navigera till `https://remix.ethereum.org`.
1. Välj **Kör**. 

    MetaMask anger din **miljö** till **in Web3** och **konto** till ditt nätverk.

    ![Kör-fliken](./media/connect-metamask/injected-web3.png)

1. Välj **Skapa ny fil**.

    Ge den nya filen namnet `simple.sol`.

    ![Skapa fil](./media/connect-metamask/create-file.png)

    Välj **OK**.

1. Klistra in följande i redigeraren Remix **enkelt smarta kontrakt** kod.

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

    Den **enkel kontrakt** deklarerar tillstånd variabeln **Saldo**. Det finns två funktioner som har definierats. Den **lägga till** lägger funktionen till ett tal till **Saldo**. Den **hämta** funktionen returnerar värdet för **Saldo**.

1. För att kompilera kontraktet, Välj den **kompilera > Starta att kompilera**. Om detta lyckas visas en grön ruta med namnet kontraktnamnet.

    ![Compile](./media/connect-metamask/compile.png)

1. Om du vill köra kontraktet, Välj den **kör** fliken. Välj den **enkel** sedan ett kontrakt **distribuera**.

    ![Anpassad RPC](./media/connect-metamask/deploy.png)

1. MetaMask visas ett meddelande varnar dig otillräckliga medel att utföra transaktionen.

    För en offentlig blockchain-nätverket måste Ether att betala för kostnaden för transaktion. Eftersom det här är ett privat nätverk i ett konsortium, kan du ange gas priset till noll.

1.  Välj **Gas avgift > Redigera > Avancerat**, ange den **Gas pris** till 0.

    ![Gas pris](./media/connect-metamask/gas-price.png)

    Välj **Spara**.

1. Välj **Bekräfta** att distribuera det smarta kontraktet till blockchain.
1. I den **distribueras kontrakt** , expanderar den **enkel** kontraktet.

    ![Distribuerade kontrakt](./media/connect-metamask/deployed-contract.png)

    Det finns två åtgärder **lägga till** och **hämta** som mappar till funktionerna som definieras i kontraktet.

1. Att utföra en **lägga till** transaktion på blockchain, ange ett tal att lägga till och välj sedan **lägga till**.
1. Precis som när du distribuerade kontraktet, MetaMask visas ett meddelande varnar dig otillräckliga medel att utföra transaktionen.

    Eftersom detta är ett privat nätverk i ett konsortium kan vi ange gas priset till noll.

1.  Välj **Gas avgift > Redigera > Avancerat**, ange den **Gas pris** till 0 och välj **spara**.
1. Välj **Bekräfta** att utföra transaktionen på blockchain.
1. Välj **hämta** åtgärd. Det här är ett anrop till noden frågedata. En transaktion är inte behövs.
1. I fönstret felsökning av Remix visas information om transaktioner på blockchain.

    ![Felsöka historik](./media/connect-metamask/debug.png)

    Du kan se den **enkel** kontrakt skapas transaktionen för **simple.add**, och anrop till **simple.get**.

1. Du kan också se transaktionshistoriken i MetaMask. Öppna MetaMask webbläsartillägg.
1. I den **historik** avsnittet ser du en logg över distribuerade kontraktet och transaktioner.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du webbläsartillägget MetaMask för att ansluta till en Azure Blockchain Service transaktion nod, distribuera ett smarta kontrakt och skicka en transaktion till blockchain. Prova nästa självstudie för att distribuera och skicka en transaktion med Truffle.

> [!div class="nextstepaction"]
> [Skicka en transaktion](send-transaction.md)