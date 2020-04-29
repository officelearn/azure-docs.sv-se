---
title: SQL-skript i Azure Synapse Studio (för hands version)
description: Introduktion till Azure Synapse Studio (för hands version) SQL-skript
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431077"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Använda SQL-skript i Azure Synapse Studio (för hands version)

Azure Synapse Studio (för hands version) innehåller ett webb gränssnitt för SQL-skript där du kan redigera SQL-frågor. Du kan ansluta till SQL-poolen (förhands granskning) eller SQL på begäran (för hands version). 

## <a name="begin-authoring-in-sql-script"></a>Börja redigera i SQL-skript 

Det finns flera sätt att starta redigerings funktionen i SQL-skript. Du kan skapa ett nytt SQL-skript via någon av följande metoder.

1. Välj ikonen "+" och välj SQL-skript.

    > [!div class="mx-imgBorder"] 
    >![newsqlscript](./media/author-sql-script/newsqlscript.png)

2. Från menyn åtgärder under utveckla SQL-skript väljer du "nytt SQL-skript" på menyn "åtgärder" under utveckla SQL-skript. 

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript2actions.png)

eller 

3. Välj Importera från menyn åtgärder under utveckla SQL-skript och välj ett befintligt SQL-skript från din lokala lagrings plats.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Skapa SQL-skript

1. Välj ett namn för ditt SQL-skript genom att välja knappen "egenskap" och ersätta standard namnet som tilldelats SQL-skriptet.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. Välj den angivna SQL-poolen eller SQL på begäran på den nedrullningsbara menyn Anslut till. Om det behövs väljer du databasen från Använd databas.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. Börja redigera SQL-skript med hjälp av IntelliSense-funktionen.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Kör SQL-skriptet

Klicka på knappen Kör för att köra SQL-skriptet. Resultaten visas som standard i en tabell.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Exportera resultaten

Du kan exportera resultaten till din lokala lagring i olika format (inklusive CSV, Excel, JSON, XML) genom att välja "Exportera resultat" och välja tillägget.

Du kan också visualisera SQL-skript resultatet i ett diagram genom att välja knappen diagram. Välj "diagram typ" och "kategori kolumn". Du kan exportera diagrammet till en bild genom att välja "Spara som bild". 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Utforska data från en Parquet-fil.

Du kan utforska Parquet-filer i ett lagrings konto med hjälp av SQL-skript för att förhandsgranska fil innehållet. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL-tabeller, externa tabeller, vyer

Genom att välja menyn åtgärder under data kan du välja flera åtgärder som: "nytt SQL-skript", "Välj de översta 1000 raderna", "skapa", "släpp och skapa". Utforska den tillgängliga gesten genom att högerklicka på noderna i SQL-poolen och SQL på begäran.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du redigerar ett SQL-skript finns i [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).