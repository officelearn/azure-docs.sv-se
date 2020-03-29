---
title: Kundens acceptansfas för livscykeln för teamdatavetenskapsprocessen
description: Mål, uppgifter och slutprodukter för kundacceptansstadiet för dina datavetenskapliga projekt
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
ms.openlocfilehash: 7224a7bb26ef491915df9fcb9b6b84ff171a9fc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720528"
---
# <a name="customer-acceptance-stage-of-the-team-data-science-process-lifecycle"></a>Kundens acceptansfas för livscykeln för teamdatavetenskapsprocessen

I den här artikeln beskrivs de mål, uppgifter och slutprodukter som är associerade med kundacceptansstadiet i Team Data Science Process (TDSP). Den här processen innehåller en rekommenderad livscykel som du kan använda för att strukturera dina datavetenskapsprojekt. Livscykeln beskriver de stora faserna som projekt vanligtvis körs, ofta iterativt:

   1. **Förståelse för verksamheten**
   2. **Förvärv och förståelse av data**
   3. **Modellering**
   4. **Distribution**
   5. **Kundgodkännande**

Här är en visuell representation av TDSP livscykel: 

![TDSP-livscykel](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Mål
**Slutför projektleveranser:** Bekräfta att pipelinen, modellen och distributionen av dem i en produktionsmiljö uppfyller kundens mål.

## <a name="how-to-do-it"></a>Hur man gör det
Det finns två huvuduppgifter som tas upp i detta skede:

   * **Systemvalidering**: Bekräfta att den distribuerade modellen och pipelinen uppfyller kundens behov.
   * **Projekthand-off:** Lämna projektet till den enhet som kommer att köra systemet i produktion.

Kunden bör verifiera att systemet uppfyller deras affärsbehov och att det besvarar frågorna med acceptabel noggrannhet för att distribuera systemet till produktion för användning av klientens program. All dokumentation är klar och granskad. Projektet överlämnas till den enhet som ansvarar för verksamheten. Den här entiteten kan till exempel vara ett IT- eller kunddatavetenskapsteam eller en agent för kunden som är ansvarig för att köra systemet i produktion. 

## <a name="artifacts"></a>Artefakter
Den viktigaste artefakten som produceras i det här slutskedet är **exitrapporten för projektet för kunden**. Den här tekniska rapporten innehåller alla detaljer i projektet som är användbara för att lära sig mer om hur du använder systemet. TDSP tillhandahåller en [exit-rapportmall.](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) Du kan använda mallen som den är, eller så kan du anpassa den för specifika klientbehov. 


## <a name="next-steps"></a>Nästa steg

Här är länkar till varje steg i livscykeln för TDSP:

   1. [Förståelse för verksamheten](lifecycle-business-understanding.md)
   2. [Förvärv och förståelse av data](lifecycle-data.md)
   3. [Modellering](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundgodkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständiga genomgångar som visar alla steg i processen för specifika scenarier. I artikeln [Exempelgenomgångar](walkthroughs.md) finns en lista över scenarier med länkar och miniatyrbeskrivningar. Genomgångarna illustrerar hur du kombinerar moln, lokala verktyg och tjänster till ett arbetsflöde eller en pipeline för att skapa ett intelligent program. 

Exempel på hur du kör steg i TDSPs som använder Azure Machine Learning Studio finns i [Använda TDSP med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
