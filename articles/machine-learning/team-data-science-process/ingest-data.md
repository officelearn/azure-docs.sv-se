---
title: Läs in data i Azure Storage miljöer – team data science process
description: Lär dig mer om hur du matar in data i olika mål miljöer där data lagras och bearbetas.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f8eab59d810fb825dbebf80d01d8efd2dd0a9841
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720545"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Läs in data i lagringsmiljöer för analys

Team data science-processen kräver att data matas in eller läses in på det lämpligaste sättet i varje steg. Data destinationer kan omfatta Azure Blob Storage, SQL Azure-databaser, SQL Server på Azure VM, HDInsight (Hadoop), Synapse Analytics och Azure Machine Learning. 

I följande artiklar beskrivs hur du mata in data i olika målmiljöer där data lagras och bearbetas.

* Till och från [Azure Blob Storage](move-azure-blob.md)
* Att [SQLServer på Azure VM](move-sql-server-virtual-machine.md)
* För att [Azure SQL Database](move-sql-azure.md)
* Att [Hive-tabeller](move-hive-tables.md)
* Att [SQL partitionerade tabeller](parallel-load-sql-partitioned-tables.md)
* Från [lokal SQLServer](move-sql-azure-adf.md)

De tekniska och affärs behoven, samt den ursprungliga platsen, formatet och storleken på dina data avgör den bästa data inmatnings planen. Det är inte ovanligt att det finns flera steg i en bra plan. Den här aktivitetssekvensen kan exempelvis innehålla datagranskning, bearbeta data i förväg, rensning, ned sampling och modellen.  Azure Data Factory är en rekommenderad Azure-resurs för att dirigera data förflyttning och omvandling.
