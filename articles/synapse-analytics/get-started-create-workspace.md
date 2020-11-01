---
title: 'Självstudie: kom igång skapa en Synapse-arbetsyta'
description: I den här självstudien får du lära dig hur du skapar en Synapse-arbetsyta, en SQL-pool och en Apache Spark pool.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: 11ff6ff5f7a999020a1b4bd3ffe933cc6a5db5a0
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147002"
---
# <a name="creating-a-synapse-workspace"></a>Skapa en Synapse-arbetsyta

I den här självstudien får du lära dig hur du skapar en Synapse-arbetsyta, en SQL-pool och en Apache Spark pool. 

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här själv studie kursen måste du ha åtkomst till en resurs grupp som du har tilldelats **ägar** rollen för. Skapa arbets ytan Synapse i den här resurs gruppen.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Skapa en Synapse-arbetsyta i Azure Portal

1. Öppna [Azure Portal](https://portal.azure.com)och högst upp i Sök efter **Synapse** .
1. I Sök resultaten under **tjänster** väljer du **Azure Synapse Analytics (för hands versioner av arbets ytor)** .
1. Välj **Lägg till** för att skapa en arbets yta.
1. I **grunderna** anger du önskad **prenumeration** , **resurs grupp** , **region** och väljer sedan ett namn på arbets ytan. I den här självstudien använder vi min **arbets yta** .
1. Du behöver ett ADLSGEN2-konto och en behållare i det kontot för att skapa en arbets yta. Det enklaste alternativet att skapa en ny. Om du vill använda en befintlig konfiguration igen måste du utföra en del ytterligare konfiguration. 
    1. Arbets ytan Synapse kommer att använda den här behållaren som standard plats för att lagra Spark-loggar och data för Spark-tabeller.
1. ALTERNATIV 1 Skapa ett nytt ADLSGEN2-konto 
    1. Gå till **välj Data Lake Storage gen 2** . 
    1. Klicka på **Skapa nytt** och ge den namnet **contosolake** .
    1. Klicka på **fil system** och ge den namnet **användare** . Då skapas en behållare med namnet **användare**
1. ALTERNATIV 2 med ett befintligt ADLSGEN2-konto. Mer information finns i avsnittet **förbereda ett ADLSGEN2 lagrings konto** längst ned i det här dokumentet.
1. Din Azure Synapse-arbetsyta kommer att använda det här lagrings kontot som det primära lagrings kontot och behållaren för att lagra data i arbets ytan. Arbets ytan lagrar data i Apache Spark tabeller. Programmet lagrar Spark-programloggarna under en mapp med namnet **/Synapse/workspacename** .
1. Välj **Granska + skapa** > **Skapa** . Din arbets yta är klar på några minuter.

## <a name="open-synapse-studio"></a>Öppna Synapse Studio

När din Azure Synapse-arbetsyta har skapats kan du öppna Synapse Studio på två sätt:

* Öppna din Synapse-arbetsyta i [Azure Portal](https://portal.azure.com). Överst i **översikts** avsnittet väljer du **Starta Synapse Studio** .
* Gå till `https://web.azuresynapse.net` och logga in på din arbets yta.

## <a name="create-a-sql-pool"></a>Skapa en SQL-pool

1. I Synapse Studio väljer du **Hantera**  >  **SQL-pooler** i det vänstra fönstret.
1. Välj **ny** och ange följande inställningar:

    |Inställning | Föreslaget värde | 
    |---|---|---|
    |**SQL-poolnamn**| **SQLDB1**|
    |**Prestanda nivå**|**DW100C**|
    |||

1. Välj **Granska + skapa** > **Skapa** . SQL-poolen är klar om några minuter. SQL-poolen är kopplad till en SQL-adresspool som också kallas **SQLDB1** .

En SQL-pool förbrukar fakturerbara resurser så länge den är aktiv. Du kan pausa poolen senare för att minska kostnaderna.

## <a name="create-a-serverless-apache-spark-pool"></a>Skapa en server lös Apache Spark-pool

1. I Synapse Studio väljer du **Hantera**  >  **Apache Spark pooler** i det vänstra fönstret.
1. Välj **ny** och ange följande inställningar:

    |Inställning | Föreslaget värde | 
    |---|---|---|
    |**Namn på Apache Spark bassäng**|**Spark1**
    |**Node-storlek**| **Liten**|
    |**Antal noder**| Ange minst 3 och maximalt 3|

1. Välj **Granska + skapa** > **Skapa** . Apache Spark-poolen är klar efter några sekunder.

När du utför Spark-aktivitet i Azure Synapse anger du en spark-pool som ska användas. Poolen talar om för Azure Synapse hur många Spark-resurser som ska användas. Du betalar bara för de resurser du använder. När du aktivt slutar använda poolen är resurserna automatiskt utgångna och återvinns.

> [!NOTE]
> Spark-databaser skapas oberoende av Spark-pooler. En arbets yta har alltid en spark-databas som kallas **default** . Du kan skapa ytterligare Spark-databaser.

## <a name="the-sql-on-demand-pool"></a>SQL on-demand-poolen

Varje arbets yta levereras med en fördefinierad pool som kallas **SQL på begäran** . Det går inte att ta bort poolen. SQL on-demand-poolen gör att du kan arbeta med SQL utan att behöva skapa eller tänka på att hantera en SQL-pool i Azure Synapse.

Till skillnad från dedikerade SQL-pooler baseras faktureringen av SQL på begäran baserat på mängden data som genomsöks för att köra frågan, inte antalet resurser som används för att köra frågan.

## <a name="preparing-a-adlsgen2-storage-account"></a>Förbereda ett ADLSGEN2-lagrings konto

### <a name="perform-the-following-steps-before-you-create-your-workspace"></a>Utför följande steg innan du skapar din arbets yta

1. Öppna [Azure-portalen](https://portal.azure.com).
1. Navigera till ditt befintliga lagrings konto
1. Välj **åtkomst kontroll (IAM)** i den vänstra rutan. 
1. Tilldela följande roller eller kontrol lera att de redan har tilldelats:
    * Tilldela rollen som **ägare** .
    * Tilldela rollen som **ägare av Storage BLOB-data** .
1. I den vänstra rutan väljer du **behållare** och skapar en behållare.
1. Du kan ge containern ett namn. I det här dokumentet använder vi namnet  **användare** .
1. Godkänn standardinställningen **offentlig åtkomst nivå** och välj sedan **skapa** .

### <a name="perform-the-following-steps-after-you-create-your-workspace"></a>Utför följande steg när du har skapat din arbets yta

Konfigurera åtkomst till lagrings kontot från din arbets yta. Hanterade identiteter för din Azure Synapse-arbetsyta kanske redan har åtkomst till lagrings kontot. Följ dessa steg för att se till att:

1. Öppna [Azure Portal](https://portal.azure.com) och det primära lagrings kontot som valts för din arbets yta.
1. Välj **åtkomst kontroll (IAM)** i den vänstra rutan.
1. Tilldela följande roller eller kontrol lera att de redan har tilldelats. Vi använder samma namn för arbets ytans identitet och arbets ytans namn.
    * För rollen **Storage BLOB data Contributor** på lagrings kontot tilldelar du min **arbets yta** som arbets ytans identitet.
    * Tilldela min **arbets yta** som arbets ytans namn.
1. Välj **Spara** .


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera med en SQL-pool](get-started-analyze-sql-pool.md)
