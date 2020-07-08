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
ms.openlocfilehash: ee384d6095ccbf25225a435fe8afe4281c5d62df
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921494"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Använda SQL-skript i Azure Synapse Studio (för hands version)

Azure Synapse Studio (för hands version) innehåller ett webb gränssnitt för SQL-skript där du kan redigera SQL-frågor. Du kan ansluta till SQL-poolen (förhands granskning) eller SQL på begäran (för hands version). 

## <a name="begin-authoring-in-sql-script"></a>Börja redigera i SQL-skript 

Det finns flera sätt att starta redigerings funktionen i SQL-skript. Du kan skapa ett nytt SQL-skript via någon av följande metoder.

1. Från menyn utveckla väljer du ikonen **"+"** och sedan SQL- **skript**.

    ![newsqlscript](media/author-sql-script/newsqlscript.png)

2. Från menyn **åtgärder** väljer du **nytt SQL-skript**.
    
    ![newsqlscript2actions](media/author-sql-script/newsqlscript2actions.png)

Du kan också : 

3. Välj **Importera** från menyn **åtgärder** under utveckla SQL-skript och välj ett befintligt SQL-skript från din lokala lagrings plats.
 
    ![newsqlscript3actions](media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Skapa SQL-skript

1. Välj ett namn för ditt SQL-skript genom att välja **egenskaps** knappen och ersätta standard namnet som tilldelats SQL-skriptet.
  
    ![newsqlscriptrename](media/author-sql-script/newsqlscriptrename.png)

2. Välj den aktuella SQL-poolen eller SQL på begäran på den nedrullningsbara menyn **Anslut till** . Eller om det behövs väljer du databasen från **Använd databas**.
 
    ![newsqlchoosepool](media/author-sql-script/newsqlchoosepool.png)

3. Börja redigera SQL-skript med hjälp av IntelliSense-funktionen.

    ![newsqlintellisense](media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Kör SQL-skriptet

Klicka på knappen **Kör** för att köra SQL-skriptet. Resultaten visas som standard i en tabell.

![newsqlscriptresultstable](media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Exportera resultaten

Du kan exportera resultaten till din lokala lagring i olika format (inklusive CSV, Excel, JSON, XML) genom att välja "Exportera resultat" och välja tillägget.

Du kan också visualisera SQL-skript resultatet i ett diagram genom att välja **diagram** knappen. Välj kolumnen diagram typ och **kategori**. Du kan exportera diagrammet till en bild genom att välja **Spara som bild**. 

![newsqlscriptresultschart](media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Utforska data från en Parquet-fil

Du kan utforska Parquet-filer i ett lagrings konto med hjälp av SQL-skript för att förhandsgranska fil innehållet.

![newscriptsqlodparquet](media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL-tabeller, externa tabeller, vyer

Genom att välja menyn **åtgärder** under data kan du välja flera åtgärder, till exempel:

- Nytt SQL-skript
- Markera de 1000 översta raderna
- FRAM
- SLÄPP och skapa 
 
Utforska den tillgängliga gesten genom att högerklicka på noderna i SQL-poolen och SQL på begäran.
 
![newscriptdatabase](media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du redigerar ett SQL-skript finns i [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).
