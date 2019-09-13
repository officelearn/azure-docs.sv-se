---
title: Använda Geth för att ansluta till Azure blockchain-tjänsten
description: Ansluta till ett Azure blockchain service-nätverk med Geth
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: a26899e291c4f44d3c9d91032b2ee191ba03133a
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931779"
---
# <a name="quickstart-use-geth-to-connect-to-a-transaction-node"></a>Snabbstart: Använda Geth för att ansluta till en transaktionsnod

Geth är en go Ethereum-klient som du kan använda för att ansluta till en Geth-instans på en Azure blockchain-tjänst transaktions nod.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Installera [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* Slutför [snabb start: Skapa en blockchain-medlem med hjälp](create-member.md) av [Azure Portal eller snabb start: Skapa en Azure blockchain-tjänst blockchain-medlem med Azure CLI](create-member-cli.md)

## <a name="get-the-geth-connection-string"></a>Hämta anslutnings strängen för Geth

Du kan hitta Geth-anslutningssträngen i Azure Portal.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Navigera till din Azure blockchain service-medlem. Välj **Transactions-noder** och noden standard transaktions nod.

    ![Välj noden standard transaktion](./media/connect-geth/transaction-nodes.png)

1. Välj **anslutnings strängar**.
1. Kopiera anslutnings strängen från **https (åtkomst nyckel 1)** . Du behöver kommandot för nästa avsnitt.

    ![Anslutningssträng](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Anslut till Geth

1. Öppna en kommandotolk eller shell.
1. Använd Geth Attach-underkommandot för att koppla till den aktiva Geth-instansen på Transaction-noden. Klistra in anslutnings strängen som ett argument för under kommandot Attach. Exempel:

    ```
    geth attach <connection string>
    ```

1. När du har anslutit till Ethereum-konsolen för Transaction-noden kan du anropa Web3 Java Script Dapp API eller admin-API.

    Använd till exempel följande API för att ta reda på chainId.

    ```bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    I det här exemplet är chainId 297.

    ![Azure blockchain service-alternativ](./media/connect-geth/geth-attach.png)

1. Om du vill koppla från konsolen skriver `exit`du.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du Geth-klienten för att ansluta till en Geth-instans på en Azure blockchain service Transaction-nod. Testa nästa självstudie för att använda Azure blockchain Development Kit för Ethereum och Truffle för att skapa, bygga, distribuera och köra en smart kontrakts funktion via en transaktion.

> [!div class="nextstepaction"]
> [Använd Visual Studio Code för att skapa, bygga och distribuera smarta kontrakt](send-transaction.md)