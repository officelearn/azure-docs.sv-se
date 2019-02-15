---
title: Azure Data Factory mappning Dataomvandling Flow mottagare
description: Azure Data Factory mappning Dataomvandling Flow mottagare
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 795b8072bbd9b248f982d061d699f490b1b63b17
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56272117"
---
# <a name="azure-data-factory-mapping-data-flow-sink-transformation"></a>Azure Data Factory mappning Dataomvandling Flow mottagare

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Alternativ för mottagare](media/data-flow/windows1.png "mottagare 1")

När ditt flöde transformering av data, kan du mottagare omvandlade data till en mål-datauppsättning. Du kan välja definitionen för datauppsättningen som du vill använda för mål-utdata för omvandling mottagare.

En vanlig metod att kompensera för att ändra inkommande data och att kompensera för drift av schemat är att mottagare utdata till en mapp utan ett definierat schema i datauppsättningen för utdata. Du kan också ta hänsyn till alla ändringar i kolumnen i dina källor genom att välja ”Tillåt schemat Drift” på källan och sedan automap alla fält i mottagaren.

Du kan välja att skriva över, lägga till eller misslyckas dataflödet när sänks till en datauppsättning.

Du kan också välja ”automap” till alla inkommande fält för mottagare. Om du vill välja vilka fält som du vill mottagare till målet, eller om du vill ändra namnen på fälten vid målet, Välj ”Off” för ”automap” och klicka sedan på fliken mappning för att mappa fält för tabellutdata:

![Alternativ för mottagare](media/data-flow/sink2.png "mottagare 2")

## <a name="output-to-one-file"></a>Utdata till en fil
För Azure Storage Blob eller Data Lake typer av mottagare kommer du sparar omvandlade data till en mapp. Spark genererar partitionerade data utdatafilerna baserat på schemat som används i mottagare transformeringen. Du kan ange partitioneringsschemat genom att klicka på fliken ”Optimize”. Om du vill ha ADF om du vill slå samman dina utdata till en enda fil, klicka på knappen ”enskild Partition”.

![Alternativ för mottagare](media/data-flow/opt001.png "alternativ för mottagare")

## <a name="blob-storage-folder"></a>BLOB Storage-mapp
När sinka din dataomvandlingar till Blob Store, väljer du en blob *mappen* som din sökväg till målmappen, inte en fil. Dataflöde för ADF genererar utdatafilerna åt dig i den mappen.

![Mappsökväg](media/data-flow/folderpath.png "mappsökvägen")

## <a name="optional-azure-sql-data-warehouse-sink"></a>Valfria Azure SQL Data Warehouse-mottagaren

Vi publicerar en tidig betaversionen av datauppsättning för ADW mottagare för dataflöde. Detta kan du alltid landar omvandlade data direkt i Azure SQL DW inom dataflöde utan att du behöver för att lägga till en Kopieringsaktivitet i din pipeline.

Börja med att skapa en ADW-datauppsättning precis som för alla andra ADF pipeline, med en länkad tjänst som innehåller dina ADW-autentiseringsuppgifter och välj den databas som du vill ansluta till. Välj en befintlig tabell eller Skriv namnet på tabellen som du vill dataflöde för att skapa automatiskt åt dig från i fälten inkommande i tabellens namn.

![Alternativ för mottagare](media/data-flow/adw3.png "mottagare 3")

Gå tillbaka till mottagare tranformation (ADW är för närvarande stöds endast som en mottagare) väljer du den ADW-datauppsättning som du skapat samt de Storage-konto som du vill använda för mellanlagring data för Polybase läser in i ADW. Sökvägsfältet har följande format: ”containername/mappnamn”.

![Alternativ för mottagare](media/data-flow/adw1.png "mottagare 4")

### <a name="save-policy"></a>Spara princip

Skriv över trunkera tabellen om den finns, sedan återskapa den och läsa in data. Lägg till att infoga nya rader. Om tabellen från tabellnamnet datauppsättningen inte finns alls i mål-ADW, ska dataflöde skapa tabellen och sedan läsa in data.

Om du avmarkerar ”Automatisk karta” kan mappa du fälten till din måltabell manuellt.

![Mottagare ADW alternativ](media/data-flow/adw2.png "adw mottagare")

### <a name="max-concurrent-connections"></a>Maxantal samtidiga anslutningar

Du kan ange maximalt antal samtidiga anslutningar omvandling mottagare när du skriver data till en Azure database-anslutning.

![Anslutningsalternativ](media/data-flow/maxcon.png "anslutningar")
