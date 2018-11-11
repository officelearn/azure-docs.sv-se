---
title: Exempeldata i Azure blob-behållare, SQL Server och Hive-tabeller | Microsoft Docs
description: Så här att utforska data som lagras i olika Azure enviromnents.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 80a9dfae-e3a6-4cfb-aecc-5701cfc7e39d
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 60431578ba8b5c740fe5e1124dcdad32c3cb2ede
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344099"
---
# <a name="heading"></a>Exempeldata i Azure blob-behållare, SQL Server och Hive-tabeller

I följande artiklar beskrivs hur du sampla data som lagras i något av tre olika Azure-platser:

* [**Azure container-blobbdata** ](sample-data-blob.md) samplas genom att hämta den via programmering och samlar det med Python-exempelkod.
* [**SQL Server-data** ](sample-data-sql-server.md) samplas med både SQL och Python Programming språk. 
* [**Hive-tabelldata** ](sample-data-hive.md) samplas med hjälp av Hive-frågor.

Den här aktiviteten för sampling är ett steg i den [Team Data Science Process (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

**Varför sampla data?**

Om datauppsättningen som du planerar att analysera är stor, men det är oftast en bra idé att nedåtsampla data för att minska det till en mindre men representativa och mer hanterbara storlek. Detta underlättar förståelse av data, utforskning och funktioner. Dess roll i Cortana Analytics-processen är att snabbt skapa prototyper för bearbetning av funktions- och machine learning-modeller.

