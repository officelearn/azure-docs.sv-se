---
title: "Teamet datavetenskap processen livscykel – Azure | Microsoft Docs"
description: "Steg som krävs för att köra dina datavetenskap projekt."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: 264386b527626f3241966bfdb2fb061781121be1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="team-data-science-process-lifecycle"></a>Team datavetenskap Process livscykel

Team Data vetenskap processen (TDSP) innehåller en rekommenderad livscykel som du kan använda för att strukturera datavetenskap projekt. Livscykeln beskrivs stegen från början till slut att projekt vanligtvis följer när de utförs. Om du använder en annan datavetenskap livscykel, exempelvis [SKARPA DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) eller din organisations egna anpassade process, du kan fortfarande använda uppgiftsbaserade TDSP med dessa development livscykler. 

Den här livscykeln har utformats för datavetenskap projekt som är avsedda att levereras som en del av intelligent program. Dessa program distribuera machine learning eller artificiell intelligence modeller för förutsägelseanalys. Undersökande datavetenskap projekt och ad hoc-analytics projekt kan också dra nytta av den här processen. Men i sådana fall vissa stegen kanske inte behövs.    

TDSP livscykel består av fem viktiga steg som utförs upprepade gånger. Exempel på dessa är:

* [1. förstå företag](lifecycle-business-understanding.md)
* [2. datainsamling och förstå](lifecycle-data.md)
* [3 modellering av](lifecycle-modeling.md)
* [4-distribution](lifecycle-deployment.md)
* [5 kundens godkännande](lifecycle-acceptance.md)

Här är en bild av den **Team datavetenskap Process livscykel**. 

![TDSP livscykel](./media/lifecycle/tdsp-lifecycle2.png) 


Team datavetenskap Process livscykel modelleras som en sekvens av hävdade steg som ger riktlinjer för de uppgifter som behövs för att använda förutsägelsemodeller. Dessa modeller kan distribueras i en produktionsmiljö att utnyttjas för att skapa intelligent program. Syftet med den här processen livscykeln ska fortsätta att flytta datavetenskap projektet framåt mot en tydlig engagement slutpunkt. När det gäller att datavetenskap är en övning i forskning och identifiering, som kan kommunicera tydligt dessa uppgifter för din grupp och dina kunder med en väl definierad uppsättning artefakter att anställda standardiserade mallar kan undvika missförstånd och öka risken för slutförd komplexa datavetenskap projektet.

För varje steg anger vi du följande information:

* **Mål**: specifika mål.
* **Hur du gör det**: specifika uppgifter beskrivs och riktlinjer som tillhandahålls slutfört dem.
* **Artefakter**: leveranser och stöd för att skapa dem.

## <a name="next-steps"></a>Nästa steg
Fullständig genomgång för slutpunkt-till-slutpunkt som visar alla steg i processen för **specifika scenarier** tillhandahålls också. De anges och är kopplad till miniatyr beskrivningar i den [exempel genomgång](walkthroughs.md) avsnittet. De visar hur du kombinerar moln, lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program. 

Exempel körs steg i Team av vetenskapliga data som använder Azure Machine Learning Studio finns i [med Azure ML](http://aka.ms/datascienceprocess) utbildningsvägar.

