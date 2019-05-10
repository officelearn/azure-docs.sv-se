---
title: Kopiera data stegvis med Azure Data Factory | Microsoft Docs
description: Dessa självstudier visar hur du inkrementellt kopierar data från ett källdatalager till ett måldatalager. Den första kopierar data från en tabell.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: 1d8b31e55a2a230385730c924d3e6bcc6072e7ea
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520444"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Läsa in data stegvis från ett källdatalager till ett måldatalager

I en dataintegrationslösning är stegvis inläsning av data (eller deltadata) efter den första fullständiga datainläsningen ett vanligt scenario. Självstudierna i det här avsnittet visar olika sätt att läsa in data inkrementellt med Azure Data Factory.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Deltadatainläsning från databas med vattenstämpel
I det här fallet definierar du en vattenstämpel i din källdatabas. En vattenstämpel är en kolumn som har den senast uppdaterade tidsstämpeln eller en stegvis ökande nyckel. Lösningen för deltainläsning läser in de ändrade data mellan en gammal och en ny vattenstämpel. Arbetsflödet för den här metoden illustreras i följande diagram: 

![Arbetsflöde för att använda en vattenstämpel](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Stegvisa instruktioner finns i följande självstudier: 

- [Kopiera data stegvis från en tabell i Azure SQL Database till Azure Blob Storage](tutorial-incremental-copy-powershell.md)
- [Kopiera data stegvist från flera tabeller i en lokal SQL Server till Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Inläsning av deltadata från SQL DB med tekniken Ändringsspårning
Tekniken för ändringsspårning är en enkel lösning i SQL Server och Azure SQL Database som tillhandahåller en effektiv ändringsspårningsmekanism för program. Det gör att ett program enkelt kan identifiera data som har infogats, uppdaterats eller tagits bort. 

Arbetsflödet för den här metoden illustreras i följande diagram:

![Arbetsflöde för att använda Ändringsspårning](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Stegvisa instruktioner finns i följande självstudie: <br/>
[Kopiera data stegvis från Azure SQL Database till Azure Blob Storage med ändringsspårningsteknik](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Läsa in endast nya och ändrade filer med hjälp av LastModifiedDate
Du kan kopiera de nya och ändrade filerna endast med hjälp av LastModifiedDate till målarkivet. ADF ska söka igenom alla filer från arkivet för källa, tillämpa filtret genom sina LastModifiedDate och endast kopiera filen nya och uppdaterade sedan senaste gången till målarkiv.  Tänk om du Låt ADF genomsökning enorma mängder filer men bara kopiera filer till mål kan du fortfarande förväntar långsamma på grund av sökning är tidskrävande samt.   

Stegvisa instruktioner finns i följande självstudie: <br/>
[Kopiera stegvis nya och ändrade filer baserat på LastModifiedDate från Azure Blob Storage till Azure Blob Storage](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Läsa in endast nya filer med hjälp av tidspartitionerat mapp- eller filnamn.
Du kan kopiera endast nya filer, där filer eller mappar redan har tidspartitionerats med tidssektorinformation som en del av fil- eller mappnamnet (till exempel /åååå/mm/dd/fil.csv). Det är de flesta prestanda-metoden för inkrementell inläsning av nya filer. 

Stegvisa instruktioner finns i följande självstudie: <br/>
[Kopiera stegvis nya filer baserat på tidspartitionerat mapp- eller filnamn från Azure Blob Storage till Azure Blob Storage](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>Nästa steg
Fortsätt till följande självstudie: 

> [!div class="nextstepaction"]
>[Kopiera data stegvis från en tabell i Azure SQL Database till Azure Blob Storage](tutorial-incremental-copy-powershell.md)
