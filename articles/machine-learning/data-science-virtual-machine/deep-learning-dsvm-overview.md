---
title: Introduktion till den virtuella datorn för djup inlärning – Azure | Microsoft Docs
description: Scenarier och komponenter för nyckel analys för djup inlärning Virtual Machines.
keywords: djup inlärning, AI, data vetenskaps verktyg, virtuell dator för data vetenskap, verktyg för data vetenskap, Linux data science
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/16/2018
ms.author: vijetaj
ms.openlocfilehash: 8541713219eb8daf880605089fe49fc585836bdc
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534951"
---
# <a name="introduction-to-the-deep-learning-virtual-machine"></a>Introduktion till den virtuella datorn för djup inlärning

> [!NOTE]
> Alla DLVM-verktyg (djupgående Learning VM) har nu viks i [data science VM (DSVM)](dsvm-tools-overview.md). DLVM och DSVM är nu desamma. Överväg att skapa en DSVM i stället för DLVM.

## <a name="why-deep-learning-virtual-machine"></a>Varför en virtuell dator i djup inlärning? 

Allt fler och djup inlärnings algoritmer/djup neurala nätverk blir en av de populära metoderna som används i många maskin inlärnings problem. De är särskilt användbara i Cognition-uppgifter som bild, text, ljud/video om du ofta närmar sig de mänskliga kognitiva nivåerna i vissa vissa domäner med avancerade neurala nätverks arkitekturer och åtkomst till stora data uppsättningar för att träna modeller. Djup inlärningen kräver stor mängd beräknings kraft för att träna modeller med dessa stora data uppsättningar. Med molnet och tillgängligheten för GPU: er (Graphical Processing Unit) är det möjligt att skapa avancerade neurala-arkitekturer och träna dem på en stor data uppsättning på en kraftfull data behandlings infrastruktur i molnet.  [Data science Virtual Machine](overview.md) har angett en omfattande uppsättning verktyg och exempel för förberedelse av data, maskin inlärning och djup inlärning. Men en av utmaningarna som användarna möter är att upptäcka verktyg och exempel för särskilda scenarier som djup inlärning och också enklare att etablera GPU-baserade VM-instanser. Den här DLVM-datorn (djupgående Learning Virtual Machine) löser dessa utmaningar. 

## <a name="what-is-deep-learning-virtual-machine"></a>Vad är den virtuella datorn för djup inlärning? 
Den virtuella datorn för djup inlärning är en särskilt konfigurerad variant av [data science Virtual Machine](overview.md) (DSVM) för att göra det mer enkelt att använda GPU-baserade VM-instanser för att träna djupgående inlärnings modeller. Den stöds i Windows 2016 och Ubuntu-Data Science Virtual Machine.  Den delar samma kärn VM-avbildningar (och därmed alla avancerade verktyg) som DSVM men har kon figurer ATS för att göra djup inlärningen enklare. Vi tillhandahåller också exempel från slut punkt till slut punkt för bild-och text förståelse som är allmänt tillämpliga för många Real liv AI-scenarier. Den virtuella datorn för djup inlärning försöker också få en omfattande uppsättning verktyg och exempel på DSVM lättare att upptäcka genom att visa en katalog med verktyg och exempel på den virtuella datorn. När det gäller verktyg tillhandahåller den virtuella datorn för djup inlärning flera populära ramverk för djup inlärning, verktyg för att hämta och Förbearbeta bilder, text data. En omfattande lista över verktyg finns på [sidan data science Virtual Machine översikt](overview.md#whats-included-in-the-data-science-vm). 

## <a name="next-steps"></a>Nästa steg

Kom igång med den virtuella datorn för djup inlärning med följande steg:

* [Etablera en Windows-Data Science Virtual Machine](provision-vm.md)
* [Etablera en Ubuntu Data Science Virtual Machine](dsvm-ubuntu-intro.md)

