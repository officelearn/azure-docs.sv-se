---
title: Aktivera antecknings böcker i Azure Cosmos DB konto (förhands granskning)
description: Med Azure Cosmos DB inbyggda antecknings böcker kan du analysera och visualisera dina data i portalen. I den här artikeln beskrivs hur du aktiverar den här funktionen för Cosmos-konton.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: cf8da4379a7770e44e86663c02cc7a57da9d3452
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089805"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Aktivera antecknings böcker för Azure Cosmos DB-konton (förhands granskning)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Inbyggda antecknings böcker för Azure Cosmos DB är för närvarande tillgängliga i följande Azure-regioner: östra Australien, östra USA, östra USA 2, norra Europa, södra centrala USA, Sydostasien, Storbritannien, södra, Västeuropa och västra USA 2. Om du vill använda antecknings böcker [skapar du ett nytt konto med antecknings böcker](#enable-notebooks-in-a-new-cosmos-account) eller [aktiverar antecknings böcker på ett befintligt konto](#enable-notebooks-in-an-existing-cosmos-account) i någon av dessa regioner.

Inbyggda Jupyter Notebook i Azure Cosmos DB gör att du kan analysera och visualisera dina data från Azure Portal. Den här artikeln beskriver hur du aktiverar den här funktionen för ditt Azure Cosmos DB-konto.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Aktivera antecknings böcker i ett nytt Cosmos-konto

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Välj **skapa en resurs**  >  **databas**  >  **Azure Cosmos DB** .
1. På sidan **skapa Azure Cosmos DB konto** väljer du **antecknings böcker** . 
 
    :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks.png" alt-text="Alternativet Välj antecknings böcker i Azure Cosmos DB skapa bladet":::

1. Välj **Granska + skapa** . Du kan hoppa över alternativet **nätverk** och **taggar** . 
1. Granska kontoinställningarna och välj sedan **Skapa** . Det tar några minuter att skapa kontot. Vänta tills portalsidan visar meddelandet **Distributionen är klar** . 

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Alternativet Välj antecknings böcker i Azure Cosmos DB skapa bladet":::

1. Välj **Gå till resurs** för att gå till sidan för Azure Cosmos DB-kontot.

   :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="Alternativet Välj antecknings böcker i Azure Cosmos DB skapa bladet":::

1. Navigera till rutan **datautforskaren** . Nu bör du se din arbets yta för antecknings böcker.

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="Alternativet Välj antecknings böcker i Azure Cosmos DB skapa bladet":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Aktivera antecknings böcker i ett befintligt Cosmos-konto

Du kan också aktivera antecknings böcker på befintliga konton. Det här steget måste bara utföras en gång per konto.

1. Navigera till fönstret **datautforskaren** i ditt Cosmos-konto.
1. Välj **Aktivera antecknings böcker** .

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="Alternativet Välj antecknings böcker i Azure Cosmos DB skapa bladet":::

1. Då uppmanas du att skapa en ny arbets yta för antecknings böcker. Välj **Slutför installationen.**
1. Ditt konto har nu Aktiver ATS för att använda antecknings böcker!

## <a name="create-and-run-your-first-notebook"></a>Skapa och kör din första bärbara dator

Du kan kontrol lera att du kan använda antecknings böcker genom att välja en av antecknings böckerna under exempel antecknings böcker. Då sparas en kopia av antecknings boken på din arbets yta och den öppnas.

I det här exemplet använder vi **GettingStarted. ipynb** . 

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="Alternativet Välj antecknings böcker i Azure Cosmos DB skapa bladet":::

Så här kör du antecknings boken:
1. Välj den första kod cellen som innehåller python-kod. 
1. Välj **Kör** för att köra cellen. Du kan också använda **Shift + Retur** för att köra cellen.
1. Uppdatera resurs fönstret om du vill se databasen och behållaren som har skapats.

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="Alternativet Välj antecknings böcker i Azure Cosmos DB skapa bladet":::

Du kan också välja **ny Notebook** för att skapa en ny antecknings bok eller ladda upp en befintlig Notebook-fil (. ipynb) genom att välja **Ladda upp fil** på menyn **Mina antecknings böcker** . 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="Alternativet Välj antecknings böcker i Azure Cosmos DB skapa bladet":::

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om fördelarna med att [Azure Cosmos DB Jupyter Notebooks](cosmosdb-jupyter-notebooks.md)
