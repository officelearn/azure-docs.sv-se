---
title: Driftsättningsfasen för livscykeln för teamdatavetenskapsprocessen
description: Mål, uppgifter och slutprodukter för distributionsfasen av dina datavetenskapliga projekt
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
ms.openlocfilehash: 1138c95274c769186a9a29aa4d35517e378baeae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720494"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Driftsättningsfasen för livscykeln för teamdatavetenskapsprocessen

I den här artikeln beskrivs de mål, uppgifter och slutprodukter som är associerade med distributionen av Team Data Science Process (TDSP). Den här processen innehåller en rekommenderad livscykel som du kan använda för att strukturera dina datavetenskapsprojekt. Livscykeln beskriver de stora faserna som projekt vanligtvis körs, ofta iterativt:

   1. **Förståelse för verksamheten**
   2. **Förvärv och förståelse av data**
   3. **Modellering**
   4. **Distribution**
   5. **Kundgodkännande**

Här är en visuell representation av TDSP livscykel: 

![TDSP-livscykel](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Mål
Distribuera modeller med en datapipeline till en produktions- eller produktionsliknande miljö för slutanvändarens godkännande. 

## <a name="how-to-do-it"></a>Hur man gör det
Den huvudsakliga uppgift som tas upp i detta skede:

**Operationalize modellen**: Distribuera modellen och pipelinen till en produktions- eller produktionsliknande miljö för programförbrukning.

### <a name="operationalize-a-model"></a>Operationalisera en modell
När du har en uppsättning modeller som fungerar bra kan du operationalisera dem för andra program att använda. Beroende på affärskraven görs förutsägelser antingen i realtid eller på batchbasis. Om du vill distribuera modeller exponerar du dem med ett öppet API-gränssnitt. Gränssnittet gör det möjligt att enkelt förbruka modellen från olika program, till exempel:

   * Online webbplatser
   * Kalkylblad 
   * Instrumentpaneler
   * Affärsprogram 
   * Backend-program 

Exempel på modell operationalization med en Azure Machine Learning-webbtjänst finns i [Distribuera en Azure Machine Learning-webbtjänst](../studio/deploy-a-machine-learning-web-service.md). Det är en bra idé att skapa telemetri och övervakning till produktionsmodellen och datapipelinen som du distribuerar. Den här metoden hjälper till med efterföljande systemstatusrapportering och felsökning.  

## <a name="artifacts"></a>Artefakter

* En statusinstrumentpanel som visar systemets hälso- och nyckelmått
* En slutlig modelleringsrapport med distributionsinformation
* Ett arkitekturdokument för slutlig lösning


## <a name="next-steps"></a>Nästa steg

Här är länkar till varje steg i livscykeln för TDSP:

   1. [Förståelse för verksamheten](lifecycle-business-understanding.md)
   2. [Datainsamling och förståelse](lifecycle-data.md)
   3. [Modellering](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundgodkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständiga genomgångar som visar alla steg i processen för specifika scenarier. I artikeln [Exempelgenomgångar](walkthroughs.md) finns en lista över scenarier med länkar och miniatyrbeskrivningar. Genomgångarna illustrerar hur du kombinerar moln, lokala verktyg och tjänster till ett arbetsflöde eller en pipeline för att skapa ett intelligent program. 

Exempel på hur du kör steg i TDSPs som använder Azure Machine Learning Studio finns i [Använda TDSP med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
