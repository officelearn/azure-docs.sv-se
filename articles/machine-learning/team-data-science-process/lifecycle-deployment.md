---
title: Distribution fas i livscykeln Team datavetenskap Process - Azure | Microsoft Docs
description: Mål, uppgifter och leveranser för distribution av steg i datavetenskap projekt
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
ms.openlocfilehash: 9c54e93eca181331117f2f7faad3e7d07274412d
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837039"
---
# <a name="deployment"></a>Distribution

Den här artikeln beskrivs mål, uppgifter och leveranser som är associerade med distribution av Team Data vetenskap processen (TDSP). Denna process tillhandahåller en rekommenderad livscykel som du kan använda för att strukturera datavetenskap projekt. Livscykeln beskrivs viktiga steg som projekt vanligtvis köra, ofta upprepade gånger:

   1. **Så här fungerar för företag**
   2. **Datainsamling och förstå**
   3. **Modeling**
   4. **Distribution**
   5. **Kundens godkännande**

Här är en bild av TDSP livscykeln: 

![TDSP livscykel](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Mål
Distribuera modeller med en rörledning för data till en produktionsmiljö eller produktions-liknande miljö för godkännande av slutanvändaren. 

## <a name="how-to-do-it"></a>Hur du gör det.
Den huvudsakliga uppgift som beskrivs i det här steget:

**Operationalisera modellen**: Distribuera modellen och pipeline i eller tillverkning eller produktions-liknande miljö för användning av programmet.

### <a name="operationalize-a-model"></a>Operationalisera en modell
När du har en uppsättning modeller som utför väl operationalisera du dem för andra program för att använda. Beroende på företagets krav görs förutsägelser i realtid eller på grundval av batch. Om du vill distribuera modeller exponera du dem med ett öppet API-gränssnitt. Modellen för att enkelt förbrukas från olika program som gör att gränssnittet:

   * Online webbplatser
   * Kalkylblad 
   * Instrumentpaneler
   * Line-of-business-program 
   * Backend-program 

Exempel på modellen operationalization med en Azure Machine Learning-webbtjänst finns [distribuera en Azure Machine Learning-webbtjänst](../studio/publish-a-machine-learning-web-service.md). Det är en bra idé att skapa telemetri och övervakningen i modellen för produktion och pipeline data som du distribuerar. Det hjälper med efterföljande systemstatus rapportering och felsökning.  

## <a name="artifacts"></a>Artefakter

* En status instrumentpanel som visar system hälso- och mått
* En slutlig modellering rapport med detaljer distribution
* Ett dokument för slutlig lösning-arkitektur


## <a name="next-steps"></a>Nästa steg

Här är länkar till varje steg i livscykeln för TDSP:

   1. [Så här fungerar för företag](lifecycle-business-understanding.md)
   2. [Datainsamling och förstå](lifecycle-data.md)
   3. [Modeling](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundens godkännande](lifecycle-acceptance.md)

Vi ger fullständig slutpunkt till slutpunkt genomgång som visar alla steg i processen för specifika scenarier. Den [exempel genomgång](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genomgångar illustrerar hur du kombinerar moln, lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program. 

Exempel på hur du utför stegen i TDSPs som använder Azure Machine Learning Studio finns [använder TDSP med Azure Machine Learning](http://aka.ms/datascienceprocess).
