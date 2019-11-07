---
title: Använd ett-Klicka för att mata in data i Azure Datautforskaren
description: Lär dig mer om hur du matar in data i Azure Datautforskaren bara genom att bara använda ett klick.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 98598a28e14dfd8175cbb019ff1b001c65503580
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73644597"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Använd ett-Klicka för att mata in data i Azure Datautforskaren

I den här artikeln visas hur du använder ett enda klick för att snabbt mata in en ny tabell i JSON-eller CSV-format från lagring till Azure Datautforskaren. När data har matats in kan du redigera tabellen och köra frågor med hjälp av webb gränssnittet.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Logga in på [programmet](https://dataexplorer.azure.com/).
* Skapa [ett Azure datautforskaren-kluster och-databas](create-cluster-database-portal.md)
* Logga in på [webb gränssnittet](https://dataexplorer.azure.com/) och [Lägg till en anslutning till klustret](/azure/data-explorer/web-query-data#add-clusters)
* Data källa i Azure Storage.

## <a name="ingest-new-data"></a>Mata in nya data

1. Högerklicka på *databas* -eller *tabell* raden i den vänstra menyn i webb gränssnittet och välj Mata in **ny data (förhands granskning)**

    ![Välj en klicknings inmatning i webb gränssnittet](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. I fönstret för att mata in **nya data (förhands granskning)** går du till fliken **källa** och slutför **projekt informationen**:

    * **Tabell**: Välj befintligt tabell namn från listruta eller Välj **Skapa nytt** för att skapa en ny tabell.
    * Välj inmatnings **typ** > **från lagring** eller **från fil**.
        * Om du valde **från lagring**anger du **länk till lagring** för att lägga till URL: en i lagrings utrymmet. Använd [blobb-SAS-webbadress](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) för privata lagrings konton. 
        * Om du har valt **från fil**väljer du **Bläddra** och drar filen till rutan.
    * Välj **Redigera schema** för att visa och redigera din tabell kolumn konfiguration.
 
    ![en klicknings käll information](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Om du väljer Hämta **nya data (för hands version)** på en *tabell* rad visas det valda tabell namnet i **projekt informationen**.

1. Om du har valt en befintlig tabell öppnas fönstret **kart kolumner** för att mappa käll data kolumner till mål tabell kolumner. 
    * Använd **utelämna kolumn** för att ta bort en mål kolumn från tabellen. 
    * Använd **ny kolumn** för att lägga till en ny kolumn i tabellen. 

    ![Fönstret kart kolumner](media/ingest-data-one-click/one-click-map-columns-window.png)

1. På fliken **schema** :

    * Välj **komprimerings typ** från listruta > **okomprimerad** eller **gzip**.
    * Välj **data format** i list rutan > **JSON**, **CSV**, **TSV**, **SCSV**, **SOHSV**, **TSVE**eller **PSV**. 
        * När du väljer **JSON** -format väljer du **JSON-nivåer**: 1-10. Nivåerna påverkar tabell kolumnens data-skildringar. 
        * Om du väljer ett annat format än JSON: Markera kryss rutan **Inkludera kolumn namn** om du vill ignorera filens rubrik rad.    
    * **Mappnings namnet** anges automatiskt men kan redige ras.
    * Om du har valt en befintlig tabell kan du välja knappen **kart kolumner** för att öppna fönstret **kart kolumner** .

    ![ett klick på att mata in CSV-format schema. png](media/ingest-data-one-click/one-click-csv-format.png)

1. I **redigeraren**väljer du **V** till höger för att öppna redigeraren. I redigeraren kan du Visa och kopiera automatiska frågor som genereras från dina indata. 

1.  I tabell: 
    * Högerklicka på nya kolumn rubriker för att **ändra datatyp**, **Byt namn på kolumn**, **ta bort kolumn**, **Sortera stigande**eller **Sortera fallande**. Endast data sortering är tillgänglig i befintliga kolumner. 
    * Dubbelklicka på det nya kolumn namnet för att redigera.

1. Välj **Starta** inmatning för att skapa tabell, skapa mappning och data inmatning.

    ![ett klicknings schema för JSON-format](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Frågedata

1. I fönstret **data inmatning har slutförts** markeras alla tre stegen med gröna markeringar, om data inmatning har slutförts. 
 
    ![ett klick för data inmatning har slutförts](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Välj **V** för att öppna frågan. Kopiera till webb gränssnittet för att redigera frågan.

1. Menyn till höger innehåller **snabb frågor** och **verktyg**. 

    * **Snabb frågor** innehåller länkar till webb gränssnitt med exempel frågor.
    * **Verktygen** innehåller länkar till webb gränssnitt med **släpp-kommandon** som gör att du kan felsöka problem genom att köra det relevanta `.drop` kommandot.

    > [!TIP]
    > Data kan gå förlorade med `.drop`-kommandon. Använd dem noggrant.

## <a name="next-steps"></a>Nästa steg

* [Fråga efter data i Azure Datautforskaren Web UI](web-query-data.md)
* [Skriv frågor för Azure Datautforskaren att använda frågespråket i Kusto](write-queries.md)
