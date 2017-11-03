---
title: "Företag att förstå steget i teamet datavetenskap processen livscykel – Azure | Microsoft Docs"
description: "Mål, uppgifter och produkterna för företag att förstå steg i dina datavetenskap projekt."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: 2adce61f8185bd86a6a870bb5752fe936701b0af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="business-understanding"></a>Förståelse för verksamheten

Det här avsnittet beskriver mål, uppgifter, och leveranser som är associerade med den **företag att förstå steget** processens Team datavetenskap. Denna process tillhandahåller en rekommenderad livscykel som du kan använda för att strukturera datavetenskap projekt. Livscykeln beskrivs viktiga steg som projekt vanligtvis köra, ofta upprepade gånger:

* **Så här fungerar för företag**
* **Datainsamling och förstå**
* **Modeling**
* **Distribution**
* **Kundens godkännande**

Här är en bild av den **Team datavetenskap Process livscykel**. 

![TDSP Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Mål
* Den **nyckeln variabler** är angett som ska fungera som den **modell mål** och vars relaterade mått används avgöra hur framgångsrik för projektet.
* Relevant **datakällor** identifieras att företaget har åtkomst till eller behöver få.

## <a name="how-to-do-it"></a>Hur du gör det.
Det finns två huvudsakliga uppgifter som beskrivs i det här steget: 

* **Definiera mål**: arbeta med kunden och andra berörda parter för att förstå och identifiera problem för företag. Formulera frågor som definierar affärsmål och datavetenskap tekniker som mål.
* **Identifiera datakällor**: hitta relevanta data som hjälper dig att besvara frågor som definierar målen för projektet.

### <a name="11-define-objectives"></a>1.1 definiera mål

1. En central syftet med det här steget är att identifiera nyckeln som **business variabler** som analysen ska förutsäga. Dessa variabler kallas den **modell mål** och mått som är kopplade till dem som används för att avgöra hur framgångsrik projektet. Två exempel på sådana mål är försäljnings- eller sannolikheten för en order som falska.

2. Definiera den **projektet mål** genom att fråga och förfina ”skarpa” frågor som är relevanta och specifika och entydigt. Datavetenskap är en process för svar på frågor med hjälp av namn och nummer. Mer information om att ställa frågor skarpa finns [hur du gör datavetenskap](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/) blogg. Datavetenskap / maskininlärning normalt används för att besvara fem typer av frågor:
 
   * Hur mycket eller hur många? (regression)
   * Vilken kategori? (klassificering)
   * Vilken grupp? (klustring)
   * Är detta underligt? (avvikelseidentifiering)
   * Vilket alternativ som ska utföras? (rekommendationer)

    Avgöra vilka frågor du frågar och hur svarande det ger affärsmålen.

3. Definiera den **projektet team** genom att ange de roller och ansvarsområden för dess medlemmar. Utveckla en plan för övergripande milstolpe som du itererar som identifieras mer information.  

4. **Definiera framgångsmått**. Till exempel: uppnå kunden omsättning prognosens noggrannhet kan förbättras av X % i slutet av det här 3-månaders-projektet så att vi kan erbjuda kampanjer för att minska omsättning. Måtten måste vara **SMART**: 
   * **S**pecifika 
   * **M**easurable
   * **En**chievable 
   * **R**elevant 
   * **T**ime-bunden 

### <a name="12-identify-data-sources"></a>1.2 identifiera datakällor
Identifiera datakällor som innehåller kända exempel svar på din skarpa frågor. Leta efter följande uppgifter:

* Data som är **de** på frågan. Har vi mått i mål- och funktioner som är relaterade till målet?
* Data som är en **korrekt mått** av vår modell mål och -funktioner av intresse.

Det är exempelvis inte ovanligt, att hitta att befintliga system behöver samla in och logga ytterligare typer av data för att lösa problemet och uppnå projektmål. I det här fallet kanske du vill leta efter externa datakällor eller uppdatera dina system för att samla in nya data.

## <a name="artifacts"></a>Artefakter
Här följer resultat i det här steget:

* [**Befrakta dokumentet**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): en standardmall tillhandahålls i TDSP projektet struktur definitionen. Detta är en levande dokument som har uppdaterats i projektet som nya identifieringar görs och som företagets behov förändras. Nyckeln är att iterera när det här dokumentet, lägga till fler detaljer slutföra identifieringsprocessen. Behåll kunden och andra berörda parter som är involverad i att göra ändringarna och tydligt kommunicera skälen för att ändringarna ska dem.  
* [**Datakällor**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources): Detta är den **Raw datakällor** avsnitt i den **datadefinitioner** rapporten som finns i projektet TDSP **Data rapporten** mapp. Det anger de ursprungliga- och målplatserna för rådata. I senare steg kan du fylla i ytterligare information som skript för att flytta data till din analytiska miljö.  
* [**Data ordlistor**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/DataDictionaries): det här dokumentet innehåller beskrivningar av de data som tillhandahålls av klienten. Dessa beskrivningar innehåller information om schemat (datatyper, information om valideringsregler, om sådana finns) och entitet relationen diagram om det är tillgängligt.

## <a name="next-steps"></a>Nästa steg

Här är länkar till varje steg i livscykeln för Team av vetenskapliga data:

* [1. Så här fungerar för företag](lifecycle-business-understanding.md)
* [2. Datainsamling och förstå](lifecycle-data.md)
* [3. Modeling](lifecycle-modeling.md)
* [4. Distribution](lifecycle-deployment.md)
* [5. Kundens godkännande](lifecycle-acceptance.md)

Fullständig genomgång för slutpunkt-till-slutpunkt som visar alla steg i processen för **specifika scenarier** tillhandahålls också. De anges och är kopplad till miniatyr beskrivningar i den [exempel genomgång](walkthroughs.md) avsnittet. De visar hur du kombinerar moln, lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program. 

Exempel körs steg i Team av vetenskapliga data som använder Azure Machine Learning Studio finns i [med Azure ML](http://aka.ms/datascienceprocess) utbildningsvägar.