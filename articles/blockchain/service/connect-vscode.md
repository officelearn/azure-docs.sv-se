---
title: Använd Azure blockchain Development Kit för Ethereum – Azure blockchain-tjänsten
description: Ansluta till ett Azure blockchain service Consortium-nätverk med Azure blockchain Development Kit for Ethereum-tillägget i Visual Studio Code
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 6364e887c699219d80974d592a8ff7c77cca2621
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329302"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Snabb start: Använd Visual Studio Code för att ansluta till ett Azure blockchain service Consortium-nätverk

I den här snabb starten installerar du och använder Azure blockchain Development Kit för att ansluta till ett konsortium på Azure blockchain-tjänsten. Azure blockchain Development Kit fören klar hur du skapar, ansluter, bygger och distribuerar smarta kontrakt på Ethereum-redovisningar. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

* Slutför [snabb start: skapa en blockchain-medlem med hjälp av Azure Portal](create-member.md) eller [snabb start: skapa en Azure blockchain service blockchain-medlem med Azure CLI](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure blockchain Development Kit för Ethereum-tillägg](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js](https://nodejs.org)
* [Git](https://git-scm.com)
* [Python](https://www.python.org/downloads/release/python-2715/). Lägg till python. exe i sökvägen. Python i din sökväg krävs för Azure blockchain Development Kit.
* [Truffle](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI](https://github.com/trufflesuite/ganache-cli)

### <a name="verify-azure-blockchain-development-kit-environment"></a>Kontrol lera Azure blockchain Development Kit-miljön

Azure blockchain Development Kit verifierar att utvecklings miljö kraven är uppfyllda. För att verifiera utvecklings miljön:

Från kommando paletten VS Code väljer du **Azure blockchain: Visa välkomst sida**.

Azure blockchain Development Kit kör ett verifierings skript som tar ungefär en minut att slutföra. Du kan visa utdata genom att välja **Terminal > ny terminal**. I meny raden i terminalen väljer du fliken **utdata** och **Azure-blockchain** i list rutan. Lyckad verifiering ser ut som på följande bild:

![Giltig utvecklings miljö](./media/connect-vscode/valid-environment.png)

 Om du saknar ett nödvändigt verktyg, en ny flik som heter **Azure blockchain Development Kit** , visar en lista över de appar som krävs för att installera och länkar för att hämta verktygen.

![Appar som krävs av dev kit](./media/connect-vscode/required-apps.png)

Installera eventuella krav som saknas innan du fortsätter med snabb starten.

## <a name="connect-to-consortium-member"></a>Anslut till konsortiet medlem

Du kan ansluta till medlemmar i konsortiet med hjälp av tillägget Azure blockchain Development Kit VS Code. När du har anslutit till ett konsortium kan du kompilera, bygga och distribuera smarta kontrakt till en Azure blockchain service Consortium-medlem.

Om du inte har åtkomst till en Azure blockchain service Consortium-medlem, slutför den nödvändiga [snabb starten: skapa en blockchain-medlem med hjälp av Azure Portal](create-member.md) eller [snabb start: skapa en Azure blockchain service blockchain-medlem med Azure CLI ](create-member-cli.md).

1. I Explorer-fönstret Visual Studio Code (VS Code) expanderar du tillägget **Azure blockchain** .
1. Välj **Anslut till konsortiet**.

   ![Anslut till konsortiet](./media/connect-vscode/connect-consortium.png)

    Om du uppmanas att ange Azure-autentisering följer du anvisningarna för att autentisera med hjälp av en webbläsare.
1. Välj **Anslut till Azure blockchain service Consortium** i list rutan kommando för palett.
1. Välj den prenumeration och resurs grupp som är kopplad till din Azure blockchain service Consortium-medlem.
1. Välj konsortiet i listan.

Konsortiet och blockchain-medlemmarna visas i Visual Studio Explorer-sido fältet.

![Konsortiet som visas i Utforskaren](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du Azure blockchain Development Kit för Ethereum Visual Studio Code Extension för att ansluta till ett konsortium på Azure blockchain-tjänsten. Testa nästa självstudie för att använda Azure blockchain Development Kit för Ethereum och Truffle för att skapa, bygga, distribuera och köra en smart kontrakts funktion via en transaktion.

> [!div class="nextstepaction"]
> [Använd Visual Studio Code för att skapa, bygga och distribuera smarta kontrakt](send-transaction.md)