---
title: Affärssyssning i Team Data Science Process
description: Mål, uppgifter och resultat för affärsförståelsestadiet för dina datavetenskapliga projekt i Team Data Science Process.
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
ms.openlocfilehash: a7aaed519f8f97a9be77a263568aeed5257c16d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76710328"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>Affärsöverensningsfasen av teamdatavetenskapsprocessens livscykel

I den här artikeln beskrivs de mål, uppgifter och resultat som är associerade med affärsförståelsestadiet i Team Data Science Process (TDSP). Den här processen innehåller en rekommenderad livscykel som du kan använda för att strukturera dina datavetenskapsprojekt. Livscykeln beskriver de stora faserna som projekt vanligtvis körs, ofta iterativt:

   1. **Förståelse för verksamheten**
   2. **Förvärv och förståelse av data**
   3. **Modellering**
   4. **Distribution**
   5. **Kundgodkännande**

Här är en visuell representation av TDSP livscykel: 

![TDSP-livscykel](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Mål
* Ange de nyckelvariabler som ska fungera som modellmål och vars relaterade mått används avgör projektets framgång.
* Identifiera relevanta datakällor som företaget har åtkomst till eller behöver hämta.

## <a name="how-to-do-it"></a>Hur man gör det
Det finns två huvuduppgifter som tas upp i detta skede: 

   * **Definiera mål**: Arbeta med din kund och andra intressenter för att förstå och identifiera affärsproblemen. Formulera frågor som definierar de affärsmål som datavetenskapsteknikerna kan inrikta sig på.
   * **Identifiera datakällor**: Hitta relevanta data som hjälper dig att svara på de frågor som definierar projektets mål.

### <a name="define-objectives"></a>Definiera mål
1. Ett centralt mål för detta steg är att identifiera de viktigaste affärsvariabler som analysen behöver förutsäga. Vi refererar till dessa variabler som *modellmål*och vi använder de mått som är associerade med dem för att avgöra projektets framgång. Två exempel på sådana mål är försäljningsprognoser eller sannolikheten för att en order ska vara bedräglig.

2. Definiera projektmålen genom att ställa och förfina "skarpa" frågor som är relevanta, specifika och otvetydiga. Datavetenskap är en process som använder namn och siffror för att svara på sådana frågor. Du använder vanligtvis datavetenskap eller maskininlärning för att svara på fem typer av frågor:
 
   * Hur mycket eller hur många? (regression)
   * Vilken kategori? (klassificering)
   * Vilken grupp? (klustring)
   * Är det här konstigt? (avvikelseidentifiering)
   * Vilket alternativ bör tas? (rekommendation)

   Bestäm vilka av dessa frågor du ställer och hur du svarar på den uppnår dina affärsmål.

3. Definiera projektgruppen genom att ange medlemmarnas roller och ansvarsområden. Utveckla en milstolpeplan på hög nivå som du itererar på när du upptäcker mer information. 

4. Definiera framgångsmåtten. Du kanske till exempel vill uppnå en förutsägelse om kundomsättning. Du behöver en noggrannhetsfrekvens på "x" procent i slutet av detta tremånadersprojekt. Med dessa data kan du erbjuda kundkampanjer för att minska omsättningen. Måtten måste vara **SMART:** 

   * **S**pecific 
   * **M**easurable
   * **En**chievable 
   * **R**elevant 
   * **T**ime-bundna 

### <a name="identify-data-sources"></a>Identifiera datakällor
Identifiera datakällor som innehåller kända exempel på svar på dina skarpa frågor. Leta efter följande data:

* Data som är relevanta för frågan. Har du mått på målet och funktioner som är relaterade till målet?
* Data som är ett korrekt mått på modellmålet och intressanta funktioner.

Du kanske till exempel upptäcker att de befintliga systemen måste samla in och logga in ytterligare typer av data för att lösa problemet och uppnå projektmålen. I det här fallet kanske du vill leta efter externa datakällor eller uppdatera dina system för att samla in nya data.

## <a name="artifacts"></a>Artefakter
Här är slutprodukterna i detta skede:

   * [Charterdokument](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): En standardmall finns i TDSP-projektstrukturdefinitionen. Stadgedokumentet är ett levande dokument. Du uppdaterar mallen i hela projektet när du gör nya upptäckter och när affärskraven ändras. Nyckeln är att iterera på det här dokumentet, lägga till mer information, som du framsteg genom identifieringsprocessen. Håll kunden och andra intressenter involverade i att göra ändringarna och tydligt kommunicera orsakerna till ändringarna till dem.  
   * [Datakällor](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): Avsnittet **Rådatakällor** i rapporten **Datadefinitioner** som finns i mappen **Datarapport** för TDSP-projekt innehåller datakällorna. Det här avsnittet anger ursprungliga platser och målplatser för rådata. I senare steg fyller du i ytterligare information som skripten för att flytta data till din analytiska miljö.  
   * [Dataordlistor](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): Det här dokumentet innehåller beskrivningar av de data som tillhandahålls av klienten. Dessa beskrivningar innehåller information om schemat (datatyper och information om eventuella valideringsregler) och entitetsrelationsdiagram, om sådana finns tillgängliga.

## <a name="next-steps"></a>Nästa steg

Här är länkar till varje steg i livscykeln för TDSP:

   1. [Förståelse för verksamheten](lifecycle-business-understanding.md)
   2. [Förvärv och förståelse av data](lifecycle-data.md)
   3. [Modellering](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundgodkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständiga genomgångar som visar alla steg i processen för specifika scenarier. I artikeln [Exempelgenomgångar](walkthroughs.md) finns en lista över scenarier med länkar och miniatyrbeskrivningar. Genomgångarna illustrerar hur du kombinerar moln, lokala verktyg och tjänster till ett arbetsflöde eller en pipeline för att skapa ett intelligent program. 
