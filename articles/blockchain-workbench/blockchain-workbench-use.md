---
title: Använda program i Azure Blockchain arbetsstationen
description: Hur du använder programmet tjänstekontrakt i Azure Blockchain arbetsstationen.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/26/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 8732d1b87acaa6673ae92b3302fb257dcb134217
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="using-applications-in-azure-blockchain-workbench"></a>Använda program i Azure Blockchain arbetsstationen

Du kan använda Blockchain arbetsstationen för att skapa och vidta åtgärder i kontrakt. Du kan också visa minimera information, till exempel status och transaktionen historik.

## <a name="prerequisites"></a>Förutsättningar

* En Blockchain arbetsstationen-distribution. Mer information finns i [Azure Blockchain arbetsstationen distribution](blockchain-workbench-deploy.md) mer information om distribution
* En distribuerad blockchain programmet i Blockchain arbetsstationen. Se [skapar en blockchain program i Azure Blockchain arbetsstationen]()

[Öppna Blockchain arbetsstationen](blockchain-workbench-deploy.md#blockchain-workbench-web-url) i webbläsaren.

![Blockchain Workbench](media/blockchain-workbench-use/workbench.png)

Du måste logga in som medlem i Blockchain arbetsstationen. Om det finns inga program i listan, är du medlem i Blockchain arbetsstationen men inte en medlem i alla program. Blockchain arbetsstationen-administratör tilldela medlemmar program.

## <a name="create-new-contract"></a>Skapa nytt kontrakt 

Om du vill skapa ett nytt kontrakt, du måste vara medlem i den **AllowedInstanceRoles** roll. 

1. Välj panelen program som innehåller kontraktet som du vill skapa under Blockchain arbetsstationen program. En lista över aktiva kontrakt visas.

2. Om du vill skapa ett nytt kontrakt, Välj **kontrakt**.

    ![Knappen Nytt kontrakt](media/blockchain-workbench-use/contract-list.png)

3. Den **kontrakt** visas. Ange värdena för inledande parametrar. Välj **Skapa**.

    ![Nya kontrakt rutan](media/blockchain-workbench-use/new-contract.png)

    Nyligen skapade kontraktet visas i listan med aktiva kontrakt.

    ![Lista över aktiva kontrakt](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>Utför en åtgärd i kontrakt

1. Välj panelen program som innehåller kontraktet att vidta åtgärder under Blockchain arbetsstationen program.
2. Välj kontraktet i listan. I olika avsnitt visas information om kontraktet. 

    ![Kontraktsinformation](media/blockchain-workbench-use/contract-details.png)

    | Section  | Beskrivning  |
    |---------|---------|
    | Status | Visar den aktuella statusen i kontraktet faser |
    | Information | Aktuella värden för kontraktet |
    | Åtgärd | Information om den senaste åtgärden |
    | Aktivitet | Historik över kontraktets |
    
3. I den **åtgärd** väljer **åtgärda**.

4. Information om det aktuella tillståndet för kontraktet visas i rutan. Välj den åtgärd som du vill ta i listrutan. 

    ![Välj åtgärd](media/blockchain-workbench-use/choose-action.png)

5. Välj **åtgärda** att starta åtgärden.
6. Parametrar som krävs för åtgärden, ange värdena för åtgärden.

    ![Vidta åtgärd](media/blockchain-workbench-use/take-action.png)

7. Välj **åtgärda** att utföra åtgärden.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Felsökning av Azure Blockchain arbetsstationen](blockchain-workbench-troubleshooting.md)