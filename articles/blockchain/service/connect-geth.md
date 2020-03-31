---
title: Använd Geth för att koppla till Azure Blockchain Service
description: Koppla till en Geth-instans på Azure Blockchain Service-transaktionsnod
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 9da78eac1dc429bcc0ad52bb9cb2f1fb743a90d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74455825"
---
# <a name="quickstart-use-geth-to-attach-to-an-azure-blockchain-service-transaction-node"></a>Snabbstart: Använd Geth för att koppla till en azure blockchain-tjänsttransaktionsnod

I den här snabbstarten använder du Geth-klienten för att koppla till en Geth-instans på en Azure Blockchain Service-transaktionsnod. När du har anslutit använder du Geth JavaScript-konsolen för att anropa ett web3 JavaScript Dapp API.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

* Installera [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* Fullständig [snabbstart: Skapa en blockchain-medlem med Azure-portalen](create-member.md) eller [Snabbstart: Skapa en Blockchain Blockchain-tjänst-blockkedjemedlem med Azure CLI](create-member-cli.md)

## <a name="get-geth-connection-string"></a>Hämta Geth-anslutningssträng

Du kan hämta Geth-anslutningssträngen för en Azure Blockchain-tjänsttransaktionsnod i Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till din Azure Blockchain Service-medlem. Välj **Transaktionsnoder** och standardlänken för transaktionsnoder.

    ![Välj standardtransaktionsnod](./media/connect-geth/transaction-nodes.png)

1. Välj **Anslutningssträngar**.
1. Kopiera anslutningssträngen från **HTTPS (Access-tangenten 1)**. Du behöver strängen för nästa avsnitt.

    ![Anslutningssträng](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Anslut till Geth

1. Öppna en kommandotolk eller ett skal.
1. Använd Geth-bifoga underkomman för att koppla till den geth-instans som körs på transaktionsnoden. Klistra in anslutningssträngen som ett argument för den bifogade underkommittén. Ett exempel:

    ``` bash
    geth attach <connection string>
    ```

1. När du är ansluten till transaktionsnodens Ethereum-konsol kan du anropa web3 JavaScript Dapp API eller admin API.

    Använd till exempel följande API för att ta reda på chainId.

    ``` bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    I det här exemplet är chainId 661.

    ![Azure Blockchain-tjänst alternativ](./media/connect-geth/geth-attach.png)

1. Om du vill koppla `exit`från konsolen skriver du .

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du Geth-klienten för att koppla till en Geth-instans på en Azure Blockchain Service-transaktionsnod. Prova nästa självstudiekurs för att använda Azure Blockchain Development Kit för Ethereum för att skapa, skapa, distribuera och köra en smart kontraktsfunktion via en transaktion.

> [!div class="nextstepaction"]
> [Skapa, skapa och distribuera smarta kontrakt på Azure Blockchain Service](send-transaction.md)
