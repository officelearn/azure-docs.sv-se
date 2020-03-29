---
title: Livscykeln för teamdatavetenskapsprocessen
description: Team Data Science Process (TDSP) ger en rekommenderad livscykel som du kan använda för att strukturera dina datavetenskapliga projekt.
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
ms.openlocfilehash: a043a1655950f3ed7688e59352f8a912146e12c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720460"
---
# <a name="the-team-data-science-process-lifecycle"></a>Livscykeln för teamdatavetenskapsprocessen

Team Data Science Process (TDSP) ger en rekommenderad livscykel som du kan använda för att strukturera dina datavetenskapliga projekt. Livscykeln beskriver de fullständiga steg som framgångsrika projekt följer. Om du använder en annan livscykel för datavetenskap, till exempel Crisp-DM (Cross Industry Standard Process for Data Mining), Knowledge Discovery in Databases (KDD) eller organisationens egen anpassade process, kan du fortfarande använda den uppgiftsbaserade TDSP.If you use another data-science lifecycle, such as the Cross Industry Standard Process for Data Mining [(CRISP-DM)](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), Knowledge Discovery in Databases [(KDD)](https://wikipedia.org/wiki/Data_mining#Process), or your organization's own custom process, you can still use the task-based TDSP. 

Den här livscykeln är utformad för datavetenskapliga projekt som är avsedda att levereras som en del av intelligenta program. Dessa program distribuerar maskininlärning eller artificiell intelligens modeller för prediktiv analys. Förberedande datavetenskapliga projekt och improviserade analysprojekt kan också dra nytta av användningen av denna process. Men för dessa projekt kanske några av de steg som beskrivs här inte behövs. 

## <a name="five-lifecycle-stages"></a>Fem livscykelstadier

TDSP-livscykeln består av fem huvudsteg som körs iterativt. Dessa steg inkluderar:

   1. [Förståelse för verksamheten](lifecycle-business-understanding.md)
   2. [Förvärv och förståelse av data](lifecycle-data.md)
   3. [Modellering](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundgodkännande](lifecycle-acceptance.md)

Här är en visuell representation av TDSP livscykel: 

![TDSP-livscykel](./media/lifecycle/tdsp-lifecycle2.png) 


TDSP-livscykeln modelleras som en sekvens av itererade steg som ger vägledning om de uppgifter som behövs för att använda prediktiva modeller. Du distribuerar de prediktiva modeller i produktionsmiljön som du planerar att använda för att skapa intelligenta program. Målet med den här processlivscykeln är att fortsätta att flytta ett datavetenskapligt projekt mot en tydlig slutpunkt för engagemang. Datavetenskap är en övning i forskning och upptäckt. Möjligheten att kommunicera uppgifter till ditt team och dina kunder med hjälp av en väldefinierad uppsättning artefakter som använder standardiserade mallar hjälper till att undvika missförstånd. Om du använder dessa mallar ökar också risken för att ett komplext datavetenskapsprojekt slutförs.

För varje steg tillhandahåller vi följande information:

   * **Mål**: De specifika målen.
   * **Hur man gör det:** En översikt över de specifika uppgifter och vägledning om hur du fyller i dem.
   * **Artefakter:** Slutprodukter och stöd för att producera dem.

## <a name="next-steps"></a>Nästa steg

Vi tillhandahåller fullständiga genomgångar från slutpunkt till slutpunkt som visar alla steg i processen för specifika scenarier. I artikeln [Exempelgenomgångar](walkthroughs.md) finns en lista över scenarier med länkar och miniatyrbeskrivningar. Genomgångarna illustrerar hur du kombinerar moln, lokala verktyg och tjänster till ett arbetsflöde eller en pipeline för att skapa ett intelligent program. 

Exempel på hur du kör steg i TDSPs som använder Azure Machine Learning Studio finns i [Använda TDSP med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
