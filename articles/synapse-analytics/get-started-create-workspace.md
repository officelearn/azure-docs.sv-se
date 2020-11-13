---
title: 'Snabb start: kom igång – skapa en Synapse-arbetsyta'
description: I den här självstudien får du lära dig hur du skapar en Synapse-arbetsyta, en dedikerad SQL-pool och en server lös Apache Spark pool.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: 65d89436486940ad5239dd2c3e83401b82234a21
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555472"
---
# <a name="creating-a-synapse-workspace"></a>Skapa en Synapse-arbetsyta

I den här självstudien får du lära dig hur du skapar en Synapse-arbetsyta, en dedikerad SQL-pool och en server lös Apache Spark pool. 

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här själv studie kursen måste du ha åtkomst till en resurs grupp som du har tilldelats **ägar** rollen för. Skapa arbets ytan Synapse i den här resurs gruppen.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Skapa en Synapse-arbetsyta i Azure Portal

1. Öppna [Azure Portal](https://portal.azure.com)och högst upp i Sök efter **Synapse**.
1. I Sök resultaten under **tjänster** väljer du **Azure Synapse Analytics (för hands versioner av arbets ytor)**.
1. Välj **Lägg till** för att skapa en arbets yta.
1. I **grunderna** anger du önskad **prenumeration** , **resurs grupp** , **region** och väljer sedan ett namn på arbets ytan. I den här självstudien använder vi min **arbets yta**.
1. Gå till **välj Data Lake Storage gen 2**. 
1. Klicka på **Skapa nytt** och ge den namnet **contosolake**.
1. Klicka på **fil system** och ge den namnet **användare**. Då skapas en behållare med namnet **användare**
1. Arbets ytan kommer att använda det här lagrings kontot som det primära lagrings kontot för Spark-tabeller och Spark-programloggarna.
1. Välj **Granska + skapa** > **Skapa**. Din arbets yta är klar på några minuter.

## <a name="open-synapse-studio"></a>Öppna Synapse Studio

När din Azure Synapse-arbetsyta har skapats kan du öppna Synapse Studio på två sätt:

* Öppna din Synapse-arbetsyta i [Azure Portal](https://portal.azure.com). Överst i **översikts** avsnittet väljer du **Starta Synapse Studio**.
* Gå till `https://web.azuresynapse.net` och logga in på din arbets yta.

## <a name="create-a-dedicated-sql-pool"></a>Skapa en dedikerad SQL-pool

1. I Synapse Studio väljer du **Hantera**  >  **SQL-pooler** i det vänstra fönstret.
1. Välj **nytt**
1. Välj **SQLDB1** för **SQL-poolnamn**
1. Välj **DW100C** för **prestanda nivå**
1. Välj **Granska + skapa** > **Skapa**. Din dedikerade SQL-pool är klar om några minuter. Din dedikerade SQL-pool är kopplad till en dedikerad SQL-adresspool som också kallas **SQLDB1**.

En dedikerad SQL-pool förbrukar fakturerbara resurser så länge den är aktiv. Du kan pausa poolen senare för att minska kostnaderna.

## <a name="create-a-serverless-apache-spark-pool"></a>Skapa en server lös Apache Spark-pool

1. I Synapse Studio väljer du **Hantera**  >  **Apache Spark pooler** i det vänstra fönstret.
1. Välj **nytt** 
1. Ange **Spark1** som **namn på Apache Spark pool** .
1. För **Node-storlek** anger du **liten**.
1. För **antal noder** ställer du in minst 3 och maximalt 3
1. Välj **Granska + skapa** > **Skapa**. Apache Spark-poolen är klar efter några sekunder.

När du utför Spark-aktivitet i Azure Synapse anger du en spark-pool som ska användas. Poolen talar om för Azure Synapse hur många Spark-resurser som ska användas. Du betalar bara för de resurser du använder. När du aktivt slutar använda poolen är resurserna automatiskt utgångna och återvinns.

> [!NOTE]
> Spark-databaser skapas oberoende av Spark-pooler. En arbets yta har alltid en spark-databas som kallas **default**. Du kan skapa ytterligare Spark-databaser.

## <a name="the-serverless-sql-pool"></a>SQL-poolen utan Server

Varje arbets yta levereras med en fördefinierad pool som kallas **inbyggd**. Det går inte att ta bort poolen. Med SQL-poolen utan server kan du arbeta med SQL utan att behöva skapa eller tänka på att hantera en server lös SQL-pool i Azure Synapse. Till skillnad från dedikerade SQL-pooler baseras faktureringen för en server lös SQL-pool på mängden data som genomsöks för att köra frågan, inte antalet resurser som används för att köra frågan.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera med en dedikerad SQL-pool](get-started-analyze-sql-pool.md)
