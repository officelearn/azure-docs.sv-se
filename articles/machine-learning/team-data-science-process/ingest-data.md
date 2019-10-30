---
title: Läs in data i Azure Storage-miljöer – team data science process
description: Lär dig mer om hur du matar in data i olika mål miljöer där data lagras och bearbetas.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 515decfafe46ad0c1b5b90743688abc26a975903
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053266"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Läs in data i lagringsmiljöer för analys

Team data science-processen kräver att data matas in eller läses in i en mängd olika lagrings miljöer som ska bearbetas eller analyseras på det sätt som är lämpligast i varje steg i processen. Data destinationer som används ofta för bearbetning inkluderar Azure Blob Storage, SQL Azure-databaser, SQL Server på virtuell Azure-dator, HDInsight (Hadoop) och Azure Machine Learning. 

I följande artiklar beskrivs hur du matar in data i olika mål miljöer där data lagras och bearbetas.

* Till/från [Azure Blob Storage](move-azure-blob.md)
* För att [SQL Server på virtuell Azure-dator](move-sql-server-virtual-machine.md)
* Till [Azure SQL Database](move-sql-azure.md)
* Till [Hive-tabeller](move-hive-tables.md)
* Till [partitionerade SQL-tabeller](parallel-load-sql-partitioned-tables.md)
* Från [lokala SQL Server](move-sql-azure-adf.md)

De tekniska och affärs behoven, samt den ursprungliga platsen, formatet och storleken på dina data avgör vilka mål miljöer som data måste matas in för att uppnå målen för din analys. Det är inte ovanligt att ett scenario kräver att data flyttas mellan flera miljöer för att uppnå den mängd uppgifter som krävs för att skapa en förutsägelse modell. Den här aktivitetssekvensen kan omfatta exempelvis data utforskning, för bearbetning, rensning, nedsampling och modell träning.
