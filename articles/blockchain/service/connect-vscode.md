---
title: Använda Visual Studio-kod för att ansluta till Azure Blockchain Service
description: Ansluta till ett Azure Blockchain Service-konsortiumnätverk med Azure Blockchain Development Kit for Ethereum-tillägget i Visual Studio Code
ms.date: 04/22/2020
ms.topic: quickstart
ms.reviewer: caleteet
ms.openlocfilehash: 8b502966317c5d07e89de4ae70ff72b899e963e6
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084846"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Snabbstart: Använda Visual Studio-kod för att ansluta till ett Azure Blockchain Service-konsortiumnätverk

I den här snabbstarten installerar och använder du tillägget Azure Blockchain Development Kit for Ethereum Visual Studio Code (VS Code) för att koppla till ett konsortium på Azure Blockchain Service. Azure Blockchain Development Kit förenklar hur du skapar, ansluter, bygger och distribuerar smarta kontrakt på Ethereum blockchain-liggare.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

* Fullständig [snabbstart: Skapa en blockchain-medlem med Azure-portalen](create-member.md) eller [Snabbstart: Skapa en Blockchain Blockchain-tjänst-blockkedjemedlem med Azure CLI](create-member-cli.md)
* [Visual Studio-koden](https://code.visualstudio.com/Download)
* [Azure Blockchain Development Kit för Ethereum-tillägg](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Nod.js 10.15.x eller högre](https://nodejs.org)
* [Git 2.10.x eller högre](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) Lägg till python.exe på sökvägen. Att ha Python version 2.7.15 i din sökväg krävs för Azure Blockchain Development Kit.
* [Tryffel 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

I Windows krävs en installerad C++-kompilator för nod-gyp-modulen. Du kan använda MSBuild-verktygen:

* Om Visual Studio 2017 är installerat konfigurerar du npm för att använda MSBuild-verktygen med kommandot`npm config set msvs_version 2017 -g`
* Om Visual Studio 2019 är installerat ställer du in sökvägen ms build tools för npm. Till exempel, `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Annars installerar du de fristående VS `npm install --global windows-build-tools` Build-verktygen med hjälp av ett upphöjt *kommandoskal för körning som administratör.*

Mer information om nod-gyp finns i [nod-gyp-databasen på GitHub](https://github.com/nodejs/node-gyp).

### <a name="verify-azure-blockchain-development-kit-environment"></a>Verifiera Azure Blockchain Development Kit-miljön

Azure Blockchain Development Kit verifierar att dina utvecklingsmiljöförutny förutsättningar har uppfyllts. Så här verifierar du utvecklingsmiljön:

Välj **Azure Blockchain: Visa välkomstsida**på kommandopaletten VS-kod .

Azure Blockchain Development Kit kör ett valideringsskript som tar ungefär en minut att slutföra. Du kan visa utdata genom att välja **Terminal > Ny terminal**. I terminalmenyraden väljer du fliken **Utdata** och **Azure Blockchain** i listrutan. Lyckad validering ser ut som följande bild:

![Giltig utvecklingsmiljö](./media/connect-vscode/valid-environment.png)

 Om du saknar ett obligatoriskt verktyg visas de verktyg som krävs med namnet **Azure Blockchain Development Kit – Preview.**

![Dev kit krävs apps](./media/connect-vscode/required-apps.png)

Installera eventuella nödvändiga krav som saknas innan du fortsätter med snabbstarten.

## <a name="connect-to-consortium-member"></a>Anslut till konsortiemedlem

Du kan ansluta till konsortiemedlemmar med azure blockchain development kit VS-kodtillägget. När du är ansluten till ett konsortium kan du kompilera, skapa och distribuera smarta kontrakt till en Azure Blockchain Service-konsortiummedlem.

Om du inte har tillgång till en Azure Blockchain Service-konsortiemedlem slutför du den nödvändiga [snabbstarten: Skapa en blockchain-medlem med Azure-portalen](create-member.md) eller [Snabbstart: Skapa en Azure Blockchain Service blockchain-medlem med Azure CLI](create-member-cli.md).

1. Expandera **Azure Blockchain-tillägget** i fönstret VS-kodutforskaren.
1. Välj **Anslut till nätverk**.

   ![Ansluta till nätverket](./media/connect-vscode/connect-consortium.png)

    Om du uppmanas att ange Azure-autentisering följer du anvisningarna för att autentisera med hjälp av en webbläsare.
1. Välj **Azure Blockchain-tjänst** i kommandopalettens listruta.
1. Välj den prenumerations- och resursgrupp som är associerad med din Azure Blockchain Service-konsortiemedlem.
1. Välj ditt konsortium i listan.

Konsortie- och blockchain-medlemmarna finns listade i sidofältet VS Code Explorer.

![Konsortiet visas i explorer](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du Azure Blockchain Development Kit för Ethereum VS-kodtillägg för att koppla till ett konsortium på Azure Blockchain Service. Prova nästa självstudiekurs för att använda Azure Blockchain Development Kit för Ethereum för att skapa, skapa, distribuera och köra en smart kontraktsfunktion via en transaktion.

> [!div class="nextstepaction"]
> [Skapa, skapa och distribuera smarta kontrakt på Azure Blockchain Service](send-transaction.md)