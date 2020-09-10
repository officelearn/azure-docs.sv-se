---
title: 'Självstudie: kom igång skapa en Synapse-arbetsyta'
description: I den här självstudien får du lära dig hur du skapar en Synapse-arbetsyta, en SQL-pool och en Apache Spark pool.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 08/27/2020
ms.openlocfilehash: 949afc00b12b1238973f832270d57fff3c2db5f9
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89669546"
---
# <a name="create-a-synapse-workspace"></a>Skapa en Synapse-arbetsyta

I den här självstudien får du lära dig hur du skapar en Synapse-arbetsyta, en SQL-pool och en Apache Spark pool. 

## <a name="create-a-synapse-workspace"></a>Skapa en Synapse-arbetsyta

1. Öppna [Azure Portal](https://portal.azure.com)och högst upp i Sök efter **Synapse**.
1. I Sök resultaten under **tjänster**väljer du **Azure Synapse Analytics (för hands versioner av arbets ytor)**.
1. Välj **Lägg till** för att skapa en arbets yta med följande inställningar:

    |Flik|Inställning | Föreslaget värde | Beskrivning |
    |---|---|---|---|
    |Grundläggande inställningar|**Namn på arbetsyta**|Du kan ge det något annat namn.| I det här dokumentet använder vi min **arbets yta**.|
    |Grundläggande inställningar|**Region**|Matcha lagrings kontots region.|

1. Du behöver ett ADLSGEN2-konto för att skapa en arbets yta. Det enklaste alternativet att skapa en ny. Om du vill använda en befintlig konfiguration igen måste du utföra en del ytterligare konfiguration. 
1. ALTERNATIV 1 Skapa ett nytt ADLSGEN2-konto 
    1. Under **välj Data Lake Storage gen 2**, klickar du på **Skapa nytt** och ge den namnet **contosolake**.
    1. Under **välj Data Lake Storage gen 2**, klickar du på **fil system** och namnger IT- **användare**.
1. ALTERNATIV 2 med ett befintligt ADLSGEN2-konto. Mer information finns i avsnittet **förbereda ett ADLSGEN2 lagrings konto** längst ned i det här dokumentet.
1. Din Azure Synapse-arbetsyta kommer att använda det här lagrings kontot som det primära lagrings kontot och behållaren för att lagra data i arbets ytan. Arbets ytan lagrar data i Apache Spark tabeller. Programmet lagrar Spark-programloggarna under en mapp med namnet **/Synapse/workspacename**.
1. Välj **Granska + skapa** > **Skapa**. Din arbets yta är klar på några minuter.


## <a name="open-synapse-studio"></a>Öppna Synapse Studio

När din Azure Synapse-arbetsyta har skapats kan du öppna Synapse Studio på två sätt:

* Öppna din Synapse-arbetsyta i [Azure Portal](https://portal.azure.com). Överst i **översikts** avsnittet väljer du **Starta Synapse Studio**.
* Gå till `https://web.azuresynapse.net` och logga in på din arbets yta.

## <a name="create-a-sql-pool"></a>Skapa en SQL-pool

1. I Synapse Studio väljer du **Hantera**  >  **SQL-pooler**i det vänstra fönstret.
1. Välj **ny** och ange följande inställningar:

    |Inställning | Föreslaget värde | 
    |---|---|---|
    |**SQL-poolnamn**| **SQLDB1**|
    |**Prestanda nivå**|**DW100C**|
    |||

1. Välj **Granska + skapa** > **Skapa**. SQL-poolen är klar om några minuter. SQL-poolen är kopplad till en SQL-adresspool som också kallas **SQLDB1**.

En SQL-pool förbrukar fakturerbara resurser så länge den är aktiv. Du kan pausa poolen senare för att minska kostnaderna.

## <a name="create-an-apache-spark-pool"></a>Skapa en Apache Spark pool

1. I Synapse Studio väljer du **Hantera**  >  **Apache Spark pooler**i det vänstra fönstret.
1. Välj **ny** och ange följande inställningar:

    |Inställning | Föreslaget värde | 
    |---|---|---|
    |**Namn på Apache Spark bassäng**|**Spark1**
    |**Node-storlek**| **Liten**|
    |**Antal noder**| Ange minst 3 och maximalt 3|

1. Välj **Granska + skapa** > **Skapa**. Apache Spark-poolen är klar efter några sekunder.

> [!NOTE]
> Trots namnet är en Apache Spark pool inte som en SQL-pool. Det är bara grundläggande metadata som du använder för att berätta för Azure Synapse-arbetsytan hur du interagerar med Spark.

Eftersom de är metadata kan inte Spark-pooler startas eller stoppas.

När du utför Spark-aktivitet i Azure Synapse anger du en spark-pool som ska användas. Poolen talar om för Azure Synapse hur många Spark-resurser som ska användas. Du betalar bara för de resurser du använder. När du aktivt slutar använda poolen är resurserna automatiskt utgångna och återvinns.

> [!NOTE]
> Spark-databaser skapas oberoende av Spark-pooler. En arbets yta har alltid en spark-databas som kallas **default**. Du kan skapa ytterligare Spark-databaser.

## <a name="the-sql-on-demand-pool"></a>SQL on-demand-poolen

Varje arbets yta levereras med en fördefinierad pool som kallas **SQL på begäran**. Det går inte att ta bort poolen. SQL on-demand-poolen gör att du kan arbeta med SQL utan att behöva skapa eller tänka på att hantera en SQL-pool i Azure Synapse.

Till skillnad från andra typer av pooler baseras faktureringen av SQL på begäran baserat på mängden data som genomsöks för att köra frågan, inte antalet resurser som används för att köra frågan.

* SQL på begäran har egna SQL-databaser på begäran som finns oberoende av en SQL-pool på begäran.
* En arbets yta har alltid exakt en SQL-pool på begäran som heter **SQL på begäran**.

## <a name="preparing-a-adlsgen2-storage-account"></a>Förbereda ett ADLSGEN2-lagrings konto

### <a name="perform-the-following-steps-before-you-create-your-workspace"></a>Utför följande steg innan du skapar din arbets yta

1. Öppna [Azure-portalen](https://portal.azure.com).
1. Navigera till ditt befintliga lagrings konto
1. Välj **åtkomst kontroll (IAM)** i den vänstra rutan. 
1. Tilldela följande roller eller kontrol lera att de redan har tilldelats:
    * Tilldela rollen som **ägare** .
    * Tilldela rollen som **ägare av Storage BLOB-data** .
1. I den vänstra rutan väljer du **behållare** och skapar en behållare.
1. Du kan ge containern ett namn. I det här dokumentet använder vi namnet  **användare**.
1. Godkänn standardinställningen **offentlig åtkomst nivå**och välj sedan **skapa**.

### <a name="perform-the-following-steps-after-you-create-your-workspace"></a>Utför följande steg när du har skapat din arbets yta

Konfigurera åtkomst till lagrings kontot från din arbets yta. Hanterade identiteter för din Azure Synapse-arbetsyta kanske redan har åtkomst till lagrings kontot. Följ dessa steg för att se till att:

1. Öppna [Azure Portal](https://portal.azure.com) och det primära lagrings kontot som valts för din arbets yta.
1. Välj **åtkomst kontroll (IAM)** i den vänstra rutan.
1. Tilldela följande roller eller kontrol lera att de redan har tilldelats. Vi använder samma namn för arbets ytans identitet och arbets ytans namn.
    * För rollen **Storage BLOB data Contributor** på lagrings kontot tilldelar du min **arbets yta** som arbets ytans identitet.
    * Tilldela min **arbets yta** som arbets ytans namn.

1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera med en SQL-pool](get-started-analyze-sql-pool.md)
