---
title: Läsa in data i Azure Storage-miljöer – Team Data Science Process
description: Lär dig mer om hur du intar data i olika målmiljöer där data lagras och bearbetas.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720545"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Läs in data i lagringsmiljöer för analys

Team Data Science Process kräver att data intas eller laddas på det lämpligaste sättet i varje steg. Datadestinationer kan omfatta Azure Blob Storage, SQL Azure-databaser, SQL Server på Azure VM, HDInsight (Hadoop), Synapse Analytics och Azure Machine Learning. 

I följande artiklar beskrivs hur du intar data i olika målmiljöer där data lagras och bearbetas.

* Till/från [Azure Blob Storage](move-azure-blob.md)
* Till [SQL Server på Azure VM](move-sql-server-virtual-machine.md)
* Till [Azure SQL-databas](move-sql-azure.md)
* Till [Tabeller för Hive](move-hive-tables.md)
* Till [SQL-partitionerade tabeller](parallel-load-sql-partitioned-tables.md)
* Från [lokalt SQL Server](move-sql-azure-adf.md)

Tekniska och affärsmässiga behov, liksom den ursprungliga platsen, formatet och storleken på dina data kommer att avgöra den bästa datainmatningsplanen. Det är inte ovanligt att en bästa plan har flera steg. Den här sekvensen av uppgifter kan till exempel omfatta datautforskning, förbearbetning, rengöring, nedsampling och modellutbildning.  Azure Data Factory är en rekommenderad Azure-resurs för att dirigera data förflyttning och omvandling.
