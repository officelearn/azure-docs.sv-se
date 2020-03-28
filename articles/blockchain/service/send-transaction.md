---
title: Skapa, skapa, & distribuera smart kontrakt handledning - Azure Blockchain Service
description: Självstudiekurs om hur du använder Azure Blockchain Development Kit for Ethereum-tillägget i Visual Studio Code för att skapa, skapa och distribuera ett smart kontrakt på Azure Blockchain Service.
ms.date: 12/06/2019
ms.topic: tutorial
ms.reviewer: chrisseg
ms.openlocfilehash: 5b901ab904425a22d2fe9643ffa75a4e978efa88
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74972880"
---
# <a name="tutorial-create-buildanddeploysmartcontracts-on-azure-blockchain-service"></a>Självstudiekurs: Skapa, skapa och distribuera smarta kontrakt på Azure Blockchain Service

I den här självstudien använder du Azure Blockchain Development Kit for Ethereum-tillägget i Visual Studio Code för att skapa, skapa och distribuera ett smart kontrakt på Azure Blockchain Service. Du använder också utvecklingspaketet för att köra en smart kontraktsfunktion via en transaktion.

Du använder Azure Blockchain Development Kit för Ethereum för att:

> [!div class="checklist"]
> * Skapa ett smart kontrakt
> * Distribuera ett smart kontrakt
> * Utföra en smart kontraktsfunktion via en transaktion

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

* Fullständig [snabbstart: Använd Visual Studio-kod för att ansluta till ett Azure Blockchain Service-konsortiumnätverk](connect-vscode.md)
* [Visual Studio-kod](https://code.visualstudio.com/Download)
* [Azure Blockchain Development Kit för Ethereum-tillägg](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Nod.js 10.15.x eller högre](https://nodejs.org/download)
* [Git 2.10.x eller högre](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) Lägg till python.exe på sökvägen. Python version 2.7.15 i din sökväg krävs för Azure Blockchain Development Kit.
* [Tryffel 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

I Windows krävs en installerad C++-kompilator för nod-gyp-modulen. Du kan använda MSBuild-verktygen:

* Om Visual Studio 2017 är installerat konfigurerar du npm för att använda MSBuild-verktygen med kommandot`npm config set msvs_version 2017 -g`
* Om Visual Studio 2019 är installerat ställer du in sökvägen ms build tools för npm. Till exempel, `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Annars installerar du de fristående VS `npm install --global windows-build-tools` Build-verktygen med hjälp av ett upphöjt *kommandoskal för körning som administratör.*

Mer information om nod-gyp finns i [nod-gyp-databasen på GitHub](https://github.com/node-gyp).

## <a name="create-a-smart-contract"></a>Skapa ett smart kontrakt

Azure Blockchain Development Kit för Ethereum använder projektmallar och tryffelverktyg för att hjälpa till att skapa, skapa och distribuera kontrakt. Innan du börjar slutför du den nödvändiga [snabbstarten: Använd Visual Studio-kod för att ansluta till ett Azure Blockchain Service-konsortiumnätverk](connect-vscode.md). Snabbstarten guidar dig genom installationen och konfigurationen av Azure Blockchain Development Kit för Ethereum.

1. Välj **Azure Blockchain: New Solidity Project**i kommandopaletten VS-kod .
1. Välj **Skapa grundläggande projekt**.
1. Skapa en ny `HelloBlockchain` mapp med namnet och **Välj ny projektsökväg**.

Azure Blockchain Development Kit skapar och initierar ett nytt Solidity-projekt åt dig. Det grundläggande projektet innehåller ett exempel på Smart Contract **För HelloBlockchain** och alla nödvändiga filer för att skapa och distribuera till din konsortiemedlem i Azure Blockchain Service. Det kan ta flera minuter innan projektet skapas. Du kan övervaka förloppet i VS-kodens terminalpanel genom att välja utdata för Azure Blockchain.

Projektstrukturen ser ut som följande exempel:

   ![Soliditetsprojekt](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Skapa ett smart kontrakt

Smarta kontrakt finns i projektets **kontraktskatalog.** Du kompilerar smarta kontrakt innan du distribuerar dem till en blockchain. Använd kommandot **Skapa kontrakt** för att kompilera alla smarta kontrakt i projektet.

1. Expandera **kontraktmappen** i projektet i sidofältet VS-kodutforskaren.
1. Högerklicka på **HelloBlockchain.sol** och välj **Bygg kontrakt** på menyn.

    ![Välj menyn Skapa kontrakt ](./media/send-transaction/build-contracts.png)

Azure Blockchain Development Kit använder Tryffel för att kompilera de smarta kontrakten.

![Utdata för tryffelkompilator](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Distribuera ett smart kontrakt

Tryffel använder migreringsskript för att distribuera dina kontrakt till ett Ethereum-nätverk. Migreringar är JavaScript-filer som finns i projektets **migreringskatalog.**

1. Om du vill distribuera ditt smarta kontrakt högerklickar du på **HelloBlockchain.sol** och väljer **Distribuera kontrakt** på menyn.
1. Välj ditt Azure Blockchain-konsortiumnätverk i kommandopaletten. Konsortiet blockchain-nätverk lades till i projektets Tryffelkonfigurationsfil när du skapade projektet.
1. Välj **Generera mnemonic**. Välj ett filnamn och spara mnemonic-filen i projektmappen. Till exempel `myblockchainmember.env`. Mnemonic-filen används för att generera en Ethereum privat nyckel för din blockchain-medlem.

Azure Blockchain Development Kit använder Tryffel för att köra migreringsskriptet för att distribuera kontrakten till blockkedjan.

![Kontrakt har distribuerats](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Anropa en kontraktsfunktion

**HelloBlockchain-kontraktets** **SendRequest-funktion** ändrar **tillståndsvariabeln RequestMessage.** Ändra tillståndet för ett blockchain-nätverk sker via en transaktion. Du kan använda sidan azure blockchain development kit smart kontrakt interaktion för att ringa **SendRequest-funktionen** via en transaktion.

1. Om du vill interagera med ditt smarta kontrakt högerklickar du på **HelloBlockchain.sol** och väljer **Visa sidan För interaktion med smarta kontrakt** på menyn.

    ![Välj Visa sidan Visa interaktion med smart kontrakt på menyn](./media/send-transaction/contract-interaction.png)

1. På interaktionssidan kan du välja en distribuerad kontraktsversion, samtalsfunktioner, visa aktuellt tillstånd och visa metadata.

    ![Exempel på sidan Interaktion med smarta kontrakt](./media/send-transaction/interaction-page.png)

1. Om du vill anropa smart kontraktsfunktion väljer du kontraktsåtgärden och skickar dina argument. Välj **SendRequest** kontrakt åtgärder och ange **Hello, Blockchain!** för parametern **requestMessage.** Välj **Kör om** du vill anropa funktionen **SendRequest** via en transaktion.

    ![Utför SendRequest-åtgärd](./media/send-transaction/sendrequest-action.png)

När transaktionen har bearbetats återspeglar interaktionsavsnittet tillståndsändringarna.

![Ändringar av kontraktstillstånd](./media/send-transaction/contract-state.png)

Funktionen SendRequest anger fälten **RequestMessage** och **State.** Det aktuella tillståndet för **RequestMessage** är argumentet du skickade **Hello, Blockchain**. Fältet **Tillståndsvärde** förblir **begäran**.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du `myResourceGroup` ta bort resurserna genom att ta bort resursgruppen som du skapade i snabbstarten *Skapa en blockchain-medlemkontör.*

Så här tar du bort resursgruppen:

1. I Azure-portalen navigerar du till **resursgruppen** i det vänstra navigeringsfönstret och väljer den resursgrupp som du vill ta bort.
1. Välj **Ta bort resursgrupp**. Verifiera borttagning genom att ange resursgruppsnamnet och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du ett exempel på Solidity-projekt med Azure Blockchain Development Kit. Du har skapat och distribuerat ett smart kontrakt som sedan kallas en funktion via en transaktion på ett blockchain consortium-nätverk som finns på Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Utveckla blockchain-program med Azure Blockchain Service](develop.md)
