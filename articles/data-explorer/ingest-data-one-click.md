---
title: Använda inmatning med ett klick för att få tillgång till data i Azure Data Explorer
description: Översikt över inmatning (inläsning) data i Azure Data Explorer helt enkelt, med hjälp av ett klick inmatning.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 5bde63427ce76f14832551864bbf2c3d8e015fd6
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521595"
---
# <a name="what-is-one-click-ingestion"></a>Vad är intag med ett klick? 

Med ett klickinmatning kan du snabbt använda data och automatiskt föreslå tabeller och mappningsstrukturer, baserat på en datakälla i Azure Data Explorer. 

Med hjälp av webbgränssnittet i Azure Data Explorer kan du använda data från lagring (blob-fil), en lokal fil eller en behållare (upp till 10 000 blobbar). Du kan också definiera ett händelserutnät på en behållare för kontinuerligt inmatning. Data kan intas i en befintlig eller ny tabell i JSON, CSV och [andra format](#file-formats). Ett klickinmatning kan föreslå en struktur för en ny tabell- och tabellmappning, baserat på datakällan, och tillhandahålla en intuitiv plattform för att justera tabellstrukturen för en befintlig tabell- och tabellmappning. Ett klickinmatning kommer att inta data i tabellen inom bara några minuter.

Inmatning med ett klick är särskilt användbart när du inbetar data för första gången, eller när datas schema inte är bekant för dig.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Skapa [ett Azure Data Explorer-kluster och -databas](create-cluster-database-portal.md).
* Logga in i [webbgränssnittet](https://dataexplorer.azure.com/) i Azure Data Explorer och [lägga till en anslutning till klustret](/azure/data-explorer/web-query-data#add-clusters).

## <a name="file-formats"></a>Filformat

Inmatning med ett klick stöder inmatning av en ny tabell från källdata i något av följande format:
* JSON
* CSV
* Tsv
* SCSV (AVSV)
* SOHSV (PÅ)
* TSVE (av )
* Psv

## <a name="one-click-ingestion-wizard"></a>Guiden Inmatning med ett klick

Guiden För inmatning med ett klick guidar dig genom inmatningsprocessen med ett klick. 

> [!Note]
> I det här avsnittet beskrivs guiden i allmänhet. Vilka alternativ du väljer beror på om du inleder en ny eller befintlig tabell. Mer information finns i:
    > * Inta i [en ny tabell](one-click-ingestion-new-table.md)
    > * Inta i en [befintlig tabell](one-click-ingestion-existing-table.md) 
    
1. Om du vill komma åt guiden högerklickar du på *databasen* eller *tabellraden* i menyn i den vänstra menyn i webbgränssnittet i Azure Data Explorer och väljer **Ingest nya data (förhandsversion).**

    ![Markera inmatning med ett klick i webbgränssnittet](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   

1. Guiden guidar dig genom följande alternativ:
       * Inta i en [befintlig tabell](one-click-ingestion-existing-table.md)
       * Inta i [en ny tabell](one-click-ingestion-new-table.md)
       * Inta data från: * Blob storage * En lokal fil * En behållare
       * Ange exempelstorleken, från 1 till 10 000 rader (endast från behållare)
       
1. När du har valt datakällan visas en förhandsgranskning av data. 
    Om du intar data från en behållare kan du filtrera data så att endast filer med specifika prefix eller filnamnstillägg förtärs. Du kanske till exempel bara vill använda filer med filnamn som börjar med ordet *Europa*, eller bara filer med tillägget *.json*. 

1. Klicka på **Redigera schema**. Om du inbeter data i en viss tabell kan du mappa källkolumnerna till målkolumnerna och bestämma om kolumnnamn ska inkluderas eller inte.

1. Starta datainmatningsprocessen.

> [!Note]
> Om datakällan är en behållare bör du tänka på att Azure Data Explorers princip för aggregering av datainmatning (batching) är utformad för att optimera inmatningsprocessen. Som standard är principen konfigurerad till 5 minuter eller 500 MB data, så du kan uppleva svarstid. Se [batchprincip](/azure/kusto/concepts/batchingpolicy) för aggregeringsalternativ. Vid intag av data från andra källor kommer intag att träda omedelbart.

## <a name="next-steps"></a>Nästa steg

* Bestäm om du ska använda inmatning med ett klick för att förtära data i [en befintlig tabell](one-click-ingestion-existing-table.md) eller en ny [tabell](one-click-ingestion-new-table.md)
* [Frågedata i webbgränssnittet i Azure Data Explorer](/azure/data-explorer/web-query-data)
* [Skriva frågor för Azure Data Explorer med Kusto Query Language](/azure/data-explorer/write-queries)