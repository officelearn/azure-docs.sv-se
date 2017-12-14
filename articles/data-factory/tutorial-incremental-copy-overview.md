---
title: Kopiera data stegvis med Azure Data Factory | Microsoft Docs
description: "Dessa självstudier visar hur du inkrementellt kopierar data från ett källdatalager till ett måldatalager. Den första kopierar data från en tabell. "
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/05/2017
ms.author: shlo
ms.openlocfilehash: 2ae6cb42685dfb227bd75f83e73dfdf646ab909f
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Läsa in data stegvis från ett källdatalager till ett måldatalager

I en dataintegrationslösning är stegvis inläsning av data (eller deltadata) efter den första fullständiga datainläsningen ett vanligt scenario. Självstudierna i det här avsnittet visar olika sätt att läsa in data inkrementellt med Azure Data Factory version 2.

## <a name="delta-data-loading-using-a-watermark"></a>Deltadatainläsning med vattenstämpel
I det här fallet definierar du en vattenstämpel i din källdatabas. En vattenstämpel är en kolumn som har den senast uppdaterade tidsstämpeln eller en stegvis ökande nyckel. Lösningen för deltainläsning läser in de ändrade data mellan en gammal och en ny vattenstämpel. Arbetsflödet för den här metoden illustreras i följande diagram: 

![Arbetsflöde för att använda en vattenstämpel](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Stegvisa instruktioner finns i följande självstudier: 

- [Kopiera data stegvis från en tabell i Azure SQL-databas till Azure Blob Storage](tutorial-incremental-copy-powershell.md)
- [Kopiera data stegvist från flera tabeller i en lokal SQL Server till en Azure SQL-databas](tutorial-incremental-copy-multiple-tables-powershell.md)


## <a name="delta-data-loading-using-the-change-tracking-technology"></a>Inläsning av deltadata med tekniken Ändringsspårning
Tekniken för ändringsspårning är en enkel lösning i SQL Server och Azure SQL Database som tillhandahåller en effektiv ändringsspårningsmekanism för program. Det gör att ett program enkelt kan identifiera data som har infogats, uppdaterats eller tagits bort. 

Arbetsflödet för den här metoden illustreras i följande diagram:

![Arbetsflöde för att använda Ändringsspårning](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Stegvisa instruktioner finns i följande självstudie: <br/>
[Kopiera data stegvis från Azure SQL-databas till Azure Blob Storage med ändringsspårningsteknik](tutorial-incremental-copy-change-tracking-feature-powershell.md)


## <a name="next-steps"></a>Nästa steg
Fortsätt till följande självstudie: 

> [!div class="nextstepaction"]
>[Kopiera data stegvis från en tabell i Azure SQL-databas till Azure Blob Storage](tutorial-incremental-copy-powershell.md)
