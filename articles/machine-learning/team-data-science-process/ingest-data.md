---
title: Läs in data i Azure lagringsmiljöer för analys | Microsoft Docs
description: Flytta data till och från Azure Blob Storage
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: a5db14b99a81c373fbc72f523798e1f3bbdf9285
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344509"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Läs in data i lagringsmiljöer för analys

Team Data Science Process kräver att data ska matas in eller läses in i en mängd olika lagringsmiljöer bearbetas eller analyseras i det lämpligaste sättet i varje steg i processen. Datamål som ofta används för bearbetning inkluderar Azure Blob Storage, SQL Azure-databaser, SQL Server på virtuella Azure-datorer, HDInsight (Hadoop) och Azure Machine Learning. 

I följande artiklar beskrivs hur du mata in data i olika målmiljöer där data lagras och bearbetas.

* Till och från [Azure Blob Storage](move-azure-blob.md)
* Att [SQLServer på Azure VM](move-sql-server-virtual-machine.md)
* Att [Azure SQL-databas](move-sql-azure.md)
* Att [Hive-tabeller](move-hive-tables.md)
* Att [SQL partitionerade tabeller](parallel-load-sql-partitioned-tables.md)
* Från [lokal SQLServer](move-sql-azure-adf.md)

Teknisk och affärsbehov, samt deras ursprungliga plats, formatera och storleken på dina data kommer att fastställa målmiljöer dit data ska matas in för att uppnå målen för din analys. Det är inte ovanligt att ett scenario för kräver att data flyttas mellan flera miljöer för olika uppgifter som krävs för att konstruera en förutsägelsemodell. Den här aktivitetssekvensen kan exempelvis innehålla datagranskning, bearbeta data i förväg, rensning, ned sampling och modellen.
