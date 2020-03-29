---
title: Använda inmatning med ett klick för att få tillgång till data i Azure Data Explorer
description: Lär dig mer om hur du intänder (läser in) data i Azure Data Explorer genom att bara använda inmatning med ett klick.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 4a53f7e68501ce7f9b19dea0822d3896ec241fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444559"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Använda inmatning med ett klick för att få tillgång till data i Azure Data Explorer

I den här artikeln visas hur du använder inmatning med ett klick för snabbt inmatning av en ny tabell i JSON- eller CSV-format. Data kan intas från lagring eller en lokal fil till en befintlig tabell eller en ny tabell. Använd den intuitiva enklicksguiden och dina data som intövs inom några minuter. Sedan kan du redigera tabellen och köra frågor med hjälp av webbgränssnittet i Azure Data Explorer.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Logga in [på programmet](https://dataexplorer.azure.com/).
* Skapa [ett Azure Data Explorer-kluster och -databas](create-cluster-database-portal.md).
* Logga in i [webbgränssnittet](https://dataexplorer.azure.com/) och [lägg till en anslutning till klustret](/azure/data-explorer/web-query-data#add-clusters).

## <a name="ingest-new-data"></a>Övriga nya data

1. Högerklicka på *databasen* eller *tabellraden* i den vänstra menyn i webbgränssnittet och välj **Ingest nya data (Förhandsgranska).**

    ![Markera inmatning med ett klick i webbgränssnittet](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. I fönstret **Ingest nya data (Förhandsgranska)** väljer du fliken **Källa** och slutför **projektinformationen:**

    * För **Tabell**väljer du ett befintligt tabellnamn på den nedrullningsbara menyn eller väljer **Skapa ny** om du vill skapa en ny tabell.
    * För **inmatningstyp**väljer du antingen **från lagring** eller från **fil**.
      * Om du har valt **från lagring**väljer du Länka **till lagring** för att lägga till URL:en. Använd [Blob SAS-URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) för privata lagringskonton. 
      * Om du valde **från fil**väljer du **Bläddra** och drar filen till rutan.
    * Välj **Redigera schema** om du vill visa och redigera konfigurationen av tabellkolumnen.
 
    ![Information om inmatningskälla med ett klick](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Om du väljer **Ingest nya data (Förhandsgranska)** på en *tabellrad* visas det valda tabellnamnet i **projektinformationen**.

1. Om du har markerat en befintlig tabell öppnas fönstret **Mappa kolumner** för att mappa källdatakolumner till tabellkolumner. 
    * Använd **Utelämna kolumn** för att ta bort en målkolumn från tabellen.
    * Använd **Ny kolumn** för att lägga till en ny kolumn i tabellen.

    ![Fönstret Kartkolumner](media/ingest-data-one-click/one-click-map-columns-window.png)

1. På fliken **Schema:**

    * Välj **Komprimeringstyp** på den nedrullningsbara menyn och välj sedan **antingen Okomprimerad** eller **GZip**.
    * Välj **Dataformat** på den nedrullningsbara menyn och välj sedan **JSON**, **CSV,** **TSV,** **SCSV,** **SOHSV,** **TSVE**eller **PSV**. 
        * När du väljer **JSON-format** måste du också välja **JSON-nivåer**, från 1 till 10. Nivåerna påverkar tabellkolumndataavbildningen. 
        * Om du markerar ett annat format än JSON måste du markera kryssrutan **Inkludera kolumnnamn** om du vill ignorera filens rubrikrad.
    * **Mappningsnamnet** ställs in automatiskt men kan redigeras.
    * Om du har markerat en befintlig tabell kan du välja **Mappa kolumner** för att öppna fönstret **Kartkolumner.**

    ![Schema för inmatning av CSV-format med ett klick](media/ingest-data-one-click/one-click-csv-format.png)

1. Öppna **redigeraren** genom att välja **v-knappen** ovanför redigeringsfönstret. I redigeraren kan du visa och kopiera de automatiska frågor som genereras från dina indata. 

1. I tabellen: 
    * Högerklicka på nya kolumnrubriker om du vill **ändra datatyp,** **Byta namn på kolumn**, Ta bort **kolumn**, **Sortera stigande**eller **Sortera fallande**. I befintliga kolumner är endast datasortering tillgänglig. 
    * Dubbelklicka på det nya kolumnnamnet som ska redigeras.

1. Välj **Starta inmatning** om du vill skapa en tabell och mappning och påbörja datainmatning.

    ![Schema för inmatning av JSON-format med ett klick](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Söka i data

1. I fönstret **Datainmatning slutförs** alla tre stegen med gröna bockar om datainmatningen har slutförts.
 
    ![Datainmatning med ett klick har slutförts](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Välj **v-knappen** för att öppna frågan. Kopiera till webbgränssnittet för att redigera frågan.

1. Menyn till höger innehåller **snabbfrågor** och **verktygsalternativ.** 

    * **Snabbfrågor** innehåller länkar till webbgränssnittet med exempelfrågor.
    * **Verktygen** innehåller en länk till **släppkommandon** i webbgränssnittet, där du `.drop` kan felsöka problem genom att köra relevanta kommandon.

    > [!TIP]
    > Du kan förlora data `.drop` när du använder kommandon. Använd dem försiktigt.

## <a name="next-steps"></a>Nästa steg

* [Frågedata i webbgränssnittet i Azure Data Explorer](web-query-data.md)
* [Skriva frågor för Azure Data Explorer med Kusto Query Language](write-queries.md)
