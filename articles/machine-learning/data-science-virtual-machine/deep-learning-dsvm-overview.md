---
title: Introduktion till djup Learning Virtual Machine - Azure | Microsoft Docs
description: Viktiga analytics scenarier och komponenter för djup Learning virtuella datorer.
keywords: Djup learning AI, datavetenskap verktyg, datavetenskap virtuell dator, verktyg för datavetenskap, datavetenskap för linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 35370d3d143fdc154728aa8b31b9874227e46168
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31406899"
---
# <a name="introduction-to-the-deep-learning-virtual-machine"></a>Introduktion till djup Learning virtuell dator

## <a name="why-deep-learning-virtual-machine"></a>Varför djup Virtual Machine Learning? 

Allt, djupa learning algoritmer / djupa neurala nätverk blir en av de vanliga metoder många machine learning-problem. De är särskilt bra på att datorn kognition uppgifter som bild, text, ljud och video förstå ofta närmar sig mänsklig kognitiva nivåer i vissa specifika domäner med avancerade djupa neurala nätverksarkitekturer och åtkomst till stor mängd data att träna modeller. Djupgående learning kräver stora mängder dataresurser för att träna modeller med dessa stora datauppsättningar. Med molnet och tillgängligheten för grafiska enheter (grafikprocessorer) blir det möjligt att bygga avancerade djupa neurala arkitektur och träna dem på en stor mängd data på kraftfulla IT-infrastruktur i molnet.  Den [datavetenskap virtuella](overview.md) tillhandahåller en omfattande uppsättning verktyg och exempel för förberedelse av data och maskininlärning djup learning. Men en av utmaningarna av användarna är att identifiera verktyg och exempel för specifika scenarier som djup learning enkelt och också etablera enklare GPU-baserad VM-instanser. Den här djup Learning virtuell dator (DLVM) löser dessa problem. 

## <a name="what-is-deep-learning-virtual-machine"></a>Vad är djup Learning virtuella datorn? 
Djupgående Learning virtuella datorn är en speciellt konfigurerade variant av den [datavetenskap virtuella](overview.md) (DSVM) för att göra det enklare att använda GPU-baserade Virtuella instanser för utbildning djup learning-modeller. Den stöds på Windows 2016 och Ubuntu datavetenskap virtuella datorn.  Den delar samma core VM-avbildningar (och därför omfattande verktygsuppsättningen) som DSVM men har konfigurerats för att underlätta djup learning. Vi erbjuder även en slutpunkt till slutpunkt-exempel för bilden och texten förstå som brett gäller för många verkligheten AI-scenarier. Djupgående learning virtuella datorn försöker också göra omfattande uppsättning verktyg och exempel på DSVM lättare kan identifieras genom att visa en katalog med verktyg och exempel på den virtuella datorn. Vad gäller verktygsuppsättning tillhandahåller djup Learning virtuella datorn flera populära djup learning ramverk, verktyg för att hämta och före processer bilden, textinformation. En omfattande lista över verktyg som du kan referera till den [datavetenskap virtuella översiktssidan](overview.md#whats-included-in-the-data-science-vm). 

## <a name="next-steps"></a>Nästa steg

Kom igång med den djupgående Learning virtuella datorn med följande steg:

* [Etablera en djup Learning virtuell dator](provision-deep-learning-dsvm.md)
* [Använd djup Learning virtuell dator](use-deep-learning-dsvm.md)
* [Referens för verktyget](dsvm-deep-learning-ai-frameworks.md)
* [Exempel](dsvm-samples-and-walkthroughs.md)
