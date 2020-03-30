---
title: Vad är teamdatavetenskapsprocessen?
description: Innehåller en datascience-metod för att leverera lösningar för prediktiv analys och intelligenta program.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: overview
ms.date: 1/10/2020
ms.author: tdsp
ms.custom: previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 10d6e562301e089700940ac5dfb212bcc4e09653
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79088082"
---
# <a name="what-is-the-team-data-science-process"></a>Vad är teamdatavetenskapsprocessen?

Team Data Science Process (TDSP) är en smidig, iterativ datavetenskapsmetod för att leverera prediktiva analyslösningar och intelligenta applikationer effektivt. TDSP hjälper till att förbättra teamets samarbete och inlärning genom att föreslå hur grupproller fungerar bäst tillsammans. TDSP innehåller bästa praxis och strukturer från Microsoft och andra branschledare för att hjälpa till att lyckas genomföra datavetenskapliga initiativ. Målet är att hjälpa företag att dra nytta av sina analysprogram fullt ut.

Den här artikeln innehåller en översikt över TDSP och dess huvudkomponenter. Vi ger en generisk beskrivning av processen här som kan genomföras med olika typer av verktyg. En mer detaljerad beskrivning av projektaktiviteter och roller som ingår i processens livscykel finns i ytterligare länkade ämnen. Vägledning om hur du implementerar TDSP med hjälp av en specifik uppsättning Microsoft-verktyg och -infrastruktur som vi använder för att implementera TDSP i våra team finns också.

## <a name="key-components-of-the-tdsp"></a>Viktiga komponenter i TDSP

TDSP består av följande nyckelkomponenter:

- En livscykeldefinition **för datavetenskap**
- En **standardiserad projektstruktur**
- **Infrastruktur och resurser** för datavetenskapliga projekt
- **Verktyg och verktyg** för projektkörning


## <a name="data-science-lifecycle"></a>Livscykel för datavetenskap

Team Data Science Process (TDSP) ger en livscykel för att strukturera utvecklingen av dina datavetenskapliga projekt. Livscykeln beskriver de fullständiga steg som framgångsrika projekt följer.

Om du använder en annan livscykel för datavetenskap, till exempel [CRISP-DM,](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining) [KDD](https://wikipedia.org/wiki/Data_mining#Process) eller organisationens egen anpassade process, kan du fortfarande använda den uppgiftsbaserade TDSP-enheten i samband med dessa utvecklingslivscykler. På en hög nivå har dessa olika metoder mycket gemensamt. 

Den här livscykeln har utformats för datavetenskapliga projekt som levereras som en del av intelligenta program. Dessa program distribuerar maskininlärning eller artificiell intelligens modeller för prediktiv analys. Utforskande datavetenskapsprojekt eller improviserade analysprojekt kan också dra nytta av att använda denna process. Men i sådana fall kan det hända att vissa av de beskrivna stegen inte behövs.    

Livscykeln beskriver de stora faserna som projekt vanligtvis körs, ofta iterativt:

* **Affärssyssning**
* **Datainsamling och förståelse**
* **Modellering**
* **Distribution**
* **Kundacceptans**

Här är en visuell representation av **Team Data Science Process livscykel**. 

![TDSP-livscykel2](./media/overview/tdsp-lifecycle2.png) 

Mål, uppgifter och dokumentationsartefakter för varje fas i livscykeln i TDSP beskrivs i livscykelavsnittet [Team Data Science Process.](lifecycle.md) Dessa aktiviteter och artefakter är associerade med projektroller:

- Lösningsarkitekt
- Projektledare
- Data Scientist
- Projektledare 

Följande diagram innehåller en rutnätsvy över de aktiviteter (i blått) och artefakter (i grönt) som är associerade med varje steg i livscykeln (på den vågräta axeln) för dessa roller (på den lodräta axeln). 

[![TDSP-roller och uppgifter](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Standardiserad projektstruktur

Att låta alla projekt dela en katalogstruktur och använda mallar för projektdokument gör det enkelt för gruppmedlemmarna att hitta information om sina projekt. All kod och alla dokument lagras i ett versionskontrollsystem (VCS) som Git, TFS eller Subversion för att möjliggöra gruppsamarbete. Genom att spåra uppgifter och funktioner i ett flexibelt projektspårningssystem som Jira, Rally och Azure DevOps kan du följa koden närmare för enskilda funktioner. Sådan spårning gör det också möjligt för team att få bättre kostnadsberäkningar. TDSP rekommenderar att du skapar en separat databas för varje projekt på VCS för versionshantering, informationssäkerhet och samarbete. Den standardiserade strukturen för alla projekt bidrar till att bygga upp institutionell kunskap i hela organisationen.

Vi tillhandahåller mallar för mappstrukturen och nödvändiga dokument på standardplatser. Den här mappstrukturen ordnar de filer som innehåller kod för datautforskning och funktionsextrahering och som registrerar modelliterationer. Dessa mallar gör det enklare för gruppmedlemmarna att förstå det arbete som utförs av andra och att lägga till nya medlemmar i teamen. Det är enkelt att visa och uppdatera dokumentmallar i markdown-format. Använd mallar för att ge checklistor med viktiga frågor för varje projekt för att försäkra att problemet är väl definierat och att slutprodukter uppfyller den förväntade kvaliteten. Exempel på rekommendationer:

- en projektstadga för att dokumentera projektets affärsproblem och omfattning
- datarapporter för att dokumentera strukturen och statistiken för rådata
- modellrapporter för att dokumentera de härledda funktionerna
- modellprestandamått som ROC-kurvor eller MSE


[![TDSP-kataloger](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

Katalogstrukturen kan klonas från [GitHub](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastruktur och resurser för datavetenskapliga projekt  

TDSP innehåller rekommendationer för hantering av delad analys- och lagringsinfrastruktur, till exempel:

- molnfilsystem för lagring av datauppsättningar 
- databaser
- stordatakluster (Hadoop eller Spark) 
- maskininlärningstjänst 

Analys- och lagringsinfrastrukturen, där råa och bearbetade datauppsättningar lagras, kan finnas i molnet eller lokalt. Den här infrastrukturen möjliggör reproducerbar analys. Man undviker också dubbelarbete, vilket kan leda till inkonsekvenser och onödiga infrastrukturkostnader. Verktyg tillhandahålls för att etablera delade resurser, spåra dem och låta varje gruppmedlem ansluta till dessa resurser på ett säkert sätt. Det är också en god idé att låta projektmedlemmar skapa en konsekvent beräkningsmiljö. Olika gruppmedlemmar kan sedan replikera och validera experiment.

Här är ett exempel på ett team som arbetar med flera projekt och delar olika molnanalysinfrastrukturkomponenter.

[![TDSP-infrastruktur](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Verktyg och verktyg för projektkörning

Att införa processer i de flesta organisationer är en utmaning. Verktyg som tillhandahålls för att genomföra datascience processen och livscykeln bidra till att minska hindren för och öka konsekvensen i deras antagande. TDSP tillhandahåller en första uppsättning verktyg och skript för att få fart på införandet av TDSP i ett team. Det hjälper också till att automatisera några av de vanliga uppgifterna i livscykeln för datavetenskap, till exempel datautforskning och baslinjemodellering. Det finns en väldefinierad struktur som tillhandahålls för enskilda personer att bidra med delade verktyg och verktyg i teamets delade kodarkiv. Dessa resurser kan sedan utnyttjas av andra projekt inom teamet eller organisationen. TDSP planerar också att möjliggöra bidrag av verktyg och verktyg till hela samhället. TDSP-verktygen kan klonas från [GitHub](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Nästa steg

[Team Data Science Process: Roller och uppgifter](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) Beskriver de viktigaste personalrollerna och deras associerade uppgifter för ett datavetenskapslag som standardiserar den här processen. 
