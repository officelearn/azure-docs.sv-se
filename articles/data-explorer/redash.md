---
title: Visualisera Azure-Datautforskaren med streckat
description: I den här artikeln får du lära dig hur du visualiserar data i Azure Datautforskaren med den omstreckade inbyggda kopplingen.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8bb8711bc4a6134ec740a55d9f5d5794b2de77ca
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588595"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Visualisera data från Azure Datautforskaren i streckat

[Omstreck](https://www.redash.io/) ansluter och frågar dina data källor och skapar instrument paneler för att visualisera data och dela dem med peer-datorer. I den här artikeln får du lära dig hur du konfigurerar Azure Datautforskaren som en data källa för att göra om streck och sedan visualisera data.

## <a name="prerequisites"></a>Nödvändiga komponenter

1. [Skapa kluster och databas](create-cluster-database-portal.md).
1. Mata in data enligt beskrivningen i mata [in exempel data i Azure datautforskaren](ingest-sample-data.md). Mer information om inmatnings alternativ finns i [Översikt över](ingest-data-overview.md)inmatning.

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Skapa Azure Datautforskaren Connector i streckat 

1. Logga in för att [omstreck](https://www.redash.io/). Välj **Kom igång** för att skapa ett konto.
1. Under **Låt oss komma igång**väljer du **Anslut en data källa**.

    ![Anslut en data Källa](media/redash/connect-data-source.png)

1. I fönstret **skapa en ny data källa** väljer du **Azure datautforskaren (Kusto)** och väljer sedan **skapa**. 

    ![Välj data källa för Azure Datautforskaren](media/redash/select-adx-data-source.png)

1. I fönstret **Azure datautforskaren (Kusto)** fyller du i följande formulär och väljer **skapa**.

    ![Fönstret inställningar för Azure Datautforskaren (Kusto)](media/redash/adx-settings-window.png)

1. I fönstret **Inställningar** väljer du **Spara** och **Testa anslutning** för att testa din **Azure datautforskaren-anslutning (Kusto)** data källa.

## <a name="create-queries-in-redash"></a>Skapa frågor i omstreck

1. I övre vänstra delen av strecken väljer du **skapa** > **fråga**. Klicka på **ny fråga** och Byt namn på frågan.

    ![Skapa fråga](media/redash/create-query.png)

1. Skriv din fråga i det översta redigerings fönstret och välj **Spara** och **Kör**. Välj **publicera** för att publicera frågan för framtida användning.

    ![Spara och kör fråga](media/redash/save-and-execute-query.png)

    I det vänstra fönstret kan du se anslutnings namnet för data källan (**GitHub Connector** i vårt flöde) i den nedrullningsbara menyn och tabellerna i den valda databasen. 

1. Visa frågeresultaten i det nedre fönstret i mitten. Skapa en visualisering för att gå med frågan genom att välja knappen **ny visualisering** .

    ![Ny visualisering](media/redash/new-visualization.png)

1. På sidan visualisering väljer du **visualiserings typ** och relevanta fält som **X kolumn** och **Y-kolumn**. **Spara** visualiseringen.

    ![Konfigurera och spara visualisering](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>Skapa en fråga med en parameter

1. Skapa en ny fråga genom att **skapa** en > **fråga** . Lägg till en parameter till den med hjälp av {{}} klammerparenteser. Välj **{{}}** för att öppna **Lägg till parameter** fönster. Du kan också välja *inställnings ikonen* för att ändra attributen för en befintlig parameter och öppna fönstret **< parameter_name >** . 

    ![Infoga parameter](media/redash/insert-parameter.png)

1. Namnge din parameter. Välj **typ**: **fråga baserad List Rute lista** från List Rute meny. Välj **OK**

    ![fråga baserad nedrullningsbar listruta](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > Frågan använder flera värden, och därför måste du inkludera följande syntax `| where Type in ((split('{{Type}}', ',')))`. Mer information finns [i i-Operator](/azure/kusto/query/inoperator). Detta resulterar i [flera frågeparametrar i en retanke-app](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io)

## <a name="create-a-dashboard-in-redash"></a>Skapa en instrument panel i omstreck

1. **Skapa** > **instrument**panel om du vill skapa din instrument panel. Alternativt väljer du befintlig instrument panel, **instrument paneler** > väljer en instrument panel i listan.

    ![Skapa instrument panel](media/redash/create-dashboard.png)

1. I fönstret **ny instrument panel** , namnge din instrument panel och välj **Spara**. I **< Dashboard_name >** -fönstret väljer du **Lägg till widget** för att skapa en ny widget. 

1. I fönstret **Lägg till widget** väljer du frågenamn, **sedan visualisering**och **parametrar**. Välj **Lägg till på instrument panelen**

   ![Välj visualiseringar och Lägg till på instrument panelen](media/redash/add-widget-window.png)

1. Slutför skapandet av instrument panelen genom att välja **klar redigering** .

1.  I redigerings läget för instrument panelen väljer du **Använd filter på instrument panels nivå** för att använda **typ** parametern som definierats tidigare.

    ![Slutför skapande av instrument panel](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>Nästa steg

* [Skriva frågor för Azure Data Explorer](write-queries.md)


