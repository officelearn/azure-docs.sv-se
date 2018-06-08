---
title: Introduktion till datavetenskap virtuell dator baserat Team - miljöer i Azure | Microsoft Docs
description: Mönster för att distribuera datavetenskap VM som team företagsmiljö.
keywords: Djup learning AI datavetenskap verktyg, datavetenskap virtuell dator, geospatiala analytics, team av vetenskapliga data
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: d2aa3c8582227363e9365f213cdf351b9f4a81af
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830545"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Data vetenskap virtuell dator baserat Team analyser och AI-miljö 
Den [datavetenskap virtuella](overview.md) (DSVM) ger en miljö med omfattande på Azure-molnet med inbyggda programvara för analys av AI och data. Traditionellt har används DSVM som enskilda analytics skrivbord och enskilda forskare få produktivitet med den här delade begreppet deras förskapad analytics-miljö. När stora analytics team planerar sina analytics miljöer för sina dataanalytiker och utvecklare AI, återkommande tema är för en delad analytics utveckling och experiment infrastruktur som hanteras i enlighet med företagets IT principer som underlättar också samarbete och konsekvens över hela datavetenskap / analytics team. En delad infrastruktur kan också IT att använda bättre analytics-miljö. Teambaserade datavetenskap / analytics infrastruktur kallas även av vissa organisationer som en ”Analytics Sandbox” som gör det möjligt för data-forskare att snabbt förstå data, köra experiment, verifiera hypotesen, skapa förutsägelsemodeller på ett säkert sätt utan att påverka produktionsmiljön samtidigt som du har åtkomst till olika datatillgångar. 

Eftersom DSVM fungerar på nivån Azure-infrastrukturen, kan IT-administratörer lätt konfigurera DSVM ska fungera i enlighet med IT-principer på företaget och ger fullständig flexibilitet för att implementera olika delade arkitekturer med åtkomst till företagets datatillgångar på ett kontrollerat sätt. 

Det här avsnittet beskrivs vissa mönster och riktlinjer som kan användas för att distribuera DSVM som en grupp datavetenskap infrastruktur.  Byggblock för dessa mönster utnyttjas direkt från Azure IaaS (infrastruktur som en tjänst) och därför gäller för alla virtuella datorer i Azure. Specifika fokuserar dessa artiklar på om hur du använder dessa funktioner standard Azure-infrastrukturen till den virtuella datorn vetenskapliga Data. 

Några av de viktigaste byggstenarna i en företagsmiljö team analytics är:

* [Automatisk skalade pool datavetenskap virtuella datorer](dsvm-pools.md)
* [Gemensamma identitet och åtkomst till arbetsytan från någon av DSVMs i poolen](dsvm-common-identity.md)
* [Säker åtkomst till datakällor](dsvm-secure-access-keys.md)


I den här serien med artiklar finns vägledning och pekare i var och en av dessa ovan aspekter. Naturligtvis, det finns flera ytterligare överväganden och behov när du distribuerar DSVM i stora företag konfigurationer som har direkt inte ingår i den här serien med artiklar. Här följer några av de andra överväganden och pekare till allmän Azure dokumentation som lätt kan användas när implementera på DSVM instanser i företaget. 

* [Nätverkssäkerhet](https://docs.microsoft.com/azure/security/azure-network-security)
* [Övervaka](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) och [Management](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Granskning och loggning](https://docs.microsoft.com/azure/security/azure-log-audit)
* [Rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Inställningen och tillämpning](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [Skadlig kod](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [Kryptering](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Identifiering av data och styrning](https://docs.microsoft.com/azure/data-catalog/)

Den [Azure-arkitektur center](https://docs.microsoft.com/en-us/azure/architecture/) är också en utmärkt resurs som ger detaljerad arkitektur för slutpunkt till slutpunkt och mönster för att skapa och hantera din molninfrastruktur baserat analytics. 
