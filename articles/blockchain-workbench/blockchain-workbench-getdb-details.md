---
title: Hämta information om Azure Blockchain arbetsstationen databas
description: Lär dig mer om att hämta Azure Blockchain arbetsstationen databasen och databasserverinformation.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: bf7cc85e823e6630dbd3278bc91fba85f404059f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Hämta information om Azure Blockchain arbetsstationen databasen

Den här artikeln visar hur du kan få detaljerad information om Azure Blockchain arbetsstationen databasen.

## <a name="overview"></a>Översikt

Det finns information om program, arbetsflöden och smarta kontraktet körning använda databasvyer i SQL-databas Blockchain arbetsstationen. Utvecklare kan använda den här informationen när du använder verktyg som Microsoft Excel, PowerBI, Visual Studio och SQL Server Management Studio.

Innan en utvecklare kan ansluta till databasen, måste de:

* Extern klientåtkomst tillåts i brandväggen för databasen. Den här artikeln om hur du konfigurerar en databas brandväggen artikeln förklarar hur du tillåter åtkomst.
* Database-servernamn och databasnamn.

## <a name="connect-to-the-blockchain-workbench-database"></a>Ansluta till databasen Blockchain arbetsstationen

Att ansluta till databasen:

1. Logga in på Azure-portalen med ett konto som har **ägare** behörigheter för Blockchain-arbetsstationen för Azure-resurser.
2. I det vänstra navigeringsfönstret väljer **resursgrupper**.
3. Välj namnet på resursgruppen för distributionen Blockchain arbetsstationen.
4. Välj **typen** att sortera resurslistan och välj sedan din **SQLServer**. Den sorterade listan i nästa skärmdump visar två SQL-databaser, ”överordnad” och en som använder ”lhgn” som den **resurs prefixet**.

   ![Sorterad lista över resurser Blockchain arbetsstationen](media/blockchain-workbench-getdb-details/sorted-workbench-resource-list.png)

5. Om du vill se detaljerad information om databasen Blockchain arbetsstationen, klicka på länken för databasen med den **resurs prefixet** du angett för distribution av Blockchain Workbench.

   ![Databasinformation](media/blockchain-workbench-getdb-details/workbench-db-details.png)

Databasens servernamn och databasnamn kan du ansluta till databasen Blockchain arbetsstationen med din utveckling eller rapportering verktyget.

## <a name="next-steps"></a>Nästa steg

* [Databasvyer i Azure Blockchain arbetsstationen](blockchain-workbench-database-views.md)