---
title: SQL-skript i Azure Synapse Studio (förhandsversion)
description: Introduktion av Azure Synapse Studio (förhandsversion) SQL-skript
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431077"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Använda SQL-skript i Azure Synapse Studio (förhandsversion)

Azure Synapse Studio (förhandsversion) tillhandahåller ett SQL-skriptwebbgränssnitt där du kan skapa SQL-frågor. Du kan ansluta till SQL-pool (förhandsgranskning) eller SQL på begäran (förhandsgranskning). 

## <a name="begin-authoring-in-sql-script"></a>Börja skapa i SQL-skript 

Det finns flera sätt att starta redigeringsupplevelsen i SQL-skript. Du kan skapa ett nytt SQL-skript med någon av följande metoder.

1. Välj "+"-ikonen och välj SQL-skript.

    > [!div class="mx-imgBorder"] 
    >![nyhetsqlscript](./media/author-sql-script/newsqlscript.png)

2. På menyn Åtgärder under Utveckla SQL-skript Välj "Nytt SQL-skript" på menyn "Åtgärder" under Utveckla SQL-skript. 

    > [!div class="mx-imgBorder"] 
    > ![nyhetsqlscript](./media/author-sql-script/newsqlscript2actions.png)

eller 

3. Välj "Importera" på menyn "Åtgärder" under Utveckla SQL-skript och välj ett befintligt SQL-skript från din lokala lagring.

    > [!div class="mx-imgBorder"] 
    > ![nyhetsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Skapa ditt SQL-skript

1. Välj ett namn på SQL-skriptet genom att välja knappen "Egenskap" och ersätta standardnamnet som tilldelats SQL-skriptet.

    > [!div class="mx-imgBorder"] 
    > ![nyhetsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. Välj den specifika SQL-poolen eller SQL på begäran på den nedrullningsbar menyn Anslut till. Eller om det behövs, välj databasen från "Använd databas".

    > [!div class="mx-imgBorder"] 
    > ![nyhetsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. Börja skapa ditt SQL-skript med intellisense-funktionen.

    > [!div class="mx-imgBorder"] 
    > ![nyhetsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Kör sql-skriptet

Välj knappen "Kör" för att köra ditt SQL-skript. Resultaten visas som standard i en tabell.

> [!div class="mx-imgBorder"] 
> ![nyhetsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Exportera dina resultat

Du kan exportera resultaten till din lokala lagring i olika format (inklusive CSV, Excel, JSON, XML) genom att välja "Exportera resultat" och välja tillägget.

Du kan också visualisera SQL-skriptet resultat i ett diagram genom att välja "Diagram"-knappen. Markera kolumnen "Diagramtyp" och "Kategori". Du kan exportera diagrammet till en bild genom att välja "Spara som bild". 

> [!div class="mx-imgBorder"] 
> ![nyhetsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Utforska data från en parkettfil.

Du kan utforska Parquet-filer i ett lagringskonto med SQL-skript för att förhandsgranska filinnehållet. 

> [!div class="mx-imgBorder"] 
> ![nyhetsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL-tabeller, externa tabeller, vyer

Genom att välja menyn "Åtgärder" under data kan du välja flera åtgärder som: "Nytt SQL-skript", "Välj TOPP 1000 rader", "SKAPA", "DROP och CREATE". Utforska den tillgängliga gesten genom att högerklicka på noderna i SQL-poolen och SQL på begäran.

> [!div class="mx-imgBorder"] 
> ![nyhetsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du skapar ett SQL-skript finns i [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).