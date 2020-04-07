---
title: Arbetsbelastningshantering
description: Vägledning för implementering av arbetsbelastningshantering i Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: dd867d4aa9a9ef5ed73e78a46826a8cd5239039b
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744237"
---
# <a name="what-is-workload-management"></a>Vad är arbetsbelastningshantering?

Om du kör blandade arbetsbelastningar kan det innebära resursutmaningar på upptagna system.  Lösningsarkitekter söker efter sätt att separera klassiska datalagringsaktiviteter (till exempel inläsning, transformering och frågedata) för att säkerställa att det finns tillräckligt med resurser för att träffa SLA:er.  

Fysisk serverisolering kan leda till fickor av infrastruktur som är underutnyttjade, överbokade eller i ett tillstånd där cacheminnen ständigt primas med maskinvarustart och stopp.  Ett framgångsrikt arbetsbelastningshanteringssystem hanterar effektivt resurser, säkerställer mycket effektivt resursutnyttjande och maximerar avkastningen på investeringen (ROI).

En datalagerarbetsbelastning refererar till alla operationer som framkommer i relation till ett informationslager. Djupet och bredden på dessa komponenter beror på tidslagerets mognadsnivå.  Datalagrets arbetsbelastning omfattar:

- Hela processen med att läsa in data i lagret
- Utföra analys och rapportering av informationslager
- Hantera data i informationslagret
- Exportera data från informationslagret

Prestandakapaciteten för ett informationslager bestäms av [informationslagerenheterna](what-is-a-data-warehouse-unit-dwu-cdwu.md).

- Om du vill visa de resurser som allokerats för alla prestandaprofiler finns i [Begränsningar för minne och samtidighet](memory-concurrency-limits.md).
- Om du vill justera kapaciteten kan du [skala upp eller ned](quickstart-scale-compute-portal.md).

## <a name="workload-management-concepts"></a>Begrepp för hantering av arbetsbelastningar

Tidigare har du för SQL Analytics i Azure Synapse hanterat frågeprestanda via [resursklasser](resource-classes-for-workload-management.md).  Resursklasser som tillåts för att tilldela minne till en fråga baserat på rollmedlemskap.  Den primära utmaningen med resursklasser är att det, när de väl har konfigurerats, inte fanns någon styrning eller förmåga att kontrollera arbetsbelastningen.  

Om du till exempel beviljade ett ad hoc-användarrollmedlemskap till smallrc tillåts användaren att förbruka 100 % av minnet i systemet.  Med resursklasser finns det inget sätt att reservera och se till att resurser är tillgängliga för kritiska arbetsbelastningar.

Hantering av Synapse SQL-poolarbetsbelastning i Azure Synapse består av tre högnivåbegrepp: [arbetsbelastningsklassificering,](sql-data-warehouse-workload-classification.md) [arbetsbelastningsbetydelse](sql-data-warehouse-workload-importance.md) och [arbetsbelastningsisolering](sql-data-warehouse-workload-isolation.md).  Dessa funktioner ger dig mer kontroll över hur din arbetsbelastning använder systemresurser.

Arbetsbelastningsklassificering är konceptet att tilldela en begäran till en arbetsbelastningsgrupp och ange prioritetsnivåer.  Historiskt sett har detta uppdrag gjorts via rollmedlemskap med hjälp av [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Detta kan nu göras via [CREATE WORKLOAD CLASSIFER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Klassificeringsfunktionen ger en rikare uppsättning alternativ som etikett, session och tid för att klassificera begäranden.

Arbetsbelastningsbetydelse påverkar i vilken ordning en begäran får åtkomst till resurser.  På ett upptaget system har en begäran med större betydelse först åtkomst till resurser.  Betydelse kan också säkerställa ordnad åtkomst till lås.

Arbetsbelastningsisolering reserverar resurser för en arbetsbelastningsgrupp.  Resurser som reserverats i en arbetsbelastningsgrupp lagras uteslutande för den arbetsbelastningsgruppen för att säkerställa körning.  Arbetsbelastningsgrupper kan du också definiera hur mycket resurser som tilldelas per begäran, ungefär som resursklasser gör.  Arbetsbelastningsgrupper ger dig möjlighet att reservera eller begränsa mängden resurser som en uppsättning begäranden kan använda.  Slutligen är arbetsbelastningsgrupper en mekanism för att tillämpa regler, till exempel tidsgränsen för frågor, på begäranden.  

## <a name="next-steps"></a>Nästa steg

- Mer information om arbetsbelastningsklassificering finns i [Arbetsbelastningsklassificering](sql-data-warehouse-workload-classification.md).  
- Mer information om arbetsbelastningsisolering finns i [Arbetsbelastningsisolering](sql-data-warehouse-workload-isolation.md).  
- Mer information om arbetsbelastningsbetydning finns i [Arbetsbelastningsbetydning](sql-data-warehouse-workload-importance.md).  
- Mer information om övervakning av arbetsbelastningshantering finns i [Övervakning av arbetsbelastningshanteringsportalen](sql-data-warehouse-workload-management-portal-monitor.md).  
