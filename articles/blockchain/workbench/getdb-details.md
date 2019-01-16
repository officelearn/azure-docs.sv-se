---
title: Hämta information om Azure Blockchain Workbench-databas
description: Lär dig hur du hämtar information om Azure Blockchain Workbench-databas och -databasserver.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 0d806491c58c4b1881adc2fd830de7c7b9f0859d
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331936"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Hämta information om din Azure Blockchain Workbench-databas

Den här artikeln beskriver hur du kan hämta detaljerad information om din Azure Blockchain Workbench-databas.

## <a name="overview"></a>Översikt

Information om program, arbetsflöden och smart kontraktskörning kan hämtas med databasvyer i Blockchain Workbench SQL DB. Utvecklare kan använda den här informationen när du använder Verktyg, till exempel Microsoft Excel, Power BI, Visual Studio och SQL Server Management Studio.

Innan en utvecklare kan ansluta till databasen, måste de:

* Extern klientåtkomst tillåts i brandväggen för databasen. Den här artikeln om att konfigurera en databasbrandväggen förklarar hur du tillåter åtkomst.
* Databasservernamn och databasnamn.

## <a name="connect-to-the-blockchain-workbench-database"></a>Ansluta till Blockchain Workbench-databasen

Så här ansluter du till databasen:

1. Logga in på Azure portal med ett konto som har **ägare** behörigheter för Azure Blockchain Workbench-resurser.
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