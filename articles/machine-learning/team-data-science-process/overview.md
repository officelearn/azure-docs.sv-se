---
title: Vad är TDSP (Team Data Science Process)? | Microsoft Docs
description: Ger en datavetenskap metod för att leverera förutsägelseanalyslösningar och intelligent program.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: deguhath
ms.openlocfilehash: 4a73b988863a27f872c695cf209d2c46c6bb5f89
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="what-is-the-team-data-science-process"></a>Vad är TDSP (Team Data Science Process)?

Team Data vetenskap processen (TDSP) är en flexibel och iterativ datavetenskap metoder att leverera förutsägelseanalyslösningar och intelligent program effektivt. TDSP hjälper till att förbättra samarbete mellan grupper och inlärning. Du får de bästa metodtipsen och strukturerna från Microsoft och andra i branschen, vilket underlättar implementeringen av datavetenskapsinitiativ. Målet är att hjälpa företag att dra nytta av sina analysprogram fullt ut.

Den här artikeln innehåller en översikt över TDSP och dess huvudsakliga komponenter. Vi ger en allmän beskrivning av processen som kan implementeras med en mängd olika verktyg. En mer detaljerad beskrivning av projektaktiviteter och roller som ingår i livscykeln för processen erbjuder ytterligare länkade avsnitt. Information om hur du implementerar TDSP med en specifik uppsättning verktyg och Microsoft infrastruktur som vi använder för att implementera TDSP i våra tillhandahålls också.

## <a name="key-components-of-the-tdsp"></a>Viktiga komponenter av TDSP

TDSP omfattar följande huvudkomponenter:

- En **datavetenskap livscykel** definition
- En **standardiserade projektstruktur**
- **Infrastruktur och resurser** för datavetenskap projekt
- **Verktyg och hjälpmedel** för projektet körning


## <a name="data-science-lifecycle"></a>Datavetenskap livscykel

Team Data vetenskap processen (TDSP) ger en livscykel för att strukturera utvecklingen av dina datavetenskap projekt. Livscykeln beskrivs stegen från början till slut att projekt vanligtvis följer när de utförs.

Om du använder en annan datavetenskap livscykel, exempelvis [SKARPA DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) eller din organisations egna anpassade process, du kan fortfarande använda uppgiftsbaserade TDSP i samband med dessa development livscykler. På en hög nivå har dessa olika metoder mycket gemensamt. 

Den här livscykeln har utformats för datavetenskap projekt som levereras som en del av intelligent program. Dessa program distribuera machine learning eller artificiell intelligence modeller för förutsägelseanalys. Undersökande datavetenskap projekt eller ad hoc-analytics projekt kan också dra nytta av den här processen. Men i sådana fall vissa av stegen som beskrivs kanske inte behövs.    

Livscykeln beskrivs viktiga steg som projekt vanligtvis köra, ofta upprepade gånger:

* **Så här fungerar för företag**
* **Datainsamling och förstå**
* **Modeling**
* **Distribution**
* **Kundens godkännande**

Här är en bild av den **Team datavetenskap Process livscykel**. 

![TDSP Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

Mål, uppgifter och dokumentation artefakter för varje steg i livscykeln i TDSP beskrivs i den [Team datavetenskap Process livscykel](lifecycle.md) avsnittet. Dessa uppgifter och artefakter är kopplade till Projektroller:

- Lösningsarkitekt
- Projektledare
- Dataexpert
- Projektledare 

Följande diagram ger en rutnätsvy uppgifter (i blått) och artefakter (i grönt) som är associerade med varje steg i livscykeln (på den horisontala axeln) för dessa roller (på den vertikala axeln). 

![TDSP-roller-och-uppgifter](./media/overview/tdsp-tasks-by-roles.png)

## <a name="standardized-project-structure"></a>Standardiserade projektstruktur

Med alla projekt som delar en katalogstruktur och använda mallar för projektdokument enkelt gruppmedlemmarna kan hitta information om deras projekt. All kod och dokument lagras i ett versionskontrollsystem (VC: er) som Git, TFS eller Subversion för att aktivera gruppsamarbete. Uppgifter och funktioner i en flexibel följer upp system som Jira projekt kan Rally, Visual Studio Team Services spåras, närmare spårning av koden för enskilda funktioner. Sådana spårning kan också grupper för att få bättre kostnad uppskattningar. TDSP rekommenderar att skapa en separat databas för varje projekt i paket för versionshantering, informationssäkerhet och samarbete. Standardiserade strukturen för alla projekt bidrar till att skapa institutionella kunskap i organisationen.

Vi ger mallar för mappstrukturen och nödvändiga dokument i standardplatserna. Den här mappstrukturen organiserar de filer som innehåller koden för datagranskning och extrahering av funktionen och som registrerar modellen iterationer. Mallarna gör det enklare för medlemmarna i gruppen att förstå arbete som utförs av andra och lägga till nya medlemmar i grupper. Det är enkelt att visa och uppdatera mallar i markdown-format. Använda mallar för att ge checklistor viktiga frågor för varje projekt till att säkerställa att problemet är väl definierade och att produkterna uppfyller kvaliteten förväntades. Exempel:

- projektet auktoriserad att dokumentera affärsproblem och projektets omfattning
- data rapporterar till dokumentet strukturen och statistik för rådata
- modellen rapporter för att dokumentera härledda funktioner
- modellen prestandamått, till exempel ROC kurvor eller mus


![TDSP kataloger](./media/overview/tdsp-dir-structure.png)

Katalogstrukturen kan klonas från [Github](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastruktur och resurser för datavetenskap projekt  

TDSP innehåller rekommendationer för att hantera delade analyser och lagringsinfrastruktur som:

- molnet filsystem för lagring av datauppsättningar, 
- databaser
- stordata (Hadoop eller Spark) kluster 
- Machine learning-tjänster. 

Infrastruktur för analys och lagring kan vara i molnet eller lokalt. Detta lagras rådata och bearbetade datauppsättningar. Den här infrastrukturen kan reproduceras analys. Det förhindrar också duplicering, vilket kan leda till inkonsekvenser och onödiga infrastrukturkostnader. Verktyg som etablera delade resurser, följa upp dem och att varje medlem i gruppen kan ansluta till dessa resurser på ett säkert sätt. Det är också en bra idé har projektmedlemmar skapar en konsekvent beräknings-miljö. Olika gruppmedlemmar kan replikera och validera experiment.

Här är ett exempel på ett team arbetar på flera projekt och dela olika moln analytics-infrastrukturkomponenter.

![TDSP-infrastruktur](./media/overview/tdsp-analytics-infra.png)


## <a name="tools-and-utilities-for-project-execution"></a>Verktyg och hjälpmedel för projektet körning

Introduktion till processer i de flesta organisationer är svårt. Verktygen att implementera datavetenskap processen och livscykel hjälp lägre hinder för och öka konsekvenskontrollen antas. TDSP ger en inledande uppsättning verktyg och skript att antagandet av TDSP i ett team. Det hjälper också till att automatisera vanliga uppgifter i datavetenskap livscykel, till exempel datagranskning och baslinjen modellering. Det finns en väldefinierad struktur för enskilda användare att bidra med delade verktyg och hjälpmedel i deras team delad kod databas. Dessa resurser kan sedan användas av andra projekt i teamet eller organisationen. TDSP planerar också att aktivera bidrag verktyg och hjälpmedel till hela gruppen. Verktygen TDSP kan klonas från [Github](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Nästa steg

[: Team vetenskap roller och aktiviteter](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) beskrivs rollerna personal och förknippade aktiviteter för en datavetenskap team som standardiserar på den här processen. 
