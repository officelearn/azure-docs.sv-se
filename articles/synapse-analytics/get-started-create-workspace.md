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
ms.date: 11/21/2020
ms.openlocfilehash: 949992418cb19c5c69ed8da92e9c2f0fc5f2f791
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445420"
---
# <a name="creating-a-synapse-workspace"></a>Skapa en Synapse-arbetsyta

I den här självstudien får du lära dig hur du skapar en Synapse-arbetsyta, en dedikerad SQL-pool och en server lös Apache Spark pool. 

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här själv studie kursen måste du ha åtkomst till en resurs grupp som du har tilldelats **ägar** rollen för. Skapa arbets ytan Synapse i den här resurs gruppen.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Skapa en Synapse-arbetsyta i Azure Portal

1. Öppna [Azure Portal](https://portal.azure.com)och högst upp i Sök efter **Synapse**.
1. I Sök resultaten under **tjänster** väljer du **Azure Synapse Analytics**.
1. Välj **Lägg till** för att skapa en arbets yta.
1. I **grunderna** anger du önskad **prenumeration**, **resurs grupp**, **region** och väljer sedan ett namn på arbets ytan. I den här självstudien använder vi min **arbets yta**.
1. Gå till **välj Data Lake Storage gen 2**. 
1. Klicka på **Skapa nytt** och ge den namnet **contosolake**.
1. Klicka på **fil system** och ge den namnet **användare**. Då skapas en behållare med namnet **användare**
1. Arbets ytan kommer att använda det här lagrings kontot som det primära lagrings kontot för Spark-tabeller och Spark-programloggarna.
1. Välj **Granska + skapa** > **Skapa**. Din arbets yta är klar på några minuter.

> [!NOTE]
> Om du vill aktivera arbets ytans funktioner från en befintlig dedikerad SQL-pool (tidigare SQL DW) läser du så [här aktiverar du en arbets yta för din dedikerade SQL-pool (tidigare SQL DW)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Öppna Synapse Studio

När din Azure Synapse-arbetsyta har skapats kan du öppna Synapse Studio på två sätt:

* Öppna din Synapse-arbetsyta i [Azure Portal](https://portal.azure.com). Överst i **översikts** avsnittet väljer du **Starta Synapse Studio**.
* Gå till `https://web.azuresynapse.net` och logga in på din arbets yta.

## <a name="create-a-dedicated-sql-pool"></a>Skapa en dedikerad SQL-pool

1. I Synapse Studio väljer du **Hantera**  >  **SQL-pooler** i det vänstra fönstret.
1. Välj **nytt**
1. Välj **SQLPOOL1** för **SQL-poolnamn**
1. Välj **DW100C** för **prestanda nivå**
1. Välj **Granska + skapa** > **Skapa**. Din dedikerade SQL-pool är klar om några minuter. Din dedikerade SQL-pool är kopplad till en dedikerad SQL-adresspool som också kallas **SQLPOOL1**.

En dedikerad SQL-pool förbrukar fakturerbara resurser så länge den är aktiv. Du kan pausa poolen senare för att minska kostnaderna.

> [!NOTE] 
> När du skapar en ny dedikerad SQL-pool (tidigare SQL DW) på din arbets yta öppnas sidan dedikerad etablering av SQL-pool. Etableringen sker på den logiska SQL-servern.

## <a name="create-a-serverless-apache-spark-pool"></a>Skapa en server lös Apache Spark-pool

1. I Synapse Studio väljer du **Hantera**  >  **Apache Spark pooler** i det vänstra fönstret.
1. Välj **nytt** 
1. Ange **Spark1** som **namn på Apache Spark pool** .
1. För **Node-storlek** anger du **liten**.
1. För **antal noder** ställer du in minst 3 och maximalt 3
1. Välj **Granska + skapa** > **Skapa**. Apache Spark-poolen är klar efter några sekunder.

Spark-poolen meddelar Azure Synapse hur många Spark-resurser som ska användas. Du betalar bara för de resurser du använder. När du aktivt slutar använda poolen är resurserna automatiskt utgångna och återvinns.

## <a name="the-built-in-serverless-sql-pool"></a>Den inbyggda SQL-poolen utan Server

Varje arbets yta levereras med en fördefinierad Server lös SQL-pool som kallas **inbyggd**. Det går inte att ta bort poolen. SQL-pooler utan Server gör att du kan använda SQL utan att behöva reservera kapacitet med dedikerade SQL-pooler. Till skillnad från dedikerade SQL-pooler baseras faktureringen för en server lös SQL-pool på mängden data som genomsöks för att köra frågan, inte antalet allokerade kapacitet till poolen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera med en dedikerad SQL-pool](get-started-analyze-sql-pool.md)
