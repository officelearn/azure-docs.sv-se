---
title: Använda program i Azure Blockchain Workbench
description: Hur du använder programmet tjänstekontrakt i Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: e17a9275792e3a7fdbea6e3b95e596eaa15f4359
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43105819"
---
# <a name="using-applications-in-azure-blockchain-workbench"></a>Använda program i Azure Blockchain Workbench

Du kan använda Blockchain Workbench för att skapa och utföra åtgärder på kontrakt. Du kan också visa information om till exempel status och transaktion historik-kontrakt.

## <a name="prerequisites"></a>Förutsättningar

* En Blockchain Workbench-distribution. Mer information finns i [Azure Blockchain Workbench distribution](blockchain-workbench-deploy.md) mer information om distribution
* En distribuerad blockchain-program i Blockchain Workbench. Se [skapa ett blockchain-program i Azure Blockchain Workbench](blockchain-workbench-create-app.md)

[Öppna Blockchain Workbench](blockchain-workbench-deploy.md#blockchain-workbench-web-url) i webbläsaren.

![Blockchain Workbench](media/blockchain-workbench-use/workbench.png)

Du måste logga in som medlem i Blockchain Workbench. Om det finns inga program i listan, är du medlem i Blockchain Workbench men inte medlem i alla program. Blockchain Workbench-administratör kan tilldela medlemmar till program.

## <a name="create-new-contract"></a>Skapa nytt kontrakt 

Om du vill skapa ett nytt kontrakt, du måste vara en medlem som angetts som ett kontrakt **initierare**. Läs om definiera programroller och initierare för kontraktet [arbetsflöden i configuration-översikt](blockchain-workbench-configuration-overview.md#workflows). Information om hur du tilldelar medlemmar till roller för programmet finns i [lägga till en medlem i program](blockchain-workbench-manage-users.md#add-member-to-application).

1. Välj panelen program som innehåller det kontrakt som du vill skapa i Blockchain Workbench program i. En lista över aktiva kontrakt visas.

2. Om du vill skapa ett nytt kontrakt, Välj **nytt kontrakt**.

    ![Knapp för ny kontrakt](media/blockchain-workbench-use/contract-list.png)

3. Den **nytt kontrakt** egenskapsruta visas. Ange värden för inledande parametrar. Välj **Skapa**.

    ![Nya kontrakt rutan](media/blockchain-workbench-use/new-contract.png)

    Nyligen skapade kontraktet visas i listan med de andra aktiva kontrakt.

    ![Aktiva kontrakt lista](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>Vidta åtgärder för kontrakt

Beroende på tillståndet är kontraktet, medlemmar kan vidta åtgärder för att övergå till nästa steg i kontraktet. Åtgärder definieras som [övergångar](blockchain-workbench-configuration-overview.md#transitions) inom en [tillstånd](blockchain-workbench-configuration-overview.md#states). Medlemmar som hör till en tillåtna program eller en instans roll för övergången kan vidta åtgärder. 

1. Välj panelen program som innehåller avtal att vidta åtgärder i Blockchain Workbench program i.
2. Välj kontraktet i listan. Information om kontraktet visas i olika avsnitt. 

    ![Kontraktsinformation](media/blockchain-workbench-use/contract-details.png)

    | Section  | Beskrivning  |
    |---------|---------|
    | Status | Visar aktuell status i kontraktet faser |
    | Information | De aktuella värdena för kontraktet |
    | Åtgärd | Information om den senaste åtgärden |
    | Aktivitet | Transaktionshistoriken kontraktets |
    
3. I den **åtgärd** väljer **vidta åtgärder**.

4. Information om det aktuella tillståndet för kontraktet visas i en ruta. Välj den åtgärd du vill ta i listrutan. 

    ![Välj åtgärd](media/blockchain-workbench-use/choose-action.png)

5. Välj **vidta åtgärder** att initiera åtgärden.
6. Om parametrar krävs för åtgärden, anger du värden för åtgärden.

    ![Vidta åtgärd](media/blockchain-workbench-use/take-action.png)

7. Välj **vidta åtgärder** att köra åtgärden.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Så här felsöker du Azure Blockchain Workbench](blockchain-workbench-troubleshooting.md)
