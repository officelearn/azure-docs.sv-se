---
title: "Steg för distribution av Team datavetenskap processen livscykel – Azure | Microsoft Docs"
description: "Mål, uppgifter och leveranser för distribution av steg i dina datavetenskap projekt."
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
ms.openlocfilehash: b49b3ab696c22928c5f5a4566e059345fd810588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deployment"></a>Distribution

Det här avsnittet beskriver mål, uppgifter, och leveranser som är associerade med den **distribution** processens Team datavetenskap. Denna process tillhandahåller en rekommenderad livscykel som du kan använda för att strukturera datavetenskap projekt. Livscykeln beskrivs viktiga steg som projekt vanligtvis köra, ofta upprepade gånger:

* **Så här fungerar för företag**
* **Datainsamling och förstå**
* **Modeling**
* **Distribution**
* **Kundens godkännande**

Här är en bild av den **Team datavetenskap Process livscykel**. 

![TDSP Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Målet
* Modeller med en rörledning för data har distribuerats till en produktionsmiljö eller produktions-liknande miljö för godkännande av slutanvändaren. 

## <a name="how-to-do-it"></a>Hur du gör det.
Den huvudsakliga uppgift som beskrivs i det här steget:

* **Operationalisera modellen**: Distribuera modellen och pipeline i eller tillverkning eller produktions-liknande miljö för användning av programmet.

### <a name="41-operationalize-a-model"></a>4.1 operationalisera en modell
När du har en uppsättning modeller som utför också kan du operationalized dem för andra program att använda. Beroende på företagets krav görs förutsägelser i realtid eller på grundval av batch. Modeller har distribuerats genom att exponera dem med ett öppet API-gränssnitt. Gränssnittet gör att förbrukas enkelt från olika program, till exempel online webbplatser, kalkylblad, instrumentpaneler och affärs- och backend-program för modellen. Exempel på modellen operationalization med en Azure Machine Learning-webbtjänst finns [distribuera en Azure Machine Learning-webbtjänst](../studio/publish-a-machine-learning-web-service.md). Det är också en bra idé att skapa telemetri och övervakningen i modellen för produktion och pipeline data som har distribuerats. Det hjälper med efterföljande systemstatus rapportering och felsökning.  

## <a name="artifacts"></a>Artefakter
* Status för instrumentpanelen i systemet hälsa och nyckeln mått.
* Sista modellering rapporten med detaljer distribution.
* Slutlig lösning arkitektur dokument.


## <a name="next-steps"></a>Nästa steg

Här är länkar till varje steg i livscykeln för Team av vetenskapliga data:

* [1. Så här fungerar för företag](lifecycle-business-understanding.md)
* [2. Datainsamling och förstå](lifecycle-data.md)
* [3. Modeling](lifecycle-modeling.md)
* [4. Distribution](lifecycle-deployment.md)
* [5. Kundens godkännande](lifecycle-acceptance.md)

Fullständig genomgång för slutpunkt-till-slutpunkt som visar alla steg i processen för **specifika scenarier** tillhandahålls också. De anges och är kopplad till miniatyr beskrivningar i den [exempel genomgång](walkthroughs.md) avsnittet. De visar hur du kombinerar moln, lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program. 

Exempel körs steg i Team av vetenskapliga data som använder Azure Machine Learning Studio finns i [med Azure ML](http://aka.ms/datascienceprocess) utbildningsvägar.