---
title: Portaler för att skapa och redigera loggen frågor i Azure Log Analytics | Microsoft Docs
description: Den här artikeln beskriver portalerna som du kan använda i Azure Log Analytics för att skapa och redigera logga sökningar.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: magoedte; bwren
ms.component: na
ms.openlocfilehash: e2ea0bf1fb3f1c63f4e6f037e465e8fdfd9a4374
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133026"
---
# <a name="portals-for-creating-and-editing-log-queries-in-azure-log-analytics"></a>Portaler för att skapa och redigera loggen frågor i Azure Log Analytics

Du kan använda loggen sökningar på olika ställen i Log Analytics för att hämta data från arbetsytan.  Faktiskt skapar och redigerar frågor förutom att arbeta interaktivt med data som returneras om, har du två alternativ som beskrivs nedan.  

## <a name="log-search"></a>Loggsökning 
Sidan loggen är tillgänglig på Azure-portalen.  Det är lämpligt för att skapa grundläggande frågor som kan skapas på en enda rad.  Sök i loggfilen kan användas utan att starta en extern portal och du kan använda för att utföra en mängd funktioner med loggen sökningar, inklusive att skapa Varningsregler, skapar datorgrupper och exportera resultatet av frågan.  

Loggen Sök innehåller flera funktioner för att redigera frågan utan att ha fullständiga kunskaper om frågespråket.  Du kan få en översikt över funktionerna i [skapa loggen söker i Azure Log Analytics loggen sökning](log-analytics-log-search-log-search-portal.md).


![Logga söksidan](media/log-analytics-log-search-portals/log-search-portal.png)

## <a name="advanced-analytics-portal"></a>Avancerade Analytics-portalen
Avancerade analyser portal är en dedikerad portal som ger avancerade funktioner som inte är tillgängliga i loggen Sök från Azure-portalen.  Funktionen ger möjlighet att redigera en fråga på flera rader, köra kod selektivt, sammanhangsberoende Intellisense och Smart Analytics.  Advanced Analytics-portalen är mest lämpliga för att utforma komplexa frågor som antingen sparas som en logg sökning eller kopieras och klistras in i andra logganalys-element.  Du kan starta Advanced Analytics-portalen från en länk på sidan logga.

![Avancerade Analytics-portalen](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


På grund av dess avancerade funktioner, ska du vanligtvis använda avancerade analyser portal som din primära verktyget för att skapa och redigera frågor.  När du har bestämt att frågan fungerar som förväntat och sedan du kopierar och klistrar in den någon annanstans, till exempel logga söksidan eller View Designer.  

### <a name="firewall-requirements"></a>Krav för brandväggen
Din webbläsare kräver åtkomst till följande adresser åt Advanced Analytics-portalen.  Om din webbläsare har åtkomst till Azure-portalen via en brandvägg, måste du aktivera åtkomst till dessa adresser.

| URI | IP-adress | Portar |
|:---|:---|:---|
| portal.loganalytics.io | Dynamisk | 80,443 |
| api.loganalytics.io    | Dynamisk | 80,443 |
| docs.loganalytics.io   | Dynamisk | 80,443 |


## <a name="next-steps"></a>Nästa steg

- Gå igenom en självstudiekurs om hur du använder [loggen Sök](log-analytics-tutorial-viewdata.md) att lära dig hur du skapar frågor med frågespråket
- Kolla in den [Advanced Analytics portal](https://go.microsoft.com/fwlink/?linkid=856587) att skapa avancerade frågor och använda som en utvecklingsmiljö för logg-sökningar.

