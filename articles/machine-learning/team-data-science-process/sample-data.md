---
title: Exempel på data på olika Azure Storage-platser – Team Data Science Process
description: Exempeldata i Azure blob-behållare, SQL Server och Hive-tabeller för att minska den till en mindre men representativ och mer hanterbar storlek.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718611"
---
# <a name="sample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Exempeldata i Azure-blobcontainrar, SQL Server och Hive-tabeller

I följande artiklar beskrivs hur du exempel på data som lagras på en av tre olika Azure-platser:

* [**Azure blob-behållardata**](sample-data-blob.md) samplas genom att hämta dem programmässigt och sedan sampla dem med exempel på Python-kod.
* [**SQL Server-data**](sample-data-sql-server.md) samplas med både SQL och Python Programming Language. 
* [**Hive-tabelldata**](sample-data-hive.md) samplas med Hive-frågor.

Den här samplingsuppgiften är ett steg i [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Varför exempeldata?**

Om datauppsättningen som du planerar att analysera är stor är det oftast en bra idé att nedexpför att ta bort data för att minska dem till en mindre men representativ och mer hanterbar storlek. Nedskärningar kan underlätta data förståelse, utforskning och funktionsteknik. Den här samplingsrollen i Cortana Analytics-processen är att möjliggöra snabb prototyper av databehandlingsfunktioner och maskininlärningsmodeller.

