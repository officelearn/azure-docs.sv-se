---
title: Distributionsstadium för Team Data Science Process-livscykel – Azure | Microsoft Docs
description: Mål, uppgifter och slutprodukter för distribution steg i dina data science-projekt
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
ms.openlocfilehash: af53b876eff401c7ee3bb0fa52e3fc30ff721afc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232827"
---
# <a name="deployment"></a>Distribution

Den här artikeln beskrivs de mål, uppgifter och slutprodukter som är kopplad till distributionen av Team Data Science Process (TDSP). Den här processen ger en rekommenderad livscykel som du kan använda för att strukturera dina data science-projekt. Livscykeln beskrivs de viktigaste stegen som projekt vanligtvis köra, ofta upprepade gånger:

   1. **Förståelse för verksamheten**
   2. **Data förvärv och förståelse av**
   3. **Modellering**
   4. **Distribution**
   5. **Kundgodkännande**

Här är en visuell representation av TDSP-livscykeln: 

![Livscykel för TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Mål
Distribuera modeller med en datapipeline till produktions- eller produktionsliknande miljö för godkännande av slutanvändaren. 

## <a name="how-to-do-it"></a>Gör så
Den huvudsakliga uppgift som beskrivs i det här steget:

**Operationalisera modellen**: Distribuera modellen och pipeline till produktions- eller produktionsliknande miljö för användning av programmet.

### <a name="operationalize-a-model"></a>Operationalisera en modell
När du har en uppsättning modeller som gör kan operationalisera du dem för andra program kan använda. Beroende på företagets krav görs förutsägelser i realtid eller på basis av batch. För att distribuera modeller måste du lägga upp dem med ett öppet API-gränssnitt. Gränssnittet gör det möjligt för modellen för att enkelt förbrukning från olika program, till exempel:

   * Online-webbplatser
   * Kalkylblad 
   * Instrumentpaneler
   * Line-of-business-program 
   * Backend-program 

Exempel på modellen driftsättning i en Azure Machine Learning-webbtjänst finns [distribuera en Azure Machine Learning-webbtjänst](../studio/publish-a-machine-learning-web-service.md). Det är en bra idé att skapa telemetri och övervakningen i produktion-modellen och datapipeline som du distribuerar. Den här metoden hjälper med efterföljande systemstatus reporting och felsökning.  

## <a name="artifacts"></a>Artefakter

* En status-instrumentpanel som visar systemmått hälso- och nyckel
* En slutlig modellering rapport med distributionsinformation
* Ett dokument för slutlig lösning-arkitektur


## <a name="next-steps"></a>Nästa steg

Här finns länkar till varje steg i livscykeln för TDSP:

   1. [Förståelse för verksamheten](lifecycle-business-understanding.md)
   2. [Data förvärv och förståelse av](lifecycle-data.md)
   3. [Modellering](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundgodkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständig från slutpunkt till slutpunkt genomgång som visar alla steg i processen för specifika scenarier. Den [exempel genomgångar](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genomgångar visar hur du kombinerar molnlösningar, lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program. 

Exempel på hur du utför stegen i TDSPs som använder Azure Machine Learning Studio finns [använder TDSP med Azure Machine Learning](https://aka.ms/datascienceprocess).
