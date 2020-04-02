---
title: Använda inmatning med ett klick för att använda data i en befintlig Azure Data Explorer-tabell
description: Intag av (inläsning) data i en befintlig Azure Data Explorer-tabell helt enkelt med hjälp av inmatning med ett klick.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: a7fdab66394c132bcd9134669b841d178c559f28
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546213"
---
# <a name="use-one-click-ingestion-to-ingest-data-to-an-existing-table-in-azure-data-explorer"></a>Använda inmatning med ett klick för att använda data till en befintlig tabell i Azure Data Explorer

Med inmatning med ett klick kan du snabbt få in data i JSON, CSV och andra format i en tabell. Med hjälp av webbgränssnittet i Azure Data Explorer kan du använda data från lagring, från en lokal fil eller från en behållare. 

I det här dokumentet beskrivs hur du använder den intuitiva enklicksguiden för att använda JSON-data från en fil till en befintlig tabell. Du kan sedan redigera tabellen och köra frågor med webbgränssnittet i Azure Data Explorer.

Inmatning med ett klick är särskilt användbart när du inbetar data för första gången, eller när datas schema inte är bekant för dig. 

En översikt över inmatning med ett klick och en lista över förutsättningar finns [i Inmatning](ingest-data-one-click.md)med ett klick .
Information om hur du inbester data i en ny tabell i Azure Data Explorer finns [i Inmatning](one-click-ingestion-new-table.md) med ett klick till en ny tabell

## <a name="ingest-new-data"></a>Övriga nya data

1. Högerklicka på en *databas* eller *tabell* på den vänstra menyn i webbgränssnittet och välj **Ingest nya data (Förhandsgranska).**

    ![Markera inmatning med ett klick i webbgränssnittet](media/one-click-ingestion-existing-table/one-click-ingestion-in-webui.png)   
 
1. I fönstret **Ingest nya data (Förhandsgranska)** markeras fliken **Källa** automatiskt.

1. Om **tabellfältet** inte fylls automatiskt väljer du ett befintligt tabellnamn på den nedrullningsbara menyn.
    > [!TIP]
    > Om du väljer **Ingest nya data (Förhandsgranska)** på en *tabellrad* visas det valda tabellnamnet i **projektinformationen**.

[!INCLUDE [data-explorer-one-click-ingestion-types](../../includes/data-explorer-one-click-ingestion-types.md)]
    
Välj **Redigera schema** om du vill visa och redigera konfigurationen av tabellkolumnen.

## <a name="edit-the-schema"></a>Redigera schemat

1. Dialogrutan **Mappa kolumner** öppnas och du kan mappa källdatakolumner till måltabellkolumner. 
    * I fälten **Källkolumner** anger du kolumnnamn som du vill mappa med **målkolumnerna**.
    * Om du vill ta bort en mappning markerar du papperskorgen.

    ![Fönstret Kartkolumner](media/one-click-ingestion-existing-table/map-columns.png)

1. Välj **Uppdatera**.
1. På fliken **Schema:**
    1. Välj **Komprimeringstyp**och välj sedan **antingen Okomprimerad** eller **GZip**.

        [!INCLUDE [data-explorer-one-click-ingestion-edit-schema](../../includes/data-explorer-one-click-ingestion-edit-schema.md)]
        
    1. Om du väljer **JSON**måste du också välja **JSON-nivåer**, från 1 till 10. Nivåerna påverkar tabellkolumndataavbildningen.

    ![Välj JSON-nivåer](media/one-click-ingestion-existing-table/json-levels.png)

    * Om du markerar ett annat format än JSON kan du markera kryssrutan **Inkludera kolumnnamn** om du vill ignorera filens rubrikrad.
        
    ![Välj Inkludera kolumnnamn](media/one-click-ingestion-existing-table/non-json-format.png)

    > [!Note]
    > Tabellformat kan endast ange kolumndata i en kolumn i en Azure Data Explorer-tabell. 

    * Nya mappningar ställs in automatiskt, men du kan ändra den så att den använder en befintlig. 
    * Du kan välja **Mappa kolumner** för att öppna fönstret **Kartkolumner.**

## <a name="copy-and-paste-queries"></a>Kopiera och klistra in frågor

1. Öppna **redigeraren** genom att välja **v-knappen** ovanför redigeringsfönstret. I redigeraren kan du visa och kopiera de automatiska kommandon som genereras från dina indata. 
1. I tabellen: 
    * Markera nya kolumnrubriker om du vill lägga till en **ny kolumn**, Ta **bort kolumn**, Sortera **stigande**eller **Sortera fallande**. I befintliga kolumner är endast datasortering tillgänglig.

    > [!Note]
    > * Du kan inte uppdatera namn och datatyp för befintliga tabeller.
    > * Släpp-kommandon återställer bara de ändringar som gjordes av hans inmatningsflöde (nya omfattningar och kolumner). Inget annat kommer att släppas.

[![](media/one-click-ingestion-existing-table/edit-view.png "One-click ingestion edit view")](media/one-click-ingestion-existing-table/edit-view.png#lightbox) 

## <a name="start-ingestion"></a>Starta inmatning

Välj **Starta inmatning** om du vill skapa en tabell och mappning och påbörja datainmatning.

![Starta inmatning](media/one-click-ingestion-existing-table/start-ingestion.png)

## <a name="data-ingestion-completed"></a>Datainmatning har slutförts

I fönstret **Datainmatning slutförs** alla tre stegen med gröna bockar om datainmatningen har slutförts.
 
![Datainmatning med ett klick har slutförts](media/one-click-ingestion-existing-table/one-click-data-ingestion-complete.png)

[!INCLUDE [data-explorer-one-click-ingestion-query-data](../../includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="next-steps"></a>Nästa steg

* [Frågedata i webbgränssnittet i Azure Data Explorer](/azure/data-explorer/web-query-data)
* [Skriva frågor för Azure Data Explorer med Kusto Query Language](/azure/data-explorer/write-queries)
