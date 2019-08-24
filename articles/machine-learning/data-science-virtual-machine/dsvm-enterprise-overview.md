---
title: Introduktion till team Data Science Virtual Machine-baserade miljöer – Azure | Microsoft Docs
description: Mönster för att distribuera den virtuella datorn för datavetenskap i en företagsmiljö team.
keywords: djupinlärning, AI, verktyg för datavetenskap, virtuell dator för datavetenskap, geospatial analys, tdsp
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: vijetaj
ms.openlocfilehash: 5f34498fbdacf7fc6e62788913c795ab70ceef23
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991621"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Data Science Virtual Machine-baserade team analys och AI-miljö 
[Data science Virtual Machine](overview.md) (DSVM) innehåller en omfattande miljö på Azure-plattformen, med fördefinierad program vara för artificiell intelligens (AI) och data analys.

Traditionellt har DSVM används som en enskild analysskrivbord. Enskilda data forskare får produktivitet med den här delade, färdiga analys miljön. Eftersom stora analys team planerar miljöer för sina data experter och AI-utvecklare, är ett av de återkommande teman en delad analys infrastruktur för utveckling och experimentering. Den här infrastrukturen hanteras i enlighet med företagets IT-principer som också fören klar samarbetet och konsekvensen mellan data vetenskap och analys team.

En delad infrastruktur ger bättre IT-användning i analys miljön. Vissa organisationer kallar den teambaserade infrastrukturen för data vetenskap/analys i ett *Analytics-läge*. Det gör det möjligt för data experter att få åtkomst till olika data till gångar för att snabbt förstå data. Den här sandbox-miljön hjälper också data experter att köra experiment, validera Hypotheses och skapa förutsägande modeller utan att påverka produktions miljön.

Eftersom DSVM arbetar på nivån Azure-infrastrukturen kan kan IT-administratörer lätt konfigurera DSVM att fungera med IT-principer för företaget. DSVM erbjuder full flexibilitet i att implementera olika delnings arkitekturer samtidigt som du erbjuder åtkomst till företags data till gångar på ett kontrollerat sätt.

Det här avsnittet beskrivs vissa mönster och riktlinjer som du kan använda för att distribuera DSVM teambaserad data science infrastruktur. Eftersom Bygg stenarna för dessa mönster kommer från Azure Infrastructure as a Service (IaaS), gäller de för alla virtuella Azure-datorer. Den här serien med artiklar fokuserar på att tillämpa dessa standard funktioner för Azure-infrastrukturen på DSVM.

Viktiga Bygg stenar i en Enterprise team Analytics-miljö är:

* [En pool med autoskalning av Dsvm](dsvm-pools.md)
* [Vanliga identitet och åtkomst till en arbetsyta från någon av Dsvm i poolen](dsvm-common-identity.md)
* [Säker åtkomst till datakällor](dsvm-secure-access-keys.md)


Den här serien ger vägledning och pekare för vart och ett av föregående avsnitt. Det beskriver inte alla överväganden och krav för att distribuera Dsvm i stora företags konfigurationer. Här följer några andra Azure-resurser som du kan använda när du implementerar DSVM-instanser i företaget:

* [Nätverkssäkerhet](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [Övervakning av](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) och [management](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Loggning och granskning](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Principinställningen och tvingande](../../governance/policy/overview.md)
* [Programvara mot skadlig kod](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Kryptering](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Identifiering och styrning](https://docs.microsoft.com/azure/data-catalog/)

Slutligen tillhandahåller [Azure Architecture Center](https://docs.microsoft.com/azure/architecture/) en detaljerad arkitektur och modeller från slut punkt till slut punkt för att skapa och hantera en molnbaserad analys infrastruktur.
