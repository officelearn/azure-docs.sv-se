---
title: Företag att förstå fas i livscykeln Team datavetenskap Process - Azure | Microsoft Docs
description: Mål, uppgifter och produkterna för företag att förstå steg i datavetenskap projekt
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: c851a9a5c20c3f658e8681a4178ebe67294fd773
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808085"
---
# <a name="business-understanding"></a>Förståelse för verksamheten

Den här artikeln beskrivs mål, uppgifter och leveranser som är associerade med business förstå fas av Team Data vetenskap processen (TDSP). Denna process tillhandahåller en rekommenderad livscykel som du kan använda för att strukturera datavetenskap projekt. Livscykeln beskrivs viktiga steg som projekt vanligtvis köra, ofta upprepade gånger:

   1. **Så här fungerar för företag**
   2. **Datainsamling och förstå**
   3. **Modeling**
   4. **Distribution**
   5. **Kundens godkännande**

Här är en bild av TDSP livscykeln: 

![TDSP livscykel](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Mål
* Ange bestämma viktiga variabler som ska användas som mål för modellen och vars relaterade mått som används i projektet.
* Identifiera de relevanta datakällor som företaget har åtkomst till eller som behöver få.

## <a name="how-to-do-it"></a>Hur du gör det.
Det finns två huvudsakliga uppgifter som beskrivs i det här steget: 

   * **Definiera mål**: arbeta med kunden och andra berörda parter för att förstå och identifiera problem för företag. Formulera frågor som definierar de affärsmål som datavetenskap tekniker som mål.
   * **Identifiera datakällor**: hitta relevanta data som hjälper dig att besvara frågor som definierar målen för projektet.

### <a name="define-objectives"></a>Definiera mål
1. En central syftet med det här steget är att identifiera viktiga variabler som analysen ska förutsäga. Vi refererar till dessa variabler som den *modell mål*, och vi använder de mätvärden som är kopplade till dem för att avgöra hur framgångsrik projektet. Två exempel på sådana mål är försäljningsprognoser eller sannolikheten för en order som falska.

2. Definiera projektet genom att fråga och förfina ”skarpa” frågor som är relevanta, specifika och entydigt. Datavetenskap är en process som använder namn och nummer för att besvara frågor. Mer information om att ställa skarpa frågor finns i [hur du gör datavetenskap](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/) blogg. Du använder vanligtvis datavetenskap eller maskininlärning att besvara fem typer av frågor:
 
   * Hur mycket eller hur många? (regression)
   * Vilken kategori? (klassificering)
   * Vilken grupp? (klustring)
   * Är detta underligt? (avvikelseidentifiering)
   * Vilket alternativ som ska utföras? (rekommendationer)

   Avgöra vilka frågor begär du och hur svarande det ger affärsmålen.

3. Definiera projektgruppen genom att ange de roller och ansvarsområden för dess medlemmar. Utveckla en plan för övergripande milstolpe som du itererar som du hittar mer information. 

4. Definiera framgångsmått. Du kanske vill få en kund omsättning förutsägelse. Du behöver en noggrannhet frekvens av procent ”x” i slutet av det här projektet för tre månader. Med dessa data kan du erbjuda kunden kampanjer för att minska omsättningsuppdateringar. Måtten måste vara **SMART**: 

   * **S**pecifika 
   * **M**easurable
   * **En**chievable 
   * **R**elevant 
   * **T**ime-bunden 

### <a name="identify-data-sources"></a>Identifiera datakällor
Identifiera datakällor som innehåller kända exempel svar på din skarpa frågor. Leta efter följande uppgifter:

* Data som är relevanta för frågan. Har du mått för målet och funktioner som är relaterade till målet?
* Data som är ett korrekt mått på mål-modellen och -funktioner av intresse.

Till exempel kanske du upptäcker att de befintliga system behöver samla in och logga ytterligare typer av data för att lösa problemet och uppnå projektmål. I det här fallet kanske du vill leta efter externa datakällor eller uppdatera dina system för att samla in nya data.

## <a name="artifacts"></a>Artefakter
Här följer resultat i det här steget:

   * [Auktoriserad dokumentet](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): en standardmall tillhandahålls i TDSP projektet struktur definitionen. Auktoriserad dokumentet är en levande. Du kan uppdatera mallen i projektet som du gör nya identifieringar och som företagets behov förändras. Nyckeln är att iterera när det här dokumentet, lägga till fler detaljer slutföra identifieringsprocessen. Behåll kunden och andra berörda parter som är involverad i att göra ändringarna och tydligt kommunicera skälen för att ändringarna ska dem.  
   * [Datakällor](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): den **rådata källor** avsnitt i den **datadefinitioner** rapporten som finns i projektet TDSP **Data rapporten** mappen innehåller data källor. Det här avsnittet anger de ursprungliga- och målplatserna för rådata. I senare steg kan du fylla i ytterligare information som skript för att flytta data till din analytiska miljö.  
   * [Data ordlistor](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): det här dokumentet innehåller beskrivningar av de data som tillhandahålls av klienten. Dessa beskrivningar innehåller information om schemat (datatyper och information om valideringsregler, om sådana finns) och entitet relations-diagram, om det är tillgängligt.

## <a name="next-steps"></a>Nästa steg

Här är länkar till varje steg i livscykeln för TDSP:

   1. [Så här fungerar för företag](lifecycle-business-understanding.md)
   2. [Datainsamling och förstå](lifecycle-data.md)
   3. [Modeling](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundens godkännande](lifecycle-acceptance.md)

Vi ger fullständig slutpunkt till slutpunkt genomgång som visar alla steg i processen för specifika scenarier. Den [exempel genomgång](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genomgångar illustrerar hur du kombinerar moln, lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program. 

Exempel på hur du utför stegen i TDSPs som använder Azure Machine Learning Studio finns [använder TDSP med Azure Machine Learning](http://aka.ms/datascienceprocess).
