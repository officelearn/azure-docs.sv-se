---
title: Företag förstå steget i Team Data Science Process-livscykel – Azure | Microsoft Docs
description: Mål, uppgifter och slutprodukter för företag förstå steg i dina data science-projekt
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 00dc359fc171afe470b1dfe8f38d0951085ec580
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228308"
---
# <a name="business-understanding"></a>Förståelse för verksamheten

Den här artikeln beskrivs de mål, uppgifter och slutprodukter som är associerade med företag förstå scenen för Team Data Science Process (TDSP). Den här processen ger en rekommenderad livscykel som du kan använda för att strukturera dina data science-projekt. Livscykeln beskrivs de viktigaste stegen som projekt vanligtvis köra, ofta upprepade gånger:

   1. **Förståelse för verksamheten**
   2. **Data förvärv och förståelse av**
   3. **Modellering**
   4. **Distribution**
   5. **Kundgodkännande**

Här är en visuell representation av TDSP-livscykeln: 

![Livscykel för TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Mål
* Ange variablerna nyckel som är som fungerar som mål för modellen och vars relaterade mått som används avgöra projektet.
* Identifiera de relevanta datakällor som företaget har åtkomst till eller behöver få.

## <a name="how-to-do-it"></a>Gör så
Det finns två huvudsakliga uppgifter som beskrivs i det här steget: 

   * **Definiera mål**: arbeta med dina kunder och andra berörda parter för att förstå och identifiera problem för företag. Formulera frågor som definierar de affärsmål som data science-tekniker kan riktas mot.
   * **Identifiera datakällor**: hitta relevanta data som hjälper dig att besvara frågor som definierar målen för projektet.

### <a name="define-objectives"></a>Definiera mål
1. En central syftet med det här steget är att identifiera viktiga variablerna som analysen ska förutsäga. Vi refererar till dessa variabler som den *modellera mål*, och vi använder de mått som är kopplade till dem för att avgöra projektet. Två exempel på sådana mål är försäljningsprognoser eller sannolikheten för en order som falska.

2. Definiera projektmål genom att ställa och förfina ”tydliga” frågor som är relevanta, specifika och entydiga. Datavetenskap är en process som använder namn och nummer för att besvara frågor. Mer information om att ställa sharp frågor finns i den [hur du gör datavetenskap](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/) blogg. Du använder vanligtvis data science eller maskininlärning att besvara fem typer av frågor:
 
   * Hur mycket eller hur många? (regression)
   * Vilken kategori? (klassificering)
   * Vilken grupp? (klustring)
   * Är detta underligt? (avvikelseidentifiering)
   * Vilket alternativ som ska utföras? (rekommendation)

   Fastställa vilka av dessa frågor begär du och hur svarande det ger dina affärsmål.

3. Definiera projektgruppen genom att ange de roller och ansvarsområden dess medlemmar. Utveckla en övergripande milstolpe plan som du itererar som du får mer information. 

4. Definiera mått för framgång. Du kanske exempelvis vill uppnå en prognostiserad kundomsättning. Du behöver en precision som andelen procent ”x” i slutet av det här projektet för tre månader. Med dessa data kan du erbjuda kunden kampanjer att minska omsättning. Måtten måste vara **SMART**: 

   * **S**pecifika 
   * **M**easurable
   * **En**chievable 
   * **R**elevant 
   * **T**ime-bunden 

### <a name="identify-data-sources"></a>Identifiera datakällor
Identifiera de datakällor som innehåller kända exempel på svar på dina sharp frågor. Sök efter följande data:

* Data som är relevant för frågan. Har du mått för målet och funktioner som är relaterade till målet?
* Data som är ett korrekt mått på mål-modellen och funktioner i närheten.

Till exempel kanske du upptäcker att de befintliga system måste samla in och logga fler typer av data för att lösa problemet och uppnå projektmål. I så fall kanske du vill leta efter externa datakällor eller uppdatera dina system för att samla in nya data.

## <a name="artifacts"></a>Artefakter
Här följer slutprodukterna i det här steget:

   * [Auktoriserad dokumentet](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): en standardmall har angetts i strukturdefinition för TDSP-projekt. Auktoriserad dokumentet är en levande. Du kan uppdatera mallen under hela projektet som du gör nya identifieringar och som företagets behov förändras. Nyckeln är att iterera på det här dokumentet, att lägga till mer information, slutföra identifieringsprocessen. Låt kunden och andra berörda parter som ingår i att göra ändringarna och tydligt kommunicera skäl för att ändringarna ska dem.  
   * [Datakällor](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): den **rådata källor** delen av den **datadefinitioner** rapporten som finns i TDSP-projekt **Data rapporten** mappen innehåller data källor. Det här avsnittet anger de ursprungliga och de nya platserna efter rådata. I senare steg kan du fylla i ytterligare information som skripten för att flytta data till din analytiska miljö.  
   * [Data ordlistor](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): det här dokumentet innehåller beskrivningar av vilken information som tillhandahålls av klienten. Dessa beskrivningar innehåller information om schemat (datatyper och information om valideringsregler eventuellt) och entiteten-relation diagrammen, om det är tillgängligt.

## <a name="next-steps"></a>Nästa steg

Här finns länkar till varje steg i livscykeln för TDSP:

   1. [Förståelse för verksamheten](lifecycle-business-understanding.md)
   2. [Data förvärv och förståelse av](lifecycle-data.md)
   3. [Modellering](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundgodkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständig från slutpunkt till slutpunkt genomgång som visar alla steg i processen för specifika scenarier. Den [exempel genomgångar](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genomgångar visar hur du kombinerar molnlösningar, lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program. 

Exempel på hur du utför stegen i TDSPs som använder Azure Machine Learning Studio finns [använder TDSP med Azure Machine Learning](https://aka.ms/datascienceprocess).
