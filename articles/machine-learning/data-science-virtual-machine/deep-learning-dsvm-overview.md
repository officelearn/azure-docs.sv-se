---
title: Introduktion till Djupinlärning för virtuell dator – Azure | Microsoft Docs
description: Viktiga analysscenarier och komponenter för virtuella datorer för Djupinlärning.
keywords: djupinlärning, AI, verktyg för datavetenskap, virtuell dator för datavetenskap, verktyg för datavetenskap, datavetenskap för linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 879f5939f110fb841ad160bf09f597edcdd86d31
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60502205"
---
# <a name="introduction-to-the-deep-learning-virtual-machine"></a>Introduktion till virtuell dator för Djupinlärning

## <a name="why-deep-learning-virtual-machine"></a>Varför Deep Learning Virtual Machine? 

Allt, deep learning-algoritmer / djupa neurala nätverk blir en av de populära metoder många machine learning-problem. De är särskilt bra på datorn kognition uppgifter, t.ex. bild, text, ljud/video förstå ofta närmar sig mänskliga kognitiva nivåer i vissa specifika domäner med avancerade djupa neurala nätverksarkitekturer och åtkomst till stor mängd data att träna modeller. Djupinlärning kräver stora mängder dataresurserna kan skapa modeller med dessa stora datauppsättningar. Med molnet och tillgängligheten för grafiska Processing Units (GPU) blir det möjligt att skapa avancerade djupa neurala arkitektur och träna dem på en stor datauppsättning på kraftfulla datorinfrastrukturen i molnet.  Den [Data Science Virtual Machine](overview.md) tillhandahåller en omfattande uppsättning verktyg och exempel för förberedelse av data, maskininlärning och djupinlärning. Men en av utmaningarna av användarna är att identifiera verktyg och exempel för specifika scenarier som deep learning enkelt och också etablera enklare GPU-baserad VM-instanser. Den här Deep Learning Virtual Machine (DLVM) löser dessa problem. 

## <a name="what-is-deep-learning-virtual-machine"></a>Vad är Deep Learning Virtual Machine? 
Deep Learning Virtual Machine är en speciellt konfigurerad variant av den [Data Science Virtual Machine](overview.md) (DSVM) för att göra det enklare att använda GPU-baserad VM-instanser för att träna modeller för djup maskininlärning. Den stöds på Windows 2016 och Ubuntu Data Science Virtual Machine.  Den delar samma VM-avbildningarna för core (och därför alla omfattande uppsättning verktyg) som DSVM men har konfigurerats för att underlätta djupinlärning. Vi ger även slutpunkt till slutpunkt-exempel för avbildningen och förståelse för text, som Allmänt gäller för många scenarier i verkliga livet är vanligtvis-AI. Den virtuella datorn för djupinlärning försöker också för att den omfattande uppsättningen med verktyg och exempel på DSVM enkelt kunna identifieras genom att visa en katalog med verktyg och exempel på den virtuella datorn. När det gäller verktygen innehåller Deep Learning Virtual Machine flera populära deep learning-ramverk, verktyg för att hämta och förprocess bild, text. En omfattande lista över verktyg som du kan referera till den [översiktssidan för Data Science Virtual Machine](overview.md#whats-included-in-the-data-science-vm). 

## <a name="next-steps"></a>Nästa steg

Kom igång med Deep Learning Virtual Machine för med följande steg:

* [Etablera en virtuell dator för Djupinlärning](provision-deep-learning-dsvm.md)
* [Använd virtuell dator för Djupinlärning](use-deep-learning-dsvm.md)
* [Verktyget referens](dsvm-deep-learning-ai-frameworks.md)
* [Exempel](dsvm-samples-and-walkthroughs.md)
