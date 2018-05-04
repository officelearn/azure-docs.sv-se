---
title: Migrera lokala datacenter till Azure | Microsoft Docs
description: Läs ett white paper som ger en översikt över hur du migrerar lokala datacenter till Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: 8ba490998ea5f20efca591327716a6e39e9c1ba8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="migrating-your-on-premises-workloads-to-azure"></a>Migrera dina lokala arbetsbelastningar till Azure


Microsoft Azure tillhandahåller åtkomst till en omfattande uppsättning molntjänster, som utvecklare och IT-proffs kan använda för att skapa, distribuera och hantera appar med en mängd verktyg och ramverk, genom ett globalt datacenternätverk. När ditt företag möter utmaningarna med digitaliseringen hjälper Azure-molnet dig att optimera resurser och åtgärder, interagera med dina kunder och anställda och transformera dina produkter.

Men även om det finns stora fördelar med att migrera till Azure och molnet i form av hastighet och flexibilitet, minskade kostnader, prestanda och tillförlitlighet, kan det för många organisationer ibland vara nödvändigt att köra lokala datacenter ibland. För att lösa problem med barriärer vid molnimplementering tillhandahåller Azure en hybridmolnstrategi, som bygger broar mellan dina lokala datacenter och det offentliga Azure-molnet. Man kan t.ex. använda molnresurser i Azure som säkerhetskopiering för att skydda lokala resurser, eller använda Azure Analytics för att få mer kunskap om lokala arbetsbelastningar. 

Som en del av vår hybrida molnstrategi tillhandahåller Azure allt fler lösningar för att migrera lokala appar och arbetsbelastningar till molnet. Med de här enkla stegen kan du noggrant bedöma dina lokala resurser för att ta reda på hur de skulle fungera i Azure-molnet. Sedan kan du på ett säkert sätt migrera dina resurser till Azure. När resurserna är igång och körs i Azure, kan du optimera dem för att behålla och förbättra åtkomsten, flexibiliteten, säkerheten och tillförlitligheten.

Den här serien av migreringsartiklar visar hur du kan planera och skapa en migreringsstrategi för ditt företag. Artiklarna visar ett antal scenarier som med tiden blir allt mer komplexa. Dessa scenarier sammanfattas i tabellen nedan. För varje scenario ger vi en översikt och en migreringsarkitektur och vi visar stegen som ingår i migreringen. 

**Scenario** | **Lösning** | **Tjänster** | **Artikel** 
--- | --- | --- | ---
[Scenario 1: Identifiering och utvärdering](migrate-scenarios-assessment.md) | Identifiera och utvärdera lokala appar, data och infrastruktur för migrering till Azure | Data Migration Assistant, tjänsten Azure Migrate  | Tillgängligt nu
**[Scenario 2: Rehost-app](migrate-scenarios-lift-and-shift.md)** | Lift och SKIFT appar till Azure. | Azure Site Recovery, Azure Database Migration Service, Azure SQL-hanterad instans | Tillgängligt nu
**Scenario 3: Flytta app** | Refactor appar under migreringen till Azure. | Under planering | Planerad
**Scenario 4: Rearchitect app** | Rearchitect appar under migreringen till Azure. | Under planering | Planerad
**Scenario 5: Återskapa appen** |Återskapa appar under migreringen till Azure | Under planering | Planerad




