---
title: "Läs in data till Azure storage-miljöer för analytics | Microsoft Docs"
description: "Flytta data till och från Azure Blob Storage"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: bradsev
ms.openlocfilehash: 7d7da4f6dfed03d470c5b5706aaf412c07096120
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Läs in data i lagringsmiljöer för analys
Team av vetenskapliga data kräver att data ska inhämtas eller läses in i en mängd olika lagringsplatser miljöer bearbetas eller analyseras i det lämpligaste sättet i varje steg i processen. Datamål som ofta används för bearbetning inkluderar Azure Blob Storage, SQL Azure-databaser, SQL Server på Azure VM, HDInsight (Hadoop) och Azure Machine Learning. 

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

Detta **menyn** länkar till avsnitt som beskriver hur du mata in data i dessa mål miljöer där data lagras och bearbetas.

Teknisk och affärsbehov samt deras ursprungliga plats formatera och storleken på dina data avgör mål-miljöer där data behöver inhämtas för att uppnå målen för din analys. Det är inte ovanligt att ett scenario för kräver att data flyttas mellan flera miljöer för olika uppgifter som krävs för att skapa en förutsägelsemodell. Den här aktivitetssekvensen kan exempelvis innehålla datagranskning, föregående bearbetning, rensa, ned provtagning och modellen utbildning.

