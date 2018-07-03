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
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: bb6cfc6575bdbe83aeb258069a9c239147d30bca
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049187"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Läsa in data stegvis från ett källdatalager till ett måldatalager

I en dataintegrationslösning är stegvis inläsning av data (eller deltadata) efter den första fullständiga datainläsningen ett vanligt scenario. Självstudierna i det här avsnittet visar olika sätt att läsa in data inkrementellt med Azure Data Factory.

## <a name="delta-data-loading-by-using-a-watermark"></a>Deltadatainläsning med vattenstämpel
I det här fallet definierar du en vattenstämpel i din källdatabas. En vattenstämpel är en kolumn som har den senast uppdaterade tidsstämpeln eller en stegvis ökande nyckel. Lösningen för deltainläsning läser in de ändrade data mellan en gammal och en ny vattenstämpel. Arbetsflödet för den här metoden illustreras i följande diagram: 

![Arbetsflöde för att använda en vattenstämpel](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Stegvisa instruktioner finns i följande självstudier: 

- [Kopiera data stegvis från en tabell i Azure SQL-databas till Azure Blob Storage](tutorial-incremental-copy-powershell.md)
- [Kopiera data stegvist från flera tabeller i en lokal SQL Server till Azure SQL-databas](tutorial-incremental-copy-multiple-tables-powershell.md)

## <a name="delta-data-loading-by-using-the-change-tracking-technology"></a>Inläsning av deltadata med tekniken Ändringsspårning
Tekniken för ändringsspårning är en enkel lösning i SQL Server och Azure SQL Database som tillhandahåller en effektiv ändringsspårningsmekanism för program. Det gör att ett program enkelt kan identifiera data som har infogats, uppdaterats eller tagits bort. 

Arbetsflödet för den här metoden illustreras i följande diagram:

![Arbetsflöde för att använda Ändringsspårning](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Stegvisa instruktioner finns i följande självstudie: <br/>
[Kopiera data stegvis från Azure SQL-databas till Azure Blob Storage med ändringsspårningsteknik](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="next-steps"></a>Nästa steg
Fortsätt till följande självstudie: 

> [!div class="nextstepaction"]
>[Kopiera data stegvis från en tabell i Azure SQL-databas till Azure Blob Storage](tutorial-incremental-copy-powershell.md)
