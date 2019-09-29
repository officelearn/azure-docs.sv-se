---
title: Aktivera antecknings böcker i Azure Cosmos DB-kontot
description: Med Azure Cosmos DB inbyggda antecknings böcker kan du analysera och visualisera dina data i portalen. I den här artikeln beskrivs hur du aktiverar den här funktionen för Cosmos-konton.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: 824a562cb5f0562a235d903fbd4e575bc9c22572
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672735"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts"></a>Aktivera antecknings böcker för Azure Cosmos DB-konton

Inbyggda Jupyter Notebook i Azure Cosmos DB gör att du kan analysera och visualisera dina data från Azure Portal. I den här artikeln beskrivs hur du aktiverar den här funktionen för ditt Azure Cosmos DB-konto.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Aktivera antecknings böcker i ett nytt Cosmos-konto
1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj **Skapa en resurs** > **Databaser** > **Azure Cosmos DB**.
1. På sidan **skapa Azure Cosmos DB konto** väljer du **antecknings böcker**. 
 
    ![Alternativet Välj antecknings böcker i Azure Cosmos DB skapa bladet](media/enable-notebooks/create-new-account-with-notebooks.png)
1. Välj **Granska + skapa**. Du kan hoppa över alternativet **nätverk** och **taggar** . 
1. Granska konto inställningarna och välj sedan **skapa**. Det tar några minuter att skapa kontot. Vänta tills Portal sidan visar att **distributionen är klar**. 

    ![Meddelandefönstret i Azure-portalen](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. Välj **gå till resurs** för att gå till sidan Azure Cosmos DB konto. 

    ![Sidan Azure Cosmos DB konto](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. Navigera till rutan **datautforskaren** . Nu bör du se din arbets yta för antecknings böcker.

    ![Arbets ytan ny Azure Cosmos DB Notebooks](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Aktivera antecknings böcker i ett befintligt Cosmos-konto
Du kan också aktivera antecknings böcker på befintliga konton. Det här steget måste bara utföras en gång per konto.

1. Navigera till fönstret **datautforskaren** i ditt Cosmos-konto.
1. Välj **Aktivera antecknings böcker**.

    ![Skapa en ny arbets yta för antecknings böcker i Datautforskaren](media/enable-notebooks/enable-notebooks-workspace.png)
1. Då uppmanas du att skapa en ny arbets yta för antecknings böcker. Välj **Slutför installationen.**
1. Ditt konto har nu Aktiver ATS för att använda antecknings böcker!

## <a name="create-and-run-your-first-notebook"></a>Skapa och kör din första bärbara dator

Du kan kontrol lera att du kan använda antecknings böcker genom att välja en av antecknings böckerna under exempel antecknings böcker. Då sparas en kopia av antecknings boken på din arbets yta och den öppnas.

I det här exemplet använder vi **GettingStarted. ipynb**. 

![Visa GettingStarted. ipynb Notebook](media/enable-notebooks/select-getting-started-notebook.png)

Så här kör du antecknings boken:
1. Välj den första kod cellen som innehåller python-kod. 
1. Välj **Kör** för att köra cellen. Du kan också använda **Shift + Retur** för att köra cellen.
1. Uppdatera resurs fönstret om du vill se databasen och behållaren som har skapats.

    ![Kör kom igång-anteckningsbok](media/enable-notebooks/run-first-notebook-cell.png)

Du kan också välja **ny Notebook** för att skapa en ny antecknings bok eller ladda upp en befintlig Notebook-fil (. ipynb) genom att välja **Ladda upp fil** på menyn **Mina antecknings böcker** . 

![Skapa eller ladda upp en ny antecknings bok](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om fördelarna med att [Azure Cosmos DB Jupyter Notebooks](cosmosdb-jupyter-notebooks.md)
- Lär dig mer om [hur du använder Notebook-funktioner och-kommandon](use-notebook-features-and-commands.md)