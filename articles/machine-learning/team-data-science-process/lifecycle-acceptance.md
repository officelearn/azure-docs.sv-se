---
title: Kundens godkännande steget i Team Data Science Process-livscykel – Azure | Microsoft Docs
description: Mål, uppgifter och mål för kundens godkännande steg i dina data science-projekt
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
ms.openlocfilehash: 9dd3ab8c9451ecfe6b095b52f5af083a7c7e9550
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232767"
---
# <a name="customer-acceptance"></a>Kundgodkännande

Den här artikeln beskrivs de mål, uppgifter och slutprodukter som är associerade med kundens godkännande scenen för Team Data Science Process (TDSP). Den här processen ger en rekommenderad livscykel som du kan använda för att strukturera dina data science-projekt. Livscykeln beskrivs de viktigaste stegen som projekt vanligtvis köra, ofta upprepade gånger:

   1. **Förståelse för verksamheten**
   2. **Data förvärv och förståelse av**
   3. **Modellering**
   4. **Distribution**
   5. **Kundgodkännande**

Här är en visuell representation av TDSP-livscykeln: 

![Livscykel för TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Mål
**Slutför projektets slutprodukter**: Bekräfta att pipelinen modellen och deras distribution i en produktionsmiljö uppfyller kundens mål.

## <a name="how-to-do-it"></a>Gör så
Det finns två huvudsakliga uppgifter som beskrivs i det här steget:

   * **Systemverifiering**: Bekräfta att distribuerad modell och pipeline uppfyller kundens behov.
   * **Projektet hand av**: lämnar projektet till den enhet som ska köras systemet i produktion.

Kunden bör verifiera att systemet uppfyller sina affärsbehov och att den svarar på frågorna med godkända noggrannhet att distribuera systemet till produktion för användning av sina klientprogram. All dokumentation underkänts och granskas. Projektet är får av till entitet som ansvarar för åtgärder. Den här entiteten kan till exempel vara ett IT- eller kund data science team eller en agent till kunden som ansvarar för att systemet körs i produktion. 

## <a name="artifacts"></a>Artefakter
Den huvudsakliga artefakt som skapas i den här sista steget är det **avsluta rapporten projektets för kunden**. Den här tekniska rapporten innehåller information om projektet som är användbara för att lära dig mer om hur du använder systemet. TDSP ger en [avsluta rapporten](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) mall. Du kan använda mallen som den är eller du kan anpassa den för specifika kundens behov. 


## <a name="next-steps"></a>Nästa steg

Här finns länkar till varje steg i livscykeln för TDSP:

   1. [Förståelse för verksamheten](lifecycle-business-understanding.md)
   2. [Data förvärv och förståelse av](lifecycle-data.md)
   3. [Modellering](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundgodkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständig från slutpunkt till slutpunkt genomgång som visar alla steg i processen för specifika scenarier. Den [exempel genomgångar](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genomgångar visar hur du kombinerar molnlösningar, lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program. 

Exempel på hur du utför stegen i TDSPs som använder Azure Machine Learning Studio finns [använder TDSP med Azure Machine Learning](https://aka.ms/datascienceprocess).
