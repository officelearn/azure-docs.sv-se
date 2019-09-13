---
title: Använd Visual Studio Code för att skapa, bygga och distribuera smarta kontrakt med Azure blockchain-tjänsten
description: Självstudie om hur du använder Azure blockchain Development Kit för Ethereum-tillägget i Visual Studio Code för att skapa, bygga och distribuera ett smart kontrakt på Azure blockchain-tjänsten.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 09/10/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
ms.openlocfilehash: 96fe4d77efdd1fda309d7da021bcc208edd2dfe9
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935054"
---
# <a name="tutorial-usevisual-studio-code-to-create-buildanddeploysmartcontracts"></a>Självstudier: Använd Visual Studio Code för att skapa, bygga och distribuera smarta kontrakt

I den här självstudien använder du tillägget Azure blockchain Development Kit for Ethereum i Visual Studio Code för att skapa, bygga och distribuera ett smart kontrakt på Azure blockchain-tjänsten. Du kan också använda Truffle för att köra en smart kontrakts funktion via en transaktion.

Du använder Azure blockchain Development Kit för att Ethereum:

> [!div class="checklist"]
> * Skapa ett smart kontrakt
> * Distribuera ett smart kontrakt
> * Köra en smart kontrakts funktion via en transaktion
> * Status för fråga kontrakt

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Slutför [snabb start: Använd Visual Studio Code för att ansluta till ett Azure blockchain service Consortium-nätverk](connect-vscode.md)

## <a name="create-a-smart-contract"></a>Skapa ett smart kontrakt

Azure blockchain Development Kit för Ethereum använder Project-mallar och Truffle-verktyg för att hjälpa Autogenerera, bygga och distribuera kontrakt. Innan du börjar måste du slutföra den nödvändiga [snabb starten: Använd Visual Studio Code för att ansluta till ett Azure blockchain service Consortium](connect-vscode.md)-nätverk. Snabb starten guidar dig genom installationen och konfigurationen av Azure blockchain Development Kit för Ethereum.

1. Från kommando paletten vs Code väljer **du Azure blockchain: Nytt projekt**för massivity.
1. Välj **skapa Basic-projekt**.
1. Skapa en ny mapp med `HelloBlockchain` namnet och **Välj ny projekt Sök väg**.

Azure blockchain Development Kit skapar och initierar ett nytt solide-projekt åt dig. Det grundläggande projektet innehåller ett exempel på ett **HelloBlockchain** Smart-kontrakt och alla nödvändiga filer för att bygga och distribuera till din Consortium-medlem i Azure blockchain-tjänsten. Det kan ta flera minuter innan projektet skapas. Du kan övervaka förloppet i VS Codes Terminal-panel genom att välja utdata för Azure-blockchain.

Projekt strukturen ser ut som i följande exempel:

   ![Massiv projekt](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Bygg ett smart kontrakt

Smarta kontrakt finns i projektets **kontrakts** katalog. Du kompilerar smarta kontrakt innan du distribuerar dem till en blockchain. Använd kommandot **Bygg kontrakt** för att kompilera alla smarta kontrakt i projektet.

1. I sido rutan i VS Code-Utforskaren expanderar du mappen **kontrakt** i projektet.
1. Högerklicka på **HelloBlockchain. sol** och välj **Bygg kontrakt** på menyn.

    ![Bygg kontrakt](./media/send-transaction/build-contracts.png)

Azure blockchain Development Kit använder Truffle för att kompilera smarta kontrakt.

![Kompilera utdata](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Distribuera ett smart kontrakt

Truffle använder migrations skript för att distribuera dina kontrakt till ett Ethereum-nätverk. Migreringar är JavaScript-filer som finns i projektets **migrerings** katalog.

1. Du distribuerar ditt smarta kontrakt genom att högerklicka på **HelloBlockchain. sol** och välja **distribuera kontrakt** på menyn.
1. Välj ditt Azure blockchain Consortium-nätverk i kommando paletten. Konsortiet blockchain-nätverk har lagts till i projektets Truffle-konfigurationsfil när du skapade projektet.
1. Välj **skapa**åtgärds kort. Välj ett fil namn och spara filen i projektmappen i projektmappen. Till exempel `myblockchainmember.env`. Den här filen används för att generera en privat Ethereum-nyckel för din blockchain-medlem.

Azure blockchain Development Kit använder Truffle för att köra migrations skriptet för att distribuera kontrakten till blockchain.

![Kontraktet har distribuerats](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Anropa en kontrakts funktion

**HelloBlockchain** -kontraktets **SendRequest** -funktion ändrar variabeln **RequestMessage** State. Att ändra tillstånd för ett blockchain nätverk görs via en transaktion. Du kan skapa ett skript för att köra **SendRequest** -funktionen via en transaktion.

1. Skapa en ny fil i roten av Truffle-projektet och ge den `sendrequest.js`namnet. Lägg till följande Web3 JavaScript-kod i filen.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. När ett projekt skapas i Azure blockchain Development Kit genereras konfigurations filen för Truffle med information om ditt konsortiums blockchain Network Endpoint. Öppna **Truffle-config. js** i projektet. Konfigurations filen visar två nätverk: en namngiven utveckling och en med samma namn som konsortiet.
1. I rutan för VS-kodens terminalfönster använder du Truffle för att köra skriptet i ditt konsortium blockchain-nätverk. I meny raden i terminalfönstret väljer du fliken **Terminal** och **PowerShell** i list rutan.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    Ersätt \<blockchain Network\> med namnet på blockchain-nätverket som definierats i **Truffle-config. js**.

Truffle kör skriptet i ditt blockchain-nätverk.

![Skriptutdata](./media/send-transaction/execute-transaction.png)

När du kör ett kontrakts funktion via en transaktion bearbetas inte transaktionen förrän ett block har skapats. Funktioner som är avsedda att köras via en transaktion returnerar ett transaktions-ID i stället för ett retur värde.

## <a name="query-contract-state"></a>Status för fråga kontrakt

Smarta kontrakt funktioner kan returnera det aktuella värdet för State-variabler. Nu ska vi lägga till en funktion för att returnera värdet för en tillstånds variabel.

1. I **HelloBlockchain. sol**lägger du till en **GetMessage** -funktion i det smarta **HelloBlockchain** -kontraktet.

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    Funktionen returnerar meddelandet som lagras i en tillstånds variabel baserat på kontraktets aktuella status.

1. Högerklicka på **HelloBlockchain. sol** och välj **Bygg kontrakt** på menyn för att kompilera ändringarna i det smarta kontraktet.
1. Du distribuerar genom att högerklicka på **HelloBlockchain. sol** och välja **distribuera kontrakt** på menyn. När du uppmanas väljer du ditt Azure blockchain Consortium-nätverk i kommando paletten.
1. Skapa sedan ett skript som använder för att anropa funktionen **GetMessage** . Skapa en ny fil i roten av Truffle-projektet och ge den `getmessage.js`namnet. Lägg till följande Web3 JavaScript-kod i filen.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. I rutan för VS-kodens terminalfönster använder du Truffle för att köra skriptet i ditt blockchain-nätverk. I meny raden i terminalfönstret väljer du fliken **Terminal** och **PowerShell** i list rutan.

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    Ersätt \<blockchain Network\> med namnet på blockchain-nätverket som definierats i **Truffle-config. js**.

Skriptet frågar det smarta kontraktet genom att anropa funktionen getMessage. Det aktuella värdet för **RequestMessage** status-variabeln returneras.

![Skriptutdata](./media/send-transaction/execute-get.png)

Observera att värdet inte är **Hej, blockchain!** . I stället är det returnerade värdet en plats hållare. När du ändrar och distribuerar kontraktet får kontraktet en ny kontrakts adress och State-variablerna tilldelas värden i konstruktorn för smarta kontrakt. Truffle-exemplet för **2_deploy_contracts. js** distribuerar det smarta kontraktet och skickar ett värde för plats hållare som ett argument. Konstruktorn anger **RequestMessage** tillstånds variabeln till värdet för plats hållaren och det som returneras.

1. Om du vill ställa in **RequestMessage** tillstånds variabel och fråga efter värdet kör du skripten **SendRequest. js** och **GetMessage. js** igen.

    ![Skriptutdata](./media/send-transaction/execute-set-get.png)

    **SendRequest. js** ställer in **RequestMessage** State-variabeln på **Hej, blockchain!** och **GetMessage. js** frågar kontraktet efter värdet för **RequestMessage** State-variabeln och returnerar **Hej, blockchain!** .

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort resurserna genom att ta `myResourceGroup` bort resurs gruppen som du skapade i snabb starten för att *skapa en blockchain-medlems* krav.

Ta bort resurs gruppen:

1. I Azure Portal navigerar du till **resurs grupp** i det vänstra navigerings fönstret och väljer den resurs grupp som du vill ta bort.
1. Välj **Ta bort resursgrupp**. Verifiera borttagning genom att ange resurs gruppens namn och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat ett exempel på ett heltäckande projekt med Azure blockchain Development Kit. Du har skapat och distribuerat ett smart kontrakt, sedan kallat en funktion via en transaktion i ett blockchain Consortium-nätverk som finns i Azure blockchain-tjänsten.

> [!div class="nextstepaction"]
> [Utveckla blockchain-program med Azure blockchain service](develop.md)
