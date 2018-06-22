---
title: Introduktion till datavetenskap virtuellt datorbaserat team - miljöer i Azure | Microsoft Docs
description: Mönster för att distribuera den virtuella datorn vetenskap Data i en företagsmiljö team.
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
ms.openlocfilehash: 8486b0be1fb5e1385da3c7ad55f6410a1059df93
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309256"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Vetenskap virtuellt datorbaserat team dataanalys och AI-miljö 
Den [datavetenskap virtuella](overview.md) (DSVM) ger en omfattande miljön i Azure-plattformen med fördefinierad programvara för artificiell intelligence (AI) och dataanalys. 

Traditionellt har DSVM används som en enskild analytics skrivbord. Enskilda forskare får produktivitet med den här delade begreppet deras färdiga analytics-miljö. Stora analytics team planera sina analytics miljöer för sina dataanalytiker och utvecklare AI, är återkommande tema en delad analytics infrastruktur för utveckling och experiment. Den här infrastrukturen hanteras i enlighet med företagets IT-principer som även underlätta samarbete och samverkan mellan olika datavetenskap/analytics grupper. 

En delad infrastruktur kan också IT att använda bättre analytics-miljö. Vissa organisationer anropa teambaserade datavetenskap/analytics infrastruktur en ”analytics sandbox”. Den gör att data forskare att komma åt olika datatillgångar för att snabbt förstå data, kör experiment, verifiera hypoteser och skapa förutsägelsemodeller utan att påverka produktionsmiljön. 

Eftersom DSVM arbetar på nivån Azure-infrastrukturen kan konfigurera IT-administratörer enkelt kan DSVM ska fungera i enlighet med IT-principer för företaget. DSVM erbjuder fullständig flexibilitet för att implementera olika delade arkitekturer med åtkomst till företagets datatillgångar på ett kontrollerat sätt. 

Det här avsnittet beskrivs vissa mönster och riktlinjer som du kan använda för att distribuera DSVM som en grupp datavetenskap infrastruktur. Byggblock för dessa mönster komma från Azure-infrastrukturen som en tjänst (IaaS), så att de gäller för alla virtuella datorer i Azure. Dessa artiklar fokuserar på om hur du använder dessa funktioner standard Azure-infrastrukturen till den virtuella datorn vetenskapliga Data. 

Några av de viktigaste byggstenarna i en företagsmiljö team analytics är:

* [Autoscaled pool datavetenskap virtuella datorer](dsvm-pools.md)
* [Gemensamma identitet och åtkomst till en arbetsyta från någon av DSVMs i poolen](dsvm-common-identity.md)
* [Säker åtkomst till datakällor](dsvm-secure-access-keys.md)


Dessa artiklar innehåller vägledning och pekare för var och en av ovanstående objekt. Det täcker inte alla överväganden och behoven i distributionen av DSVM i stora företag konfigurationer. Det finns andra Azure-dokumentationen som du kan använda när du implementerar DSVM instanser i ditt företag: 

* [Nätverkssäkerhet](https://docs.microsoft.com/azure/security/azure-network-security)
* [Övervaka](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) och [management](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Loggning och granskning](https://docs.microsoft.com/azure/security/azure-log-audit)
* [Rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Inställningen och tillämpning](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [Programvara mot skadlig kod](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [Kryptering](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Identifiering av data och styrning](https://docs.microsoft.com/azure/data-catalog/)

Den [Azure arkitektur Center](https://docs.microsoft.com/en-us/azure/architecture/) ger en detaljerad slutpunkt till slutpunkt-arkitektur och mönster för att skapa och hantera infrastrukturen molnbaserade analytics. 
