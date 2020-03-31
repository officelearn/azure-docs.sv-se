---
title: Teamanalys och AI-miljö
titleSuffix: Azure Data Science Virtual Machine
description: Mönster för att distribuera den virtuella datorn för datavetenskap i en företagsgruppsmiljö.
keywords: djupinlärning, AI, datavetenskapsverktyg, virtuell datavetenskap, geospatial analys, teamdatavetenskapsprocess
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: 06d05d6d410af13bfbe85f3cb66523c1d48cb77c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "70195663"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Data Science Virtual Machine-baserad teamanalys och AI-miljö 
[Data Science Virtual Machine](overview.md) (DSVM) ger en rik miljö på Azure-plattformen, med fördefinierad programvara för artificiell intelligens (AI) och dataanalys.

Traditionellt har DSVM använts som ett enskilt analysskrivbord. Enskilda datavetar forskare ökar produktiviteten med den här delade, fördefinierade analysmiljön. När stora analysteam planerar miljöer för sina dataforskare och AI-utvecklare är ett av de återkommande temana en gemensam analysinfrastruktur för utveckling och experiment. Den här infrastrukturen hanteras i enlighet med företagets IT-principer som också underlättar samarbete och konsekvens i datavetenskaps- och analysteamen.

En delad infrastruktur möjliggör bättre IT-utnyttjande av analysmiljön. Vissa organisationer kallar den teambaserade datavetenskaps-/analysinfrastrukturen för en *analyssandlåda*. Det gör det möjligt för dataforskare att komma åt olika datatillgångar för att snabbt förstå data. Den här sandlådemiljön hjälper också dataforskare att köra experiment, validera hypoteser och bygga prediktiva modeller utan att påverka produktionsmiljön.

Eftersom DSVM fungerar på Azure-infrastrukturnivå kan IT-administratörer enkelt konfigurera DSVM så att den fungerar i enlighet med företagets IT-principer. DSVM erbjuder full flexibilitet i genomförandet av olika delningsarkitekturer samtidigt som den ger tillgång till företagets datatillgångar på ett kontrollerat sätt.

I det här avsnittet beskrivs några mönster och riktlinjer som du kan använda för att distribuera DSVM som en teambaserad datavetenskapsinfrastruktur. Eftersom byggstenarna för dessa mönster kommer från Azure-infrastruktur som en tjänst (IaaS), gäller de för alla virtuella Azure-datorer. Den här artikelserien fokuserar på att tillämpa dessa standardfunktioner för Azure-infrastruktur på DSVM.

Viktiga byggstenar i en företagsgruppanalysmiljö är:

* [En pool med automatisk skalning av DSVM](dsvm-pools.md)
* [Gemensam identitet och åtkomst till en arbetsyta från någon av DSVM:erna i poolen](dsvm-common-identity.md)
* [Säker åtkomst till datakällor](dsvm-secure-access-keys.md)


Den här serien ger vägledning och tips för vart och ett av de föregående ämnena. Det täcker inte alla överväganden och krav för att distribuera DSVMs i stora företagskonfigurationer. Här är några andra Azure-resurser som du kan använda när du implementerar DSVM-instanser i ditt företag:

* [Nätverkssäkerhet](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [Övervakning](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) och [förvaltning](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Loggning och granskning](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Principinställning och tillämpning](../../governance/policy/overview.md)
* [Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Kryptering](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Identifiering och styrning av data](https://docs.microsoft.com/azure/data-catalog/)

Slutligen tillhandahåller [Azure Architecture Center](https://docs.microsoft.com/azure/architecture/) en detaljerad heltäckande arkitektur och modeller för att skapa och hantera din molnbaserade analysinfrastruktur.