---
title: Vad är TDSP (Team Data Science Process)?
description: Tillhandahåller en data vetenskaps metod för att leverera lösningar för förutsägelse analys och intelligenta program.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: overview
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4a341d33c957af8fe7423bda80f1d34a7f59cdd6
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94738007"
---
# <a name="what-is-the-team-data-science-process"></a>Vad är TDSP (Team Data Science Process)?

TDSP (Team data science process) är en smidig, iterativ data vetenskaps metod för att leverera lösningar för förutsägelse analys och intelligenta program effektivt. TDSP hjälper till att förbättra grupp samarbete och inlärning genom att föreslå hur grupp roller fungerar bäst tillsammans. TDSP innehåller bästa praxis och strukturer från Microsoft och andra bransch ledare för att hjälpa till med lyckad implementering av data vetenskaps initiativ. Målet är att hjälpa företag att dra nytta av sina analysprogram fullt ut.

Den här artikeln innehåller en översikt över TDSP och dess huvud komponenter. Vi tillhandahåller en allmän beskrivning av processen här som kan implementeras med olika typer av verktyg. En mer detaljerad beskrivning av de projekt aktiviteter och roller som ingår i processens livs cykel finns i ytterligare länkade ämnen. Vägledning om hur du implementerar TDSP med en speciell uppsättning Microsoft-verktyg och-infrastruktur som vi använder för att implementera TDSP i våra team tillhandahålls också.

## <a name="key-components-of-the-tdsp"></a>Viktiga komponenter i TDSP

TDSP har följande viktiga komponenter:

- En **livs cykel definition för data vetenskap**
- En **standardiserad projekt struktur**
- **Infrastruktur och resurser** som rekommenderas för data vetenskaps projekt
- **Verktyg och verktyg som** rekommenderas för projekt körning


## <a name="data-science-lifecycle"></a>Data vetenskaps livs cykel

Med TDSP (Team data science process) får du en livs cykel för att strukturera utvecklingen av data vetenskaps projekt. Livs cykeln beskriver alla steg som slutfört projekt.

Om du använder en annan data vetenskaps livs cykel, till exempel [skarpt DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) eller din organisations egen anpassade process, kan du fortfarande använda uppgiftsbaserade TDSP i samband med dessa utvecklings livscykler. På hög nivå har dessa olika metoder mycket gemensamt. 

Den här livs cykeln har utformats för data vetenskaps projekt som levereras som en del av intelligenta program. Dessa program distribuerar Machine Learning-eller artificiell Intelligence-modeller för förutsägelse analys. Undersökande data vetenskaps projekt eller Improvised analys projekt kan också dra nytta av den här processen. Men i sådana fall kanske vissa av stegen som beskrivs inte behövs.    

Livs cykeln beskriver de viktigaste faserna som projekt vanligt vis körs, ofta iterativt:

* **Affärs förståelse**
* **Data hämtning och förståelse**
* **Modellering**
* **Distribution**

Här är en visuell representation av **livs cykeln för team data science process**. 

![Diagrammet visar livs cykeln för data vetenskap, inklusive affärs förståelse, data hämtning/förståelse, modellering och distribution.](./media/overview/tdsp-lifecycle2.png) 

De mål, uppgifter och dokumentations artefakter för varje steg i livs cykeln i TDSP beskrivs i avsnittet om [team data science process Lifecycle](lifecycle.md) . Dessa uppgifter och artefakter är kopplade till projekt roller:

- Lösnings arkitekt
- Projektledare
- Datatekniker
- Dataexpert
- Programutvecklare
- Projektledare 

Följande diagram innehåller en rutnätsvy över de aktiviteter (i blått) och artefakter (i grönt) som är associerade med varje stadium i livs cykeln (på den vågräta axeln) för dessa roller (på den lodräta axeln). 

[![TDSP – roller-och-uppgifter](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Standardiserad projekt struktur

Om du har alla projekt som delar en katalog struktur och använder mallar för projekt dokument blir det enklare för grupp medlemmarna att hitta information om sina projekt. All kod och alla dokument lagras i ett versions kontroll system (VCS) som git, TFS eller Subversion för att ge grupp samarbete. Spårning av aktiviteter och funktioner i ett flexibelt projekt spårnings system, till exempel JIRA, rally och Azure DevOps, gör det möjligt att spåra koden för enskilda funktioner. Sådan spårning gör det också möjligt för team att få bättre kostnads uppskattningar. TDSP rekommenderar att du skapar en separat lagrings plats för varje projekt i VCS för versions hantering, informations säkerhet och samarbete. Den standardiserade strukturen för alla projekt hjälper till att bygga institutionella kunskaper i organisationen.

Vi tillhandahåller mallar för mappstrukturen och de dokument som krävs på standard platserna. Den här mappstrukturen ordnar filerna som innehåller kod för data utforskning och funktions extrahering och den post modellen iterationer. Dessa mallar gör det enklare för grupp medlemmar att förstå arbete som har utförts av andra och lägga till nya medlemmar i team. Det är enkelt att visa och uppdatera dokumentmallar i markdown-format. Använd mallar för att tillhandahålla check listor med viktiga frågor för varje projekt för att se till att problemet är väl definierat och att slut produkten uppfyller den förväntade kvaliteten. Exempel:

- en projekt stadga för att dokumentera affärs problem och projektets omfattning
- data rapporter för att dokumentera strukturen och statistiken för rå data
- modell rapporter för att dokumentera de härledda funktionerna
- modellera prestanda mått som ROC-kurvor eller MSE


[![TDSP – kataloger](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

Katalog strukturen kan klonas från [GitHub](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastruktur och resurser för data vetenskaps projekt  

TDSP ger rekommendationer för att hantera infrastruktur för delad analys och lagring, till exempel:

- moln fil system för lagring av data uppsättningar 
- databaser
- Big data (SQL-eller Spark)-kluster 
- Machine Learning-tjänsten 

Analys-och lagrings infrastrukturen, där obehandlade och bearbetade data uppsättningar lagras, kan finnas i molnet eller lokalt. Den här infrastrukturen möjliggör reproducerbar analys. Dessutom undviks duplicering, vilket kan leda till inkonsekvenser och onödiga infrastruktur kostnader. Det finns verktyg för att etablera delade resurser, spåra dem och tillåta varje grupp medlem att ansluta till dessa resurser på ett säkert sätt. Det är också en bra idé att låta projekt medlemmar skapa en konsekvent beräknings miljö. Olika team medlemmar kan sedan replikera och validera experiment.

Här är ett exempel på ett team som arbetar på flera projekt och delar olika infrastruktur komponenter för moln analys.

[![TDSP-infrastruktur](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Verktyg och verktyg för projekt körning

Det är svårt att introducera processer i de flesta organisationer. Verktyg som tillhandahålls för att implementera data vetenskaps processen och livs cykeln bidrar till att minska barriärerna till och öka konsekvensen av deras införande. TDSP innehåller en första uppsättning verktyg och skript som du kan använda för att komma igång med att använda TDSP i ett team. Du kan också automatisera några av de vanliga uppgifterna i livs cykeln för data vetenskap, till exempel data utforskning och bas linje modellering. Det finns en väldefinierad struktur som ger enskilda personer möjlighet att bidra med delade verktyg och verktyg till sitt Teams delade kod lagrings plats. Dessa resurser kan sedan utnyttjas av andra projekt i teamet eller organisationen.  Microsoft tillhandahåller omfattande verktyg i [Azure Machine Learning](../index.yml) som stöder både öppen källkod (python, R, ONNX och vanliga ramverk för djup inlärning) och även Microsofts egna verktyg (AutoML).


## <a name="next-steps"></a>Nästa steg

[Team data vetenskaps process: roller och uppgifter](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) Beskriver viktiga personal roller och deras associerade uppgifter för ett data vetenskaps team som är standardiserat för den här processen. 
