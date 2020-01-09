---
title: Använd ett-Klicka för att mata in data i Azure Datautforskaren
description: Lär dig mer om hur du matar in data i Azure Datautforskaren bara genom att bara använda ett klick.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 4a53f7e68501ce7f9b19dea0822d3896ec241fb8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444559"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Använd ett-Klicka för att mata in data i Azure Datautforskaren

I den här artikeln visar vi hur du använder ett enda klick för att snabbt mata in en ny tabell i JSON-eller CSV-format. Data kan matas in från lagring eller en lokal fil till en befintlig tabell eller en ny tabell. Använd den intuitiva guiden för enkel klickning och dina data inmatning inom några minuter. Sedan kan du redigera tabellen och köra frågor med hjälp av Azure Datautforskaren Web UI.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Logga in på [programmet](https://dataexplorer.azure.com/).
* Skapa [ett Azure datautforskaren-kluster och-databas](create-cluster-database-portal.md).
* Logga in på [webb gränssnittet](https://dataexplorer.azure.com/) och [Lägg till en anslutning till klustret](/azure/data-explorer/web-query-data#add-clusters).

## <a name="ingest-new-data"></a>Mata in nya data

1. Högerklicka på *databasen* eller *tabell* raden i den vänstra menyn i webb gränssnittet och välj Mata in **nya data (för hands version)** .

    ![Välj en inmatning i webb gränssnittet för ett klick](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. I fönstret mata in **nya data (för hands version)** väljer du fliken **källa** och slutför **projekt informationen**:

    * För **tabell**väljer du ett befintligt tabell namn från den nedrullningsbara menyn eller väljer **Skapa nytt** för att skapa en ny tabell.
    * För inmatnings **typ**väljer du antingen **från lagring** eller **från fil**.
      * Om du valde **från lagring**väljer du **länk till lagring** för att lägga till URL: en. Använd [blobb-SAS-webbadress](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) för privata lagrings konton. 
      * Om du har valt **från fil**väljer du **Bläddra** och drar filen till rutan.
    * Välj **Redigera schema** för att visa och redigera din tabell kolumn konfiguration.
 
    ![Käll information om ett klick](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > Om du väljer Hämta **nya data (för hands version)** på en *tabell* rad visas det valda tabell namnet i **projekt informationen**.

1. Om du har valt en befintlig tabell öppnas fönstret **kart kolumner** för att mappa käll data kolumner till mål tabell kolumner. 
    * Använd **utelämna kolumn** för att ta bort en mål kolumn från tabellen.
    * Använd **ny kolumn** för att lägga till en ny kolumn i tabellen.

    ![Fönstret kart kolumner](media/ingest-data-one-click/one-click-map-columns-window.png)

1. På fliken **schema** :

    * Välj **komprimerings typ** på den nedrullningsbara menyn och välj sedan antingen **okomprimerad** eller **gzip**.
    * Välj **data format** på den nedrullningsbara menyn och välj sedan **JSON**, **CSV**, **TSV**, **SCSV**, **SOHSV**, **TSVE**eller **PSV**. 
        * När du väljer **JSON** -formatet måste du också välja **JSON-nivåer**, från 1 till 10. Nivåerna påverkar tabell kolumnens data-skildringar. 
        * Om du väljer ett annat format än JSON måste du markera kryss rutan **Inkludera kolumn namn** om du vill ignorera rubrik raden i filen.
    * **Mappnings namnet** anges automatiskt men kan redige ras.
    * Om du har valt en befintlig tabell kan du välja **kart kolumner** för att öppna fönstret **kart kolumner** .

    ![Schema för CSV-format med ett klick](media/ingest-data-one-click/one-click-csv-format.png)

1. Välj **v** - **knappen i redigerings fönstret för** att öppna redigeraren. I redigeraren kan du Visa och kopiera automatiska frågor som genereras från dina indata. 

1. I tabellen: 
    * Högerklicka på nya kolumn rubriker om du vill **ändra datatyp**, **Byt namn på kolumn**, **ta bort kolumn**, **Sortera stigande**eller **Sortera fallande**. I befintliga kolumner är endast data sortering tillgänglig. 
    * Dubbelklicka på det nya kolumn namnet för att redigera.

1. Välj **börja** mata in för att skapa en tabell och mappning och för att börja använda data inmatning.

    ![Inmatnings-JSON-format med ett klick](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Söka i data

1. I fönstret **data inmatning har** alla tre steg marker ATS med gröna kryss markeringar om data inmatningen har slutförts.
 
    ![Data inmatning med ett klick slutförd](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Välj **v** -knappen för att öppna frågan. Kopiera till webb gränssnittet för att redigera frågan.

1. Menyn till höger innehåller **snabba frågor** och **verktyg** alternativ. 

    * **Snabb frågor** innehåller länkar till webb gränssnittet med exempel frågor.
    * **Verktygen** innehåller en länk för att **släppa kommandon** i webb gränssnittet, vilket gör att du kan felsöka problem genom att köra relevanta `.drop`-kommandon.

    > [!TIP]
    > Du kan förlora data när du använder `.drop`-kommandon. Använd dem noggrant.

## <a name="next-steps"></a>Nästa steg

* [Fråga efter data i Azure Datautforskaren Web UI](web-query-data.md)
* [Skriv frågor för Azure Datautforskaren att använda frågespråket i Kusto](write-queries.md)
