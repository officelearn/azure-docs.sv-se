---
title: "Portaler för att skapa och redigera loggen frågor i Azure Log Analytics | Microsoft Docs"
description: "Den här artikeln beskriver portalerna som du kan använda i Azure Log Analytics för att skapa och redigera logga sökningar."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: magoedte; bwren
ms.openlocfilehash: b205f226d95d94b938a70a834ac0147e76d459ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="portals-for-creating-and-editing-log-queries-in-azure-log-analytics"></a>Portaler för att skapa och redigera loggen frågor i Azure Log Analytics

Du kan använda loggen sökningar på olika ställen i Log Analytics för att hämta data från arbetsytan.  Faktiskt skapar och redigerar frågor förutom att arbeta interaktivt med data som returneras om, har du två alternativ som beskrivs nedan.  

## <a name="log-search"></a>Loggsökning 
Sidan loggen är tillgänglig på Azure-portalen.  Det är lämpligt för att skapa grundläggande frågor som kan skapas på en enda rad.  Sök i loggfilen kan användas utan att starta en extern portal och du kan använda för att utföra en mängd funktioner med loggen sökningar, inklusive att skapa Varningsregler, skapar datorgrupper och exportera resultatet av frågan.  

Loggen Sök innehåller flera funktioner för att redigera frågan utan att ha fullständiga kunskaper om frågespråket.  Du kan få en översikt över funktionerna i [skapa loggen söker i Azure Log Analytics loggen sökning](log-analytics-log-search-log-search-portal.md).


![Logga söksidan](media/log-analytics-log-search-portals/log-search-portal.png)

## <a name="advanced-analytics-portal"></a>Avancerade Analytics-portalen
Avancerade analyser portal är en dedikerad portal som ger avancerade funktioner som inte är tillgängliga i loggen Sök från Azure-portalen.  Innefattar möjligheten att redigera en fråga på flera rader, selektivt köra kod, sammanhangsberoende Intellisense och smarta Analytics.  Advanced Analytics-portalen är mest lämpliga för att utforma komplexa frågor som antingen sparas som en logg sökning eller kopieras och klistras in i andra logganalys-element.  Du kan starta Advanced Analytics-portalen från en länk på sidan logga.

![Avancerade Analytics-portalen](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


På grund av dess avancerade funktioner, ska du vanligtvis använda avancerade analyser portal som din primära verktyget för att skapa och redigera frågor.  När du har bestämt att frågan fungerar som förväntat och sedan du kopierar och klistrar in den någon annanstans, till exempel logga söksidan eller View Designer.  Eftersom Advanced Analytics-portalen stöder frågor med flera rader om du behöver ta hänsyn till följande när du kopierar en fråga från den här portalen.

- Kommentarer måste tas bort från frågan innan den har kopieras och klistras in i en annan plats.  Du kan kommentera en rad genom att låta det med två snedstreck (/ /).  När du klistrar in en flera rad-fråga till en enda rad tas radbrytningar bort.  Om kommentarer ingår betraktas alla tecken efter den första kommentaren som en del av kommentaren.


## <a name="next-steps"></a>Nästa steg

- Gå igenom en självstudiekurs om hur du använder [loggen Sök](log-analytics-tutorial-viewdata.md) att lära dig hur du skapar frågor med frågespråket
- Kolla in den [Advanced Analytics portal](https://go.microsoft.com/fwlink/?linkid=856587) att skapa avancerade frågor och använda som en utvecklingsmiljö för logg-sökningar.

