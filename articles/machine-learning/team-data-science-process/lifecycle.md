---
title: Team datavetenskap Process livscykel – Azure | Microsoft Docs
description: De steg som krävs för att köra datavetenskap projekt
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: a971e40bc4338f187991eeae991044248844d8b6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="the-team-data-science-process-lifecycle"></a>Team datavetenskap Process livscykel

Team Data vetenskap processen (TDSP) innehåller en rekommenderad livscykel som du kan använda för att strukturera datavetenskap projekt. Livscykeln beskrivs stegen från början till slut att projekt vanligtvis följer när de utförs. Om du använder en annan datavetenskap livscykel, till exempel mellan branschen standardprocessen för datautvinning [(SKARPA-DM)](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), Knowledge identifiering i databaser [(KDD)](https://wikipedia.org/wiki/Data_mining#Process), eller din organisations egna anpassade processen , du kan fortfarande använda uppgiftsbaserade TDSP. 

Den här livscykeln är utformad för datavetenskap projekt som är avsedda att levereras som en del av intelligent program. Dessa program distribuera machine learning eller artificiell intelligence modeller för förutsägelseanalys. Undersökande datavetenskap projekt och ad hoc-analytics projekt kan också dra nytta av den här processen. Men för de projekt vissa av stegen som beskrivs här kanske inte behövs. 

TDSP livscykel består av fem viktiga steg som utförs upprepade gånger. Dessa steg omfattar:

   1. [Så här fungerar för företag](lifecycle-business-understanding.md)
   2. [Datainsamling och förstå](lifecycle-data.md)
   3. [Modeling](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundens godkännande](lifecycle-acceptance.md)

Här är en bild av TDSP livscykeln: 

![TDSP livscykel](./media/lifecycle/tdsp-lifecycle2.png) 


TDSP livscykel modelleras som en sekvens av hävdade steg som ger riktlinjer för de uppgifter som behövs för att använda förutsägelsemodeller. Du kan distribuera förutsägelsemodeller i produktionsmiljön som du planerar att skapa intelligent program. Syftet med den här processen livscykeln ska fortsätta att flytta en datavetenskap projektet mot en tydlig engagement slutpunkt. Datavetenskap är en uppgift i forskning och identifiering. Möjligheten att kommunicera uppgifter att ditt team och dina kunder med hjälp av en väldefinierad uppsättning artefakter som använder standardiserade mallar bidrar till att undvika missförstånd. Med hjälp av dessa mallar ökar också risken för slutförande av en komplex datavetenskap-projektet.

För varje steg anger vi du följande information:

   * **Mål**: specifika mål.
   * **Hur du gör det**: en disposition över särskilda aktiviteter och anvisningar om hur du slutför dem.
   * **Artefakter**: leveranser och stöd för att skapa dem.

## <a name="next-steps"></a>Nästa steg

Vi ger fullständig slutpunkt till slutpunkt genomgång som visar alla steg i processen för specifika scenarier. Den [exempel genomgång](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genomgångar illustrerar hur du kombinerar moln, lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program. 

Exempel på hur du utför stegen i TDSPs som använder Azure Machine Learning Studio finns [använder TDSP med Azure Machine Learning](http://aka.ms/datascienceprocess).
