---
title: Vad är TDSP (Team Data Science Process)?
description: Innehåller en datavetenskapsmetod för att leverera prediktiva Analyslösningar och intelligenta program.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 10/20/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3b4e8c78d7402c254c91c3e100814e1f3eafc41b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61429441"
---
# <a name="what-is-the-team-data-science-process"></a>Vad är TDSP (Team Data Science Process)?

Team Data Science Process (TDSP) är en flexibel, iterativ datavetenskapsmetod att leverera prediktiva Analyslösningar och intelligenta program effektivt. TDSP hjälper till att förbättra samarbete mellan grupper och inlärning. Du får de bästa metodtipsen och strukturerna från Microsoft och andra i branschen, vilket underlättar implementeringen av datavetenskapsinitiativ. Målet är att hjälpa företag att dra nytta av sina analysprogram fullt ut.

Den här artikeln innehåller en översikt över TDSP och dess huvudkomponenter. Vi tillhandahåller en allmän beskrivning av processen som kan implementeras med en mängd olika verktyg. En mer detaljerad beskrivning av projektuppgifter och roller som ingår i livscykeln för processen finns i ytterligare länkade ämnen. Du får också information om hur du implementerar TDSP med hjälp av en specifik uppsättning Microsoft-verktyg och infrastruktur som vi använder för att implementera TDSP i vårt team.

## <a name="key-components-of-the-tdsp"></a>Viktigaste komponenterna i TDSP

TDSP består av följande huvudkomponenter:

- En **livscykeln för datavetenskap** definition
- En **standardiserade projektstruktur**
- **Infrastruktur och resurser** för dataforskningsprojekt
- **Verktyg och hjälpmedel** för projektkörning av


## <a name="data-science-lifecycle"></a>Livscykeln för datavetenskap

Team Data Science Process (TDSP) innehåller en livscykel för att strukturera utvecklingen av dina data science-projekt. Livscykeln beskrivs stegen, från början till slut att projekt vanligtvis följer när de utförs.

Om du använder en annan data science-livscykeln, till exempel [SKARPA-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) eller din organisations egna anpassade process, du kan fortfarande använda TDSP uppgiftsbaserade i samband med dessa livscykler. På en hög nivå har dessa olika metoder mycket gemensamt. 

Den här livscykeln har utformats för dataforskningsprojekt som levereras som en del av intelligenta program. Dessa program distribuera machine learning eller artificiell intelligens modeller för förutsägelseanalys. Undersökande dataforskningsprojekt eller ad hoc-analysprojekt kan också dra nytta av den här processen. Men i så fall vissa av stegen som beskrivs kan inte behövs.    

Livscykeln beskrivs de viktigaste stegen som projekt vanligtvis köra, ofta upprepade gånger:

* **Förståelse för verksamheten**
* **Data förvärv och förståelse av**
* **Modellering**
* **Distribution**
* **Kundgodkännande**

Här är en visuell representation av den **Team Data Science Process-livscykeln**. 

![TDSP-Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

Mål, uppgifter och dokumentation artefakter för varje steg i livscykeln i TDSP beskrivs i den [Team Data Science Process-livscykeln](lifecycle.md) avsnittet. Dessa uppgifter och artefakter är associerade med Projektroller:

- Lösningsarkitekt
- Projektledare
- Dataexpert
- Projektledare 

Följande diagram ger en rutnätsvy för aktiviteter (i blått) och artefakter (i grönt) som är associerade med varje steg i livscykeln (på den horisontala axeln) för dessa roller (på den vertikala axeln). 

[![TDSP-roles-and-tasks](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Standardiserad projektstruktur

Med alla projekt dela en katalogstruktur och använda mallar för project-dokument gör det enkelt för gruppmedlemmar att hitta information om deras projekt. Alla kod och dokument lagras i ett versionskontrollsystem (paket) som Git, TFS eller Subversion för att aktivera samarbete. Spåra uppgifter och funktioner i en flexibel spårningssystem som Jira, Rally och Azure DevOps-projektet kan närmare spårning av koden för enskilda funktioner. Sådana spårning kan också team att få bättre kostnadsuppskattningar. TDSP rekommenderar att du skapar en separat lagringsplats för varje projekt på paket för versionshantering, informationssäkerhet och samarbete. Standardiserad strukturen för alla projekt hjälper till att bygga upp institutionella kunskaper i organisationen.

Vi tillhandahåller mallar för mappstrukturen och nödvändiga dokument i standardplatserna. Den här mappstrukturen organiserar de filer som innehåller kod för datagranskning och extrahering av funktionen och som registrera modellen iterationer. Dessa mallar gör det enklare för gruppmedlemmar att förstå arbete som görs av andra och lägga till nya medlemmar i teams. Det är enkelt att visa och uppdatera mallar i markdown-format. Använd mallar för att ge checklistor viktiga frågor för varje projekt för att se till att problemet är väl definierade och att slutprodukter uppfyller den kvalitet som förväntat. Exempel:

- projektet auktoriserad att dokumentera affärsproblem och projektets omfång
- data som rapporterar till dokumentet strukturen och statistik för rådata
- modellen rapporter för att dokumentera de härledda funktionerna
- prestandamått för modellen, till exempel ROC kurvor eller MSE


[![TDSP-kataloger](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

Katalogstrukturen kan klonas från [GitHub](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastruktur och resurser för dataforskningsprojekt  

TDSP innehåller rekommendationer för hantering av delade analyser och lagringsinfrastruktur som:

- filsystem för molnet för att lagra datauppsättningar 
- databaser
- Big data-kluster (Hadoop- eller Spark) 
- Machine learning-tjänsten 

Infrastruktur för analys och lagring kan finnas i molnet eller lokalt. Det här är där rådata och bearbetade datauppsättningar lagras. Den här infrastrukturen kan reproducerbar analys. Det förhindrar också duplicering, vilket kan leda till inkonsekvenser och onödiga infrastrukturkostnader. Verktyg som etablerar de delade resurserna, spåra dem och att varje medlem i gruppen att ansluta till dessa resurser på ett säkert sätt. Det är också en bra idé att ha projektmedlemmar skapa en konsekvent beräkningsmiljö. Olika teammedlemmar kan replikera och validera experiment.

Här är ett exempel på ett team som arbetar med flera projekt och dela olika moln analytics-infrastrukturkomponenter.

[![TDSP-infrastructure](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Verktyg och hjälpmedel för projektkörning av

Introduktion till processer i de flesta organisationer är en utmaning. Verktyg som finns att implementera data science process och livscykelhantering hjälp lägre hinder gällande och öka antagandet konsekvens. TDSP ger en inledande uppsättning verktyg och skript för att ge implementering av TDSP i ett team. Det hjälper också att automatisera vissa av de vanliga uppgifterna i livscykeln för datavetenskap, till exempel datagranskning och modellering av baslinjen. Det finns en väldefinierad struktur som anges för enskilda användare kan bidra med delade verktyg och hjälpmedel i sina team delade lagringsplatsen. Dessa resurser kan sedan användas av andra projekt inom gruppen eller organisationen. TDSP planerar också att aktivera bidrag av verktyg och hjälpmedel för hela communityn. TDSP-verktyg kan klonas från [GitHub](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Nästa steg

[Team Data Science Process: Roller och uppgifter](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) beskrivs rollerna som personal och förknippade aktiviteter för data science team som standardiserar om den här processen. 
