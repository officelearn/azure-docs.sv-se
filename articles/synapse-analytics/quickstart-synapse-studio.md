---
title: Använda Synapse Studio (förhandsgranskning)
description: I den här snabbstarten ser och lär du dig hur enkelt det är att fråga olika typer av filer med Synapse Studio.
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 294f53fe929343708bdbb9564b23c46865cf02ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423868"
---
# <a name="quickstart-using-synapse-studio-preview"></a>Snabbstart: Använda Synapse Studio (förhandsgranskning)

I den här snabbstarten får du lära dig att fråga filer med Synapse Studio.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="prerequisites"></a>Krav

[Skapa en Azure Synapse-arbetsyta och associerat lagringskonto](quickstart-create-workspace.md).

## <a name="launch-synapse-studio"></a>Starta Synapse Studio

Klicka på **Starta Synapse Studio**i Din Azure Synapse-arbetsyta i Azure-portalen .

![Starta Synapse Studio](./media/quickstart-synapse-studio/launch-synapse-workspace.png)

Alternativt kan du starta Synapse Studio genom att klicka på [Azure Synapse Analytics](https://web.azuresynapse.net) och tillhandahålla lämpliga klient-, prenumerations- och arbetsytasvärden.

## <a name="browse-storage-accounts"></a>Bläddra bland lagringskonton

När du har öppnat Synapse Studio bläddrar du till **Data** och expanderar sedan **Lagringskonton** för att visa lagringskontot på arbetsytan.

![Bläddra bland filer vid lagring](./media/quickstart-synapse-studio/browse-files-on-storage.png)

Du kan skapa nya mappar och ladda upp filer med hjälp av länkarna i verktygsfältet för att ordna dina filer.

## <a name="query-files-on-storage-account"></a>Fråga filer på lagringskonto

> [!IMPORTANT]
> Du måste vara medlem `Storage Blob Reader` i rollen på det underliggande lagringsutrymmet för att kunna fråga filerna. Lär dig hur du [tilldelar RBAC-behörigheter för **lagringsblobbdataläsare** eller **lagringsblobbdatamedarbetare** på Azure Storage](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role).

1. Ladda `PARQUET` upp några filer.
2. Markera en eller flera filer och skapa sedan ett nytt SQL-skript eller en Spark-anteckningsbok för att se innehållet i filerna. Om du vill skapa en anteckningsbok måste du skapa [Apache Spark-poolen på arbetsytan](spark/apache-spark-notebook-create-spark-use-sql.md).

   ![Fråga filer vid lagring](./media/quickstart-synapse-studio/query-files-on-storage.png)

3. Kör den genererade frågan eller anteckningsboken för att se innehållet i filen:

   ![Se innehållet i filen](./media/quickstart-synapse-studio/query-files-on-storage-result.png)

4. Du kan ändra frågan för att filtrera och sortera resultat. Hitta språkfunktioner som är tillgängliga i SQL on-demand i [SQL-funktioner översikt](sql/overview-features.md).

## <a name="next-steps"></a>Nästa steg

- Aktivera Azure AD-användare att fråga filer [genom att tilldela RBAC-behörigheter för **storage blob dataläsare** eller **storage blob data contributor** i Azure Storage](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role)
- [Fråga filer på Azure Storage med SQL On-Demand](sql/on-demand-workspace-overview.md)
- [Skapa Apache Spark pool](spark/apache-spark-notebook-create-spark-use-sql.md)
- [Skapa Power BI-rapport om filer som lagras på Azure Storage](sql/tutorial-connect-power-bi-desktop.md)
