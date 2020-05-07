---
title: Team Analytics och AI-miljö
titleSuffix: Azure Data Science Virtual Machine
description: Mönster för att distribuera Data Science VM i en företags grupp miljö.
keywords: djup inlärning, AI, data vetenskaps verktyg, virtuell data vetenskaps dator, Geospatial analys, team data vetenskaps process
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: 03fdbf6979db3249d1322a3025b48de81b953ae1
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856208"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Data Science Virtual Machine-baserad team Analytics och AI-miljö 
[Data science Virtual Machine](overview.md) (DSVM) innehåller en omfattande miljö på Azure-plattformen, med fördefinierad program vara för artificiell intelligens (AI) och data analys.

Traditionellt sett har DSVM använts som ett individuellt analys skriv bord. Enskilda data forskare får produktivitet med den här delade, färdiga analys miljön. Eftersom stora analys team planerar miljöer för sina data experter och AI-utvecklare, är ett av de återkommande teman en delad analys infrastruktur för utveckling och experimentering. Den här infrastrukturen hanteras i enlighet med företagets IT-principer som också fören klar samarbetet och konsekvensen mellan data vetenskap och analys team.

En delad infrastruktur ger bättre IT-användning i analys miljön. Vissa organisationer kallar den teambaserade infrastrukturen för data vetenskap/analys i ett *Analytics-läge*. Det gör det möjligt för data experter att få åtkomst till olika data till gångar för att snabbt förstå data. Den här sandbox-miljön hjälper också data experter att köra experiment, validera Hypotheses och skapa förutsägande modeller utan att påverka produktions miljön.

Eftersom DSVM fungerar på Azures infrastruktur nivå kan IT-administratörer enkelt konfigurera DSVM för att fungera i enlighet med företagets IT-principer. DSVM erbjuder full flexibilitet i att implementera olika delnings arkitekturer samtidigt som du erbjuder åtkomst till företags data till gångar på ett kontrollerat sätt.

I det här avsnittet beskrivs några mönster och rikt linjer som du kan använda för att distribuera DSVM som en team-baserad infrastruktur för data vetenskap. Eftersom Bygg stenarna för dessa mönster kommer från Azure Infrastructure as a Service (IaaS), gäller de för alla virtuella Azure-datorer. Den här serien med artiklar fokuserar på att tillämpa dessa standard funktioner för Azure-infrastrukturen på DSVM.

Viktiga Bygg stenar i en Enterprise team Analytics-miljö är:

* [En pool med autoskalning av Dsvm](dsvm-pools.md)
* [Gemensam identitet och åtkomst till en arbets yta från någon av Dsvm i poolen](dsvm-common-identity.md)
* [Säker åtkomst till data källor](dsvm-secure-access-keys.md)


Den här serien ger vägledning och pekare för vart och ett av föregående avsnitt. Det beskriver inte alla överväganden och krav för att distribuera Dsvm i stora företags konfigurationer. Här följer några andra Azure-resurser som du kan använda när du implementerar DSVM-instanser i företaget:

* [Nätverkssäkerhet](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [Övervakning](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) och [hantering](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Loggning och granskning](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Princip inställning och tillämpning](../../governance/policy/overview.md)
* [Skadlig kod](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Kryptering](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-overview)
* [Identifiering och styrning av data](https://docs.microsoft.com/azure/data-catalog/)

Slutligen tillhandahåller [Azure Architecture Center](https://docs.microsoft.com/azure/architecture/) en detaljerad arkitektur och modeller från slut punkt till slut punkt för att skapa och hantera en molnbaserad analys infrastruktur.