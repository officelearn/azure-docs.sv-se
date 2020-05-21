---
title: Använda Synapse Studio (för hands version)
description: I den här snabb starten får du se och lära dig hur enkelt det är att fråga olika typer av filer med hjälp av Synapse Studio.
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 182c6a20af51508c357079b9542b2bda695fdae4
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658555"
---
# <a name="quickstart-use-synapse-studio-preview"></a>Snabb start: Använd Synapse Studio (för hands version)

I den här snabb starten får du lära dig hur du frågar filer med hjälp av Synapse Studio.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="prerequisites"></a>Krav

[Skapa en Azure Synapse-arbetsyta och tillhör ande lagrings konto](quickstart-create-workspace.md).

## <a name="launch-synapse-studio"></a>Starta Synapse Studio

I din Azure Synapse-arbetsyta i Azure Portal klickar du på **Starta Synapse Studio**.

![Starta Synapse Studio](./media/quickstart-synapse-studio/launch-synapse-workspace.png)

Du kan också starta Synapse Studio genom att klicka på [Azure Synapse Analytics](https://web.azuresynapse.net) och ange lämpliga värden för klient-, prenumerations-och arbets ytan.

## <a name="browse-storage-accounts"></a>Bläddra bland lagrings konton

När du har öppnat Synapse Studio bläddrar du till **data** och expanderar **lagrings konton** för att Visa lagrings kontot i arbets ytan.

![Bläddra bland filer på lagrings platsen](./media/quickstart-synapse-studio/browse-files-on-storage.png)

Du kan skapa nya mappar och ladda upp filer med hjälp av länkarna i verktygsfältet för att organisera dina filer.

## <a name="query-files-on-storage-account"></a>Fråga efter filer på lagrings kontot

> [!IMPORTANT]
> Du måste vara medlem i `Storage Blob Reader` rollen för den underliggande lagringen för att kunna fråga filerna. Lär dig hur du [tilldelar **data läsare för Storage BLOB-dataläsaren** eller **Storage BLOB data Contributor** RBAC-behörigheter på Azure Storage](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role).

1. Ladda upp vissa `PARQUET` filer.
2. Välj en eller flera filer och skapa sedan ett nytt SQL-skript eller en spark-anteckningsbok för att se innehållet i filerna. Om du vill skapa en antecknings bok måste du skapa en [Apache Spark pool i Synapse-arbetsytor](quickstart-create-apache-spark-pool-studio.md).

   ![Fråga efter filer på lagrings platsen](./media/quickstart-synapse-studio/query-files-on-storage.png)

3. Kör den genererade frågan eller antecknings boken för att se innehållet i filen.

   ![Visa innehållet i filen](./media/quickstart-synapse-studio/query-files-on-storage-result.png)

4. Du kan ändra frågan för att filtrera och sortera resultat. Hitta språk funktioner som är tillgängliga i [Översikt över](sql/overview-features.md)SQL på begäran i SQL-funktioner.

## <a name="next-steps"></a>Nästa steg

- Gör det möjligt för Azure AD-användare att fråga filer genom att använda [ **Storage BLOB data Reader** eller **Storage BLOB data Contributor** RBAC-behörighet på Azure Storage](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role)
- [Fråga efter filer på Azure Storage med SQL på begäran](sql/on-demand-workspace-overview.md)
- [Skapa Apache Spark-pool med hjälp av Azure-portalen](quickstart-create-apache-spark-pool-portal.md)
- [Skapa Power BIs rapport för filer som lagras på Azure Storage](sql/tutorial-connect-power-bi-desktop.md)
