---
title: Azure SQL Database skala resurser | Microsoft Docs
description: Den här artikeln förklarar hur du skalar databasen genom att lägga till eller ta bort allokerade resurser.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 98d24b4f497f09e982101917296b572a5c381f42
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053602"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Skala databas resurser dynamiskt med minimal stillestånds tid

Med Azure SQL Database kan du dynamiskt lägga till fler resurser i databasen med minimal [stillestånds](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/)tid. Det finns dock en växel över tid där anslutningen förloras till databasen under en kort tids period, vilket kan minskas med logiken för omprövning.

## <a name="overview"></a>Översikt

När efter frågan på din app växer från en fåtal enheter och kunder till miljon tals Azure SQL Database skalas i farten med minimal stillestånds tid. Skalbarhet är en av de viktigaste egenskaperna hos PaaS som gör det möjligt att dynamiskt lägga till fler resurser till din tjänst när det behövs. Med Azure SQL Database kan du enkelt ändra resurser (processor kraft, minne, IO-dataflöde och lagring) som allokerats till dina databaser.

Du kan minska prestanda problem på grund av ökad användning av programmet som inte kan åtgärdas med hjälp av metoder för indexering eller fråga om omskrivning. Genom att lägga till fler resurser kan du snabbt reagera när databasen träffar de aktuella resurs gränserna och behöver mer kraft för att hantera inkommande arbets belastning. Med Azure SQL Database kan du också skala ut resurserna när de inte behövs för att sänka kostnaderna.

Du behöver inte bekymra dig om att köpa maskin vara och ändra den underliggande infrastrukturen. Skalnings databasen kan enkelt utföras via Azure Portal med hjälp av ett skjutreglage.

![Skalning av databas prestanda](media/sql-database-scalability/scale-performance.svg)

Azure SQL Database erbjuder den [DTU-baserade inköps modellen](sql-database-service-tiers-dtu.md) och den [vCore-baserade inköps modellen](sql-database-service-tiers-vcore.md).

- Den [DTU-baserade inköps modellen](sql-database-service-tiers-dtu.md) erbjuder en blandning av beräknings-, minnes-och IO-resurser i tre tjänst nivåer för att stödja lätta till tunga databas arbets belastningar: Basic, standard och Premium. Det finns prestandanivåer inom varje nivå med en blandning av dessa resurser, och du kan lägga till ytterligare lagringsresurser till dessa.
- Med den [vCore-baserade inköps modellen](sql-database-service-tiers-vcore.md) kan du välja antalet virtuella kärnor, mängden eller minnet samt mängden och lagrings hastigheten. Den här inköps modellen erbjuder tre tjänst nivåer: Generell användning, Affärskritisk och storskalig.

Du kan bygga din första app på en liten, enkel databas till en låg kostnad per månad på nivån Basic, standard eller Generell användning och sedan ändra dess tjänst nivå manuellt eller program mässigt när som helst till Premium-eller Affärskritisks tjänst nivån för att uppfylla Ne EDS för din lösning. Du kan justera prestandan utan driftavbrott för din app eller dina kunder. Dynamisk skalbarhet gör att databasen transparent kan svara på snabbt förändrade resurskrav och gör det möjligt för dig att endast betala för de resurser som du behöver, när du behöver dem.

> [!NOTE]
> Dynamisk skalbarhet skiljer sig från autoskalning. Autoskalning är när en tjänst skalas automatiskt baserat på villkor, medan dynamisk skalbarhet möjliggör manuell skalning med minimal stillestånds tid.

Enkla Azure SQL-databaser stöder manuell dynamisk skalbarhet, men inte Autoskala. Om du vill ha en mer *automatisk* upplevelse bör du använda elastiska pooler, vilka tillåter databaser att dela resurser i en pool utifrån enskilda databasbehov.
Det finns dock skript som kan hjälpa dig att automatisera skalbarheten för en enda Azure SQL Database. Mer information finns i [Använd PowerShell till att övervaka och skala en enskild SQL-databas](scripts/sql-database-monitor-and-scale-database-powershell.md).

Du kan ändra [DTU-tjänstens nivåer](sql-database-service-tiers-dtu.md) eller [vCore-egenskaper](sql-database-vcore-resource-limits-single-databases.md) när som helst med minimal stilleståndstid för ditt program (ofta under fyra sekunder). För många företag och appar räcker det att kunna skapa databaser och reglera prestanda för fristående databaser upp eller ner efter behov, speciellt om användningsmönstren är relativt förutsägbara. Men om du har oförutsägbara användningsmönster, kan det vara svårt att hantera kostnader och din affärsmodell. I det här scenariot använder du en elastisk pool med ett visst antal eDTU: er som delas mellan flera databaser i poolen.

![Introduktion till SQL Database: DTU:er för enkla databaser efter nivå](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Alla tre varianter i Azure SQL Database erbjuder viss möjlighet att dynamiskt skala dina databaser:

- Med en [enda databas](sql-database-single-database-scale.md)kan du använda antingen [DTU](sql-database-dtu-resource-limits-single-databases.md) -eller [vCore](sql-database-vcore-resource-limits-single-databases.md) -modeller för att definiera maximalt antal resurser som ska tilldelas varje databas.
- En [hanterad instans](sql-database-managed-instance.md) använder [virtuella kärnor](sql-database-managed-instance.md#vcore-based-purchasing-model) -läge och gör att du kan definiera högsta processor kärnor och maximalt lagrings utrymme som allokeras till din instans. Alla databaser inom instansen delar resurserna som är allokerade till instansen.
- [Elastiska pooler](sql-database-elastic-pool-scale.md) gör att du kan definiera högsta resurs gräns per grupp av databaser i poolen.

Att initiera åtgärden för att skala upp eller ned i någon av varianter startar databas motor processen och flyttar den till en annan virtuell dator om det behövs. Processen att flytta databas motorn till en ny virtuell dator är en **online process** där du kan fortsätta att använda din befintliga Azure SQL Database-tjänst medan processen pågår. När mål databas motorn är fullständigt initierad och redo att bearbeta frågorna växlar anslutningarna [från källa till mål databas motorn](sql-database-single-database-scale.md#impact-of-changing-service-tier-or-rescaling-compute-size).

> [!NOTE]
> Du kan vänta en kort anslutnings rast när processen för att skala upp/skala ned är färdig. Om du har implementerat [omprövnings logik för vanliga tillfälliga fel](sql-database-connectivity-issues.md#retry-logic-for-transient-errors), ser du inte redundansväxlingen.

## <a name="alternative-scale-methods"></a>Alternativa skalnings metoder

Skalning av resurser är det enklaste och mest effektiva sättet att förbättra databasens prestanda utan att ändra antingen databas-eller program kod. I vissa fall kan även de högsta tjänst nivåerna, beräknings storlekarna och prestanda optimeringen inte hantera din arbets belastning på ett lyckat och kostnads effektivt sätt. I så fall har du följande ytterligare alternativ för att skala databasen:

- [Läs skalning](sql-database-read-scale-out.md) är en funktion som är tillgänglig i där du får en skrivskyddad replik av dina data där du kan köra krävande skrivskyddade frågor som rapporter. Skrivskyddad replik hanterar din skrivskyddade arbets belastning utan att påverka resursanvändningen på din primära databas.
- [Database horisontell partitionering](sql-database-elastic-scale-introduction.md) är en uppsättning tekniker som du kan använda för att dela upp data i flera databaser och skala dem oberoende av varandra.

## <a name="next-steps"></a>Nästa steg

- Information om hur du kan förbättra databasens prestanda genom att ändra databas koden finns i [hitta och tillämpa prestanda rekommendationer](sql-database-advisor-portal.md).
- Information om hur du kan optimera databasen med inbyggd databas information finns i [Automatisk justering](sql-database-automatic-tuning.md).
- Information om Läs skalbarhet i Azure SQL Database-tjänsten finns i så här använder du [skrivskyddade repliker för att belastningsutjämna skrivskyddade arbets belastningar för frågor](sql-database-read-scale-out.md).
- Information om en databas-horisontell partitionering finns i [skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md).
