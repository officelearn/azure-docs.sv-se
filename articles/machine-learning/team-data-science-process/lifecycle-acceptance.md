---
title: Kundens godkännande steg i livscykeln för Team Data Science Process
description: Mål, uppgifter och mål för kundens godkännande steg i dina data science-projekt
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720528"
---
# <a name="customer-acceptance-stage-of-the-team-data-science-process-lifecycle"></a>Kundens godkännande steg i livscykeln för Team Data Science Process

Den här artikeln beskrivs de mål, uppgifter och slutprodukter som är associerade med kundens godkännande scenen för Team Data Science Process (TDSP). Den här processen ger en rekommenderad livscykel som du kan använda för att strukturera dina data science-projekt. Livscykeln beskrivs de viktigaste stegen som projekt vanligtvis köra, ofta upprepade gånger:

   1. **Affärs förståelse**
   2. **Data hämtning och förståelse**
   3. **Form**
   4. **Distribution**
   5. **Kund godkännande**

Här är en visuell representation av TDSP-livscykeln: 

![Livscykel för TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Målet
**Slutför projektets slut produkter**: bekräfta att pipelinen, modellen och deras distribution i en produktions miljö uppfyller kundens mål.

## <a name="how-to-do-it"></a>Gör så
Det finns två huvudsakliga uppgifter som beskrivs i det här steget:

   * **System verifiering**: bekräfta att den distribuerade modellen och pipelinen uppfyller kundens behov.
   * **Projektets behållning**: gör om projektet till den entitet som ska köra systemet i produktion.

Kunden bör verifiera att systemet uppfyller sina affärsbehov och att den svarar på frågorna med godkända noggrannhet att distribuera systemet till produktion för användning av sina klientprogram. All dokumentation underkänts och granskas. Projektet är får av till entitet som ansvarar för åtgärder. Den här entiteten kan till exempel vara ett IT- eller kund data science team eller en agent till kunden som ansvarar för att systemet körs i produktion. 

## <a name="artifacts"></a>Artefakter
Huvud artefakten som skapas i det här slutliga skedet är **avslutnings rapporten för projektet för kunden**. Den här tekniska rapporten innehåller information om projektet som är användbara för att lära dig mer om hur du använder systemet. TDSP tillhandahåller en mall för att [Avsluta rapporter](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) . Du kan använda mallen som den är eller du kan anpassa den för specifika kundens behov. 


## <a name="next-steps"></a>Nästa steg

Här finns länkar till varje steg i livscykeln för TDSP:

   1. [Affärs förståelse](lifecycle-business-understanding.md)
   2. [Data hämtning och förståelse](lifecycle-data.md)
   3. [Form](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kund godkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständiga genom gångar som demonstrerar alla steg i processen för olika scenarier. [Exempel](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genomgångar visar hur du kombinerar molnlösningar, lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program. 

Exempel på hur du kör steg i TDSPs som använder Azure Machine Learning Studio finns i [använda TDSP med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
