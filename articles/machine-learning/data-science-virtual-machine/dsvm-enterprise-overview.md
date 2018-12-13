---
title: Introduktion till team Data Science Virtual Machine-baserade miljöer – Azure | Microsoft Docs
description: Mönster för att distribuera den virtuella datorn för datavetenskap i en företagsmiljö team.
keywords: djupinlärning, AI, verktyg för datavetenskap, virtuell dator för datavetenskap, geospatial analys, tdsp
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 84dfef65c341ca8023b741a98c26081da36e9ff7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085135"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Data Science Virtual Machine-baserade team analys och AI-miljö 
Den [Data Science Virtual Machine](overview.md) (DSVM) tillhandahåller en omfattande miljö i Azure-plattformen med färdiga programvara för artificiell intelligens (AI) och dataanalys. 

Traditionellt har DSVM används som en enskild analysskrivbord. Enskilda dataforskare få produktivitet med den här delade begreppet deras färdiga analytics-miljö. När stora analytics team planerar sina miljöer för analys för datatekniker och AI-utvecklare, är återkommande tema en delad analytics-infrastruktur för utveckling och experimentering. Den här infrastrukturen hanteras i enlighet med enterprise IT principer som även underlätta samarbete och konsekvens i data science/analytics team. 

En delad infrastruktur kan också IT-avdelningen att bättre utnyttja analytics-miljö. Vissa organisationer anropa teambaserad data science-/ analysinfrastruktur en ”analytics sandlåda”. Det gör det möjligt för dataexperter att få åtkomst till olika datatillgångar för att snabbt förstå data, köra experiment, verifiera hypoteser och skapa förutsägande modeller utan att påverka produktionsmiljön. 

Eftersom DSVM arbetar på nivån Azure-infrastrukturen kan kan IT-administratörer lätt konfigurera DSVM att fungera med IT-principer för företaget. DSVM erbjuder fullständig flexibilitet implementera olika arkitekturer för delning med åtkomst till företagets datatillgångar på ett kontrollerat sätt. 

Det här avsnittet beskrivs vissa mönster och riktlinjer som du kan använda för att distribuera DSVM teambaserad data science infrastruktur. Byggstenarna för dessa mönster kommer från Azure-infrastruktur som en tjänst (IaaS), så att de gäller för alla virtuella Azure-datorer. Fokus i den här artikelserien är för att använda dessa funktioner för standard Azure-infrastrukturen till den virtuella datorn för datavetenskap. 

Några av de viktigaste byggstenarna i en företagsmiljö team analytics är:

* [Autoscaled pool av virtuella datorer för datavetenskap](dsvm-pools.md)
* [Vanliga identitet och åtkomst till en arbetsyta från någon av Dsvm i poolen](dsvm-common-identity.md)
* [Säker åtkomst till datakällor](dsvm-secure-access-keys.md)


Den här artikelserien ger vägledning och pekare för var och en av ovanstående objekt. Det täcker inte alla överväganden och behov distribuera DSVM i stort företag konfigurationer. Här är andra Azure-dokumentation som du kan använda när du implementerar DSVM-instanser i ditt företag: 

* [Nätverkssäkerhet](https://docs.microsoft.com/azure/security/azure-network-security)
* [Övervakning av](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) och [management](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Loggning och granskning](https://docs.microsoft.com/azure/security/azure-log-audit)
* [Rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Principinställningen och tvingande](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [Programvara mot skadlig kod](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [Kryptering](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Identifiering och styrning](https://docs.microsoft.com/azure/data-catalog/)

Den [Azure Architecture Center](https://docs.microsoft.com/azure/architecture/) ger en detaljerad slutpunkt till slutpunkt-arkitektur och mönster för att skapa och hantera din infrastruktur för molnbaserad analys. 
