---
title: Exempel data på olika Azure Storage platser – team data science process
description: Exempeldata i Azure blob-behållare, SQL Server och Hive-tabeller för att minska det till en mindre men representativa och mer hanterbara storlek.
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
ms.openlocfilehash: 22e91d50227fcb44c7b90478d76379c14161ae05
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718611"
---
# <a name="heading"></a>Exempeldata i Azure blob-behållare, SQL Server och Hive-tabeller

I följande artiklar beskrivs hur du sampla data som lagras i något av tre olika Azure-platser:

* [**Azure container-blobbdata** ](sample-data-blob.md) samplas genom att hämta den via programmering och samlar det med Python-exempelkod.
* [**SQL Server-data** ](sample-data-sql-server.md) samplas med både SQL och Python Programming språk. 
* [**Hive-tabelldata** ](sample-data-hive.md) samplas med hjälp av Hive-frågor.

Den här aktiviteten för sampling är ett steg i den [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Varför sampla data?**

Om datauppsättningen som du planerar att analysera är stor, men det är oftast en bra idé att nedåtsampla data för att minska det till en mindre men representativa och mer hanterbara storlek. Downsizing kan under lätta data förståelse, utforskning och funktions teknik. Den här samplings rollen i Cortana Analytics-processen är att möjliggöra snabb prototyper av data bearbetnings funktioner och maskin inlärnings modeller.

