---
title: Använd Geth för att ansluta till Azure Blockchain-tjänsten
description: Ansluta till ett nätverk för Azure Blockchain-tjänsten med hjälp av Geth
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 0716a9326a54ae31d4f355fe5f4c88488339b390
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026923"
---
# <a name="quickstart-use-geth-to-connect-to-a-transaction-node"></a>Snabbstart: Använd Geth för att ansluta till en nod för transaktion

Geth är en Gå Ethereum-klient som du kan använda för att ansluta till en Geth-instans på en nod för Azure Blockchain Service-transaktion.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* Installera [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* [Skapa en medlem i Azure Blockchain](create-member.md)

## <a name="get-the-geth-connection-string"></a>Hämta anslutningssträngen Geth

Du hittar Geth anslutningssträngen i Azure-portalen.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Gå till din Azure Blockchain Service medlem. Välj **transaktion noder** och länken standard transaktion noden.

    ![Välj standard transaktion nod](./media/connect-geth/transaction-nodes.png)

1. Välj **anslutningssträngar**.
1. Kopiera anslutningssträngen från **HTTPS (åtkomstnyckel 1)**. Du behöver kommandot för nästa avsnitt.

    ![Anslutningssträng](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Ansluta till Geth

1. Öppna en kommandotolk eller shell.
1. Använd Geth koppla underkommandot att koppla till den Geth-instansen som som körs på noden transaktion. Klistra in anslutningssträngen som argument för underkommandot attach. Exempel:

    ```
    geth attach <connection string>
    ```

1. När du är ansluten till noden transaktion Ethereum-konsolen, kan du anropa API: et web3 JavaScript Dapp eller administrations-API.

    Exempelvis kan du använda följande API för att ta reda på chainId.

    ```bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    I det här exemplet är chainId 297.

    ![Azure Blockchain Service-alternativet](./media/connect-geth/geth-attach.png)

1. Om du vill koppla från konsolen skriver `exit`.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du Geth klienten för att ansluta till en Geth-instans på en nod för Azure Blockchain Service-transaktion. Prova nästa självstudie för att distribuera och skicka en transaktion med Truffle.

> [!div class="nextstepaction"]
> [Skicka en transaktion](send-transaction.md)