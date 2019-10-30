---
title: Livs cykeln för team data science process
description: TDSP (Team data science process) tillhandahåller en rekommenderad livs cykel som du kan använda för att strukturera data vetenskaps projekt.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 00efe89314d4a1a5c3302e820b8609adf194aa59
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053245"
---
# <a name="the-team-data-science-process-lifecycle"></a>Livs cykeln för team data science process

TDSP (Team data science process) tillhandahåller en rekommenderad livs cykel som du kan använda för att strukturera data vetenskaps projekt. Livs cykeln beskriver stegen, från början till slut, som vanligt vis följer de projekt som körs. Om du använder en annan data vetenskaps livs cykel, till exempel en process som bygger på flera bransch standarder för Data utvinning [(skarp DM)](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), kunskaps identifiering i databaser [(KDD)](https://wikipedia.org/wiki/Data_mining#Process)eller din organisations egen anpassade process, kan du fortfarande använda den uppgiftsbaserade TDSP. 

Den här livs cykeln är utformad för data vetenskaps projekt som är avsedda att levereras som en del av intelligenta program. Dessa program distribuerar Machine Learning-eller artificiell Intelligence-modeller för förutsägelse analys. Undersökande data – vetenskaps projekt och ad hoc Analytics-projekt kan också dra nytta av användningen av den här processen. Men för dessa projekt kanske vissa av stegen som beskrivs här inte behövs. 

## <a name="five-lifecycle-stages"></a>Fem livs cykel steg

TDSP-livscykeln består av fem huvud steg som körs iterativt. Dessa steg omfattar:

   1. [Affärs förståelse](lifecycle-business-understanding.md)
   2. [Data hämtning och förståelse](lifecycle-data.md)
   3. [Form](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kund godkännande](lifecycle-acceptance.md)

Här är en visuell representation av TDSP-livs cykeln: 

![TDSP-livscykel](./media/lifecycle/tdsp-lifecycle2.png) 


TDSP-livscykeln modelleras som en sekvens av upprepade steg som ger vägledning om de uppgifter som behövs för att använda förutsägande modeller. Du distribuerar de förutsägelse modeller i produktions miljön som du planerar att använda för att bygga smarta program. Målet med den här process livs cykeln är att fortsätta att flytta ett data vetenskaps projekt till en klar slut punkt för engagemang. Data vetenskap är en övning för forskning och identifiering. Möjligheten att kommunicera uppgifter till ditt team och dina kunder genom att använda en väldefinierad uppsättning artefakter som använder standardiserade mallar hjälper till att undvika fel. Med hjälp av dessa mallar ökar risken för att ett komplext data vetenskaps projekt slutförs.

För varje steg tillhandahåller vi följande information:

   * **Mål**: de speciella målen.
   * **Gör så här**: en översikt över de olika aktiviteterna och rikt linjerna för hur du utför dem.
   * **Artefakter**: slut produkter och support för att skapa dem.

## <a name="next-steps"></a>Nästa steg

Vi tillhandahåller fullständiga genom gångar som demonstrerar alla steg i processen för särskilda scenarier. [Exempel](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genom gången illustrerar hur du kombinerar moln, lokala verktyg och tjänster till ett arbets flöde eller en pipeline för att skapa ett intelligent program. 

Exempel på hur du kör steg i TDSPs som använder Azure Machine Learning Studio finns i [använda TDSP med Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
