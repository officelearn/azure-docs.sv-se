---
title: Hämta information om databas information för för hands versionen av Azure blockchain Workbench
description: Lär dig hur du hämtar Azure blockchain Workbench Preview-databas och databas server information.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f4a4eaab9a03aeed27e29eb645b6e22a028b243b
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845071"
---
# <a name="get-information-about-your-azure-blockchain-workbench-preview-database"></a>Hämta information om din Azure blockchain Workbench Preview-databas

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