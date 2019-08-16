---
title: Använd ett-Klicka för att mata in data i Azure Datautforskaren
description: Lär dig mer om hur du matar in data i Azure Datautforskaren bara genom att bara använda ett klick.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: b1ce2d9efe44021b4e3191739bd2f922e34c44cb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520054"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Använd ett-Klicka för att mata in data i Azure Datautforskaren

I den här artikeln visas hur du använder ett enda klick för att snabbt mata in en ny tabell i JSON-eller CSV-format från lagring till Azure Datautforskaren. När data har matats in kan du redigera tabellen och köra frågor med hjälp av webb gränssnittet.

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Logga in på [programmet](https://dataexplorer.azure.com/).
* Skapa [ett Azure datautforskaren-kluster och-databas](create-cluster-database-portal.md)
* Data källa i Azure Storage.

## <a name="ingest-new-data"></a>Mata in nya data

1. Högerklicka på *databas namnet* och välj Mata in **ny data (förhands granskning)**

    ![Välj en klicknings inmatning i webb gränssnittet](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. I fönstret för **data inmatning (förhands granskning)** går du till fliken **källa** och slutför **projekt informationen**:

    * Ange **ett nytt tabell namn**. 
    * Välj inmatnings **typ** > **från lagring**.
    * Ange **länk till lagring** Lägg till URL i lagrings utrymmet. Använd blobb-SAS-webbadress för privata lagrings konton. 
    * Välj **Redigera schema**
 
    ![en klicknings käll information](media/ingest-data-one-click/one-click-ingestion-source.png) 

1. På fliken **schema** väljer du **data format** från List rutan > **JSON** eller **CSV**. 
   
   Om väljer du **CSV**:
    * Markera kryss rutan **Ignorera rubrik** för att ignorera rubrik raden i CSV-filen.    
    * **Mappnings namnet** anges automatiskt men kan redige ras.

    ![ett klick på att mata in CSV-format schema. png](media/ingest-data-one-click/one-click-csv-format.png)

   Om Välj **JSON**:
    * Välj **JSON-nivåer**: 1-10 från listruta. Nivåerna i JSON-filen visas i tabellen längst ned till höger. 
    * **Mappnings namnet** anges automatiskt men kan redige ras.

    ![ett klicknings schema för JSON-format](media/ingest-data-one-click/one-click-json-format.png)  

1. I **redigeraren**väljer du **V** till höger för att öppna redigeraren. I redigeraren kan du Visa och kopiera automatiska frågor som genereras från dina indata. 

1.  I tabellen längst ned till höger: 
    * Välj **V** till höger om kolumnen för att **byta namn på kolumn**, **ta bort kolumn**, **Sortera stigande**eller **Sortera fallande**
    * Dubbelklicka på kolumn namn för att redigera.
    * Välj ikonen till vänster om kolumn namnet för att ändra data typen. 

1. Välj **Starta** inmatning för att skapa tabell, skapa mappning och data inmatning.
 
## <a name="query-data"></a>Söka i data

1. I fönstret **data inmatning har slutförts** markeras alla tre stegen med gröna markeringar, om data inmatning har slutförts. 
 
    ![ett klick för data inmatning har slutförts](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Välj **V** för att öppna frågan. Kopiera till webb gränssnittet för att redigera frågan.

1. Menyn till höger innehåller **snabb frågor** och **verktyg**. 

    * **Snabb frågor** innehåller länkar till webb gränssnitt med exempel frågor.
    * **Verktygen** innehåller länkar till webb gränssnitt med **släpp-kommandon** som gör att du kan felsöka problem genom att `.drop` köra det relevanta kommandot.

    > [!TIP]
    > Data kan gå förlorade med `.drop` kommandon. Använd dem noggrant.

## <a name="next-steps"></a>Nästa steg

* [Fråga efter data i Azure Datautforskaren Web UI](web-query-data.md)
* [Skriv frågor för Azure Datautforskaren att använda frågespråket i Kusto](write-queries.md)
