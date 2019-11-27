---
title: Hämta information om Azure Blockchain Workbench-databas
description: Lär dig hur du hämtar Azure blockchain Workbench Preview-databas och databas server information.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 2b3190a9d042be8ead1ff3d5ef48d4a2a19e8963
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324695"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Hämta information om din Azure Blockchain Workbench-databas

Den här artikeln visar hur du får detaljerad information om din databas för för hands versionen av Azure blockchain Workbench.

## <a name="overview"></a>Översikt

Information om program, arbetsflöden och smart kontraktskörning kan hämtas med databasvyer i Blockchain Workbench SQL DB. Utvecklare kan använda den här informationen när de använder verktyg som Microsoft Excel, Power BI, Visual Studio och SQL Server Management Studio.

Innan en utvecklare kan ansluta till databasen, måste de:

* Extern klientåtkomst tillåts i brandväggen för databasen. Den här artikeln om att konfigurera en databasbrandväggen förklarar hur du tillåter åtkomst.
* Databasservernamn och databasnamn.

## <a name="connect-to-the-blockchain-workbench-database"></a>Ansluta till Blockchain Workbench-databasen

Så här ansluter du till databasen:

1. Logga in på Azure Portal med ett konto som har **ägar** behörigheter för Azure blockchain Workbench-resurserna.
2. Välj **Resursgrupper** i det vänstra navigeringsfönstret.
3. Välj namnet på resursgruppen för din Blockchain Workbench-distribution.
4. Sortera listan med resurser genom att välja **Typ** och välj sedan din **SQL-server**. Den sorterade listan i nästa skärmdump visar två SQL-databaser, ”master” och en som använder ”lhgn” som **resursprefix**.

   ![Sorterad lista över Blockchain Workbench-resurser](./media/getdb-details/sorted-workbench-resource-list.png)

5. Om du vill se detaljerad information om Blockchain Workbench-databasen väljer du länken för databasen med det **resursprefix** du angav för distributionen av Blockchain Workbench.

   ![Databasinformation](./media/getdb-details/workbench-db-details.png)

Med databasens servernamn och databasnamn kan du ansluta till Blockchain Workbench-databasen med hjälp av utvecklings- eller rapporteringsverktyg.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Databasvyer i Azure Blockchain Workbench](database-views.md)