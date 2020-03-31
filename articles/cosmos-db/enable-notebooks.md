---
title: Aktivera anteckningsböcker i Azure Cosmos DB-konto (förhandsversion)
description: Azure Cosmos DB:s inbyggda anteckningsböcker gör att du kan analysera och visualisera dina data inifrån portalen. I den här artikeln beskrivs hur du aktiverar den här funktionen för Cosmos-konton.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: dcec310db43baa513b2d574d03f3f35dee3f773b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768024"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Aktivera anteckningsböcker för Azure Cosmos DB-konton (förhandsversion)

> [!IMPORTANT]
> Inbyggda anteckningsböcker för Azure Cosmos DB är för närvarande tillgängliga i följande Azure-regioner: Östra Australien, Östra USA, Östra USA 2, Norra Europa, Södra centrala USA, Sydostasien, Storbritannien i södra, västra Europa och västra USA 2. Om du vill använda anteckningsböcker [skapar du ett nytt konto med anteckningsböcker](#enable-notebooks-in-a-new-cosmos-account) eller aktiverar [anteckningsböcker på ett befintligt konto](#enable-notebooks-in-an-existing-cosmos-account) i något av dessa regioner.

Inbyggda Jupyter-anteckningsböcker i Azure Cosmos DB gör att du kan analysera och visualisera dina data från Azure-portalen. Den här artikeln beskriver hur du aktiverar den här funktionen för ditt Azure Cosmos DB-konto.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Aktivera anteckningsböcker i ett nytt Cosmos-konto
1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj Skapa en**resursDatabaser** >  **Create a resource** > **Azure Cosmos DB**.
1. Välj **Anteckningsböcker**på sidan **Skapa Azure Cosmos DB-konto** . 
 
    ![Välj alternativet anteckningsböcker i Azure Cosmos DB Skapa blad](media/enable-notebooks/create-new-account-with-notebooks.png)
1. Välj **Granska + skapa**. Du kan hoppa över alternativet **Nätverk** och **taggar.** 
1. Granska kontoinställningarna och välj sedan **Skapa**. Det tar några minuter att skapa kontot. Vänta tills portalsidan visar meddelandet **Distributionen är klar**. 

    ![Meddelandefönstret i Azure-portalen](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. Välj **Gå till resurs** för att gå till sidan för Azure Cosmos DB-kontot. 

    ![Sidan för Azure Cosmos DB-kontot](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. Navigera till fönstret **Data explorer.** Du bör nu se din arbetsyta för anteckningsböcker.

    ![Nya Azure Cosmos DB-anteckningsböcker arbetsyta](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Aktivera anteckningsböcker i ett befintligt Cosmos-konto
Du kan också aktivera anteckningsböcker på befintliga konton. Det här steget behöver bara göras en gång per konto.

1. Navigera till **fönstret Data explorer** i ditt Cosmos-konto.
1. Välj **Aktivera anteckningsböcker**.

    ![Skapa en ny arbetsyta för anteckningsböcker i Data Explorer](media/enable-notebooks/enable-notebooks-workspace.png)
1. Detta kommer att uppmana dig att skapa en ny arbetsyta för anteckningsböcker. Välj **Slutförd installation.**
1. Ditt konto är nu aktiverat för att använda anteckningsböcker!

## <a name="create-and-run-your-first-notebook"></a>Skapa och kör din första anteckningsbok

Om du vill kontrollera att du kan använda anteckningsböcker väljer du en av anteckningsböckerna under Exempelböcker. Då sparas en kopia av anteckningsboken på arbetsytan och den öppnas.

I det här exemplet använder vi **GettingStarted.ipynb**. 

![Visa den bärbara kommastarten.ipynb](media/enable-notebooks/select-getting-started-notebook.png)

Så här kör du anteckningsboken:
1. Markera den första kodcellen som innehåller Python-kod. 
1. Välj **Kör** om du vill köra cellen. Du kan också använda **Skift + Retur** för att köra cellen.
1. Uppdatera resursfönstret om du vill visa databasen och behållaren som har skapats.

    ![Kör komma igång anteckningsbok](media/enable-notebooks/run-first-notebook-cell.png)

Du kan också välja **Ny anteckningsbok** om du vill skapa en ny anteckningsbok eller ladda upp en befintlig anteckningsboksfil (.ipynb) genom att välja Ladda **upp fil** på menyn **Mina anteckningsböcker.** 

![Skapa eller ladda upp en ny anteckningsbok](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om fördelarna med [bärbara Azure Cosmos DB Jupyter-datorer](cosmosdb-jupyter-notebooks.md)
