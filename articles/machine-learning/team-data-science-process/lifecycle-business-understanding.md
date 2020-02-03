---
title: Förståelse för verksamheten i Team Data Science Process
description: Mål, uppgifter och slutprodukter för företag förstå steg i dina data science-projekt i Team Data Science Process.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710328"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>Företag förstå steg i livscykeln för Team Data Science Process

Den här artikeln beskrivs de mål, uppgifter och slutprodukter som är associerade med företag förstå scenen för Team Data Science Process (TDSP). Den här processen ger en rekommenderad livscykel som du kan använda för att strukturera dina data science-projekt. Livscykeln beskrivs de viktigaste stegen som projekt vanligtvis köra, ofta upprepade gånger:

   1. **Affärs förståelse**
   2. **Data hämtning och förståelse**
   3. **Form**
   4. **Distribution**
   5. **Kund godkännande**

Här är en visuell representation av TDSP-livscykeln: 

![Livscykel för TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Mål
* Ange variablerna nyckel som är som fungerar som mål för modellen och vars relaterade mått som används avgöra projektet.
* Identifiera de relevanta datakällor som företaget har åtkomst till eller behöver få.

## <a name="how-to-do-it"></a>Gör så
Det finns två huvudsakliga uppgifter som beskrivs i det här steget: 

   * **Definiera mål**: arbeta med din kund och andra intressenter för att förstå och identifiera affärs problem. Formulera frågor som definierar de affärsmål som data science-tekniker kan riktas mot.
   * **Identifiera data källor**: hitta relevanta data som hjälper dig att besvara frågorna som definierar projektets mål.

### <a name="define-objectives"></a>Definiera mål
1. En central syftet med det här steget är att identifiera viktiga variablerna som analysen ska förutsäga. Vi refererar till dessa variabler som *modell mål*, och vi använder de mått som är kopplade till dem för att fastställa projektets framgångar. Två exempel på sådana mål är försäljningsprognoser eller sannolikheten för en order som falska.

2. Definiera projektmål genom att ställa och förfina ”tydliga” frågor som är relevanta, specifika och entydiga. Datavetenskap är en process som använder namn och nummer för att besvara frågor. Du använder vanligtvis data science eller maskininlärning att besvara fem typer av frågor:
 
   * Hur mycket eller hur många? (regression)
   * Vilken kategori? (klassificering)
   * Vilken grupp? (klustring)
   * Är detta underligt? (avvikelseidentifiering)
   * Vilket alternativ som ska utföras? (rekommendation)

   Fastställa vilka av dessa frågor begär du och hur svarande det ger dina affärsmål.

3. Definiera projektgruppen genom att ange de roller och ansvarsområden dess medlemmar. Utveckla en övergripande milstolpe plan som du itererar som du får mer information. 

4. Definiera mått för framgång. Du kanske exempelvis vill uppnå en prognostiserad kundomsättning. Du behöver en precision som andelen procent ”x” i slutet av det här projektet för tre månader. Med dessa data kan du erbjuda kunden kampanjer att minska omsättning. Måtten måste vara **smarta**: 

   * **S**pecifika 
   * **M**easurable
   * **En**chievable 
   * **R**-Elevant 
   * **T**IME-kopplad 

### <a name="identify-data-sources"></a>Identifiera datakällor
Identifiera de datakällor som innehåller kända exempel på svar på dina sharp frågor. Sök efter följande data:

* Data som är relevant för frågan. Har du mått för målet och funktioner som är relaterade till målet?
* Data som är ett korrekt mått på mål-modellen och funktioner i närheten.

Till exempel kanske du upptäcker att de befintliga system måste samla in och logga fler typer av data för att lösa problemet och uppnå projektmål. I så fall kanske du vill leta efter externa datakällor eller uppdatera dina system för att samla in nya data.

## <a name="artifacts"></a>Artefakter
Här följer slutprodukterna i det här steget:

   * [Charter dokument](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): en standardmall finns i TDSP-projektets struktur definition. Auktoriserad dokumentet är en levande. Du kan uppdatera mallen under hela projektet som du gör nya identifieringar och som företagets behov förändras. Nyckeln är att iterera på det här dokumentet, att lägga till mer information, slutföra identifieringsprocessen. Låt kunden och andra berörda parter som ingår i att göra ändringarna och tydligt kommunicera skäl för att ändringarna ska dem.  
   * [Data källor](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): avsnittet **rå data källor** i rapporten **data definitioner** som finns i mappen TDSP Project **data Report** innehåller data källorna. Det här avsnittet anger de ursprungliga och de nya platserna efter rådata. I senare steg kan du fylla i ytterligare information som skripten för att flytta data till din analytiska miljö.  
   * [Data ord listor](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): det här dokumentet innehåller beskrivningar av de data som tillhandahålls av klienten. Dessa beskrivningar innehåller information om schemat (datatyper och information om valideringsregler eventuellt) och entiteten-relation diagrammen, om det är tillgängligt.

## <a name="next-steps"></a>Nästa steg

Här finns länkar till varje steg i livscykeln för TDSP:

   1. [Affärs förståelse](lifecycle-business-understanding.md)
   2. [Data hämtning och förståelse](lifecycle-data.md)
   3. [Form](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kund godkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständiga genom gångar som demonstrerar alla steg i processen för olika scenarier. [Exempel](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genomgångar visar hur du kombinerar molnlösningar, lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program. 
