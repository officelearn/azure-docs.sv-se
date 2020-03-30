---
title: Skala resurser
description: I den här artikeln beskrivs hur du skalar databasen genom att lägga till eller ta bort allokerade resurser.
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
ms.openlocfilehash: c4366b2718271b1e27325e6946c5016e9230cea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76835920"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Dynamisk skala databasresurser med minimal stilleståndstid

Med Azure SQL Database kan du dynamiskt lägga till fler resurser i databasen med minimal [stilleståndstid.](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/) Det finns dock en switch över period där anslutningen går förlorad till databasen under en kort tid, vilket kan minskas med hjälp av logik för återförsök.

## <a name="overview"></a>Översikt

När efterfrågan på din app ökar från en handfull enheter och kunder till miljoner skalas Azure SQL Database i farten med minimal stilleståndstid. Skalbarhet är en av de viktigaste egenskaperna hos PaaS som gör att du dynamiskt kan lägga till mer resurser till din tjänst när det behövs. Med Azure SQL Database kan du enkelt ändra resurser (CPU-kraft, minne, I/O-dataflöde och lagring) som allokerats till dina databaser.

Du kan minska prestandaproblem på grund av ökad användning av ditt program som inte kan åtgärdas med hjälp av indexerings- eller frågereaskrivningsmetoder. Genom att lägga till fler resurser kan du snabbt reagera när databasen når de aktuella resursgränserna och behöver mer kraft för att hantera den inkommande arbetsbelastningen. Azure SQL Database kan du också skala ner resurserna när de inte behövs för att sänka kostnaden.

Du behöver inte oroa dig för att köpa maskinvara och ändra underliggande infrastruktur. Skalningsdatabas kan enkelt göras via Azure-portalen med hjälp av ett skjutreglage.

![Skala databasprestanda](media/sql-database-scalability/scale-performance.svg)

Azure SQL Database erbjuder den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) och den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).

- Den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) erbjuder en blandning av beräknings-, minnes- och IO-resurser på tre tjänstnivåer för att stödja lätta till tungviktsdatabasarbetsbelastningar: Basic, Standard och Premium. Det finns prestandanivåer inom varje nivå med en blandning av dessa resurser, och du kan lägga till ytterligare lagringsresurser till dessa.
- Med den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) kan du välja antalet virtuella kärnor, mängden eller minnet samt mängden och lagringshastigheten. Den här inköpsmodellen erbjuder tre tjänstnivåer: Allmänt syfte, Affärskritisk och Hyperskala.

Du kan skapa din första app på en liten, enskild databas till en låg kostnad per månad på tjänstnivån Basic, Standard eller General Purpose och sedan när som helst ändra dess tjänstnivå manuellt eller programmässigt till premium- eller affärskritisk tjänstnivå för att uppfylla behov av din lösning. Du kan justera prestandan utan driftavbrott för din app eller dina kunder. Dynamisk skalbarhet gör att databasen transparent kan svara på snabbt förändrade resurskrav och gör det möjligt för dig att endast betala för de resurser som du behöver, när du behöver dem.

> [!NOTE]
> Dynamisk skalbarhet skiljer sig från autoskalning. Automatisk skalning är när en tjänst skalas automatiskt baserat på villkor, medan dynamisk skalbarhet möjliggör manuell skalning med minimal stilleståndstid.

Enkla Azure SQL-databaser stöder manuell dynamisk skalbarhet, men inte Autoskala. Om du vill ha en mer *automatisk* upplevelse bör du använda elastiska pooler, vilka tillåter databaser att dela resurser i en pool utifrån enskilda databasbehov.
Det finns dock skript som kan hjälpa till att automatisera skalbarheten för en enda Azure SQL-databas. Mer information finns i [Använd PowerShell till att övervaka och skala en enskild SQL-databas](scripts/sql-database-monitor-and-scale-database-powershell.md).

Du kan ändra [DTU-tjänstens nivåer](sql-database-service-tiers-dtu.md) eller [vCore-egenskaper](sql-database-vcore-resource-limits-single-databases.md) när som helst med minimal stilleståndstid för ditt program (ofta under fyra sekunder). För många företag och appar räcker det att kunna skapa databaser och reglera prestanda för fristående databaser upp eller ner efter behov, speciellt om användningsmönstren är relativt förutsägbara. Men om du har oförutsägbara användningsmönster, kan det vara svårt att hantera kostnader och din affärsmodell. I det här fallet använder du en elastisk pool med ett visst antal eDFUs som delas mellan flera databaser i poolen.

![Introduktion till SQL Database: DTU:er för enkla databaser efter nivå](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Alla tre smakerna av Azure SQL Database erbjuder vissa möjligheter att dynamiskt skala dina databaser:

- Med en [enda databas](sql-database-single-database-scale.md)kan du använda antingen [DTU-](sql-database-dtu-resource-limits-single-databases.md) eller [vCore-modeller](sql-database-vcore-resource-limits-single-databases.md) för att definiera maximal mängd resurser som ska tilldelas varje databas.
- En [hanterad instans](sql-database-managed-instance.md) använder [vCores-läge](sql-database-managed-instance.md#vcore-based-purchasing-model) och gör att du kan definiera maximala CPU-kärnor och maximalt lagringsutrymme som allokerats till din instans. Alla databaser i instansen delar de resurser som allokerats till instansen.
- [Med elastiska pooler](sql-database-elastic-pool-scale.md) kan du definiera maximal resursgräns per grupp av databaser i poolen.

Initiera skala upp eller skala ner åtgärder i någon av de smaker skulle starta databasmotorprocessen och flytta den till en annan virtuell dator om det behövs. Att flytta databasmotorprocessen till en ny virtuell dator är **onlineprocess** där du kan fortsätta använda din befintliga Azure SQL Database-tjänst medan processen pågår. När måldatabasmotorn är helt initierad och redo att bearbeta frågorna, kommer anslutningarna att [växlas från källa till måldatabasmotor](sql-database-single-database-scale.md#impact). 


> [!NOTE]
> Du kan förvänta dig en kort anslutningsbrytning när skala upp/skala ned-processen är klar. Om du har implementerat [logik för återförsök för standardtransienta fel](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)kommer du inte att märka redundansen.

## <a name="alternative-scale-methods"></a>Alternativa skalningsmetoder

Att skala resurser är det enklaste och mest effektiva sättet att förbättra databasens prestanda utan att ändra databasens eller programkoden. I vissa fall kanske inte ens de högsta tjänstnivåerna, beräkningsstorlekarna och prestandaoptimeringarna hanterar din arbetsbelastning på ett framgångsrikt och kostnadseffektivt sätt. I så fall har du dessa ytterligare alternativ för att skala databasen:

- [Lässkalning](sql-database-read-scale-out.md) är en tillgänglig funktion där du får en skrivskyddad replik av dina data där du kan köra krävande skrivskyddade frågor som rapporter. Skrivskyddad replik hanterar din skrivskyddade arbetsbelastning utan att påverka resursanvändningen i den primära databasen.
- [Databassharding](sql-database-elastic-scale-introduction.md) är en uppsättning tekniker som gör att du kan dela upp dina data i flera databaser och skala dem oberoende av dem.

## <a name="next-steps"></a>Nästa steg

- Information om hur du förbättrar databasens prestanda genom att ändra databaskoden finns i [Hitta och tillämpa prestandarekommendationer](sql-database-advisor-portal.md).
- Information om hur du låter inbyggd databasinformation optimera databasen finns i [Automatisk justering](sql-database-automatic-tuning.md).
- Information om Lässkalning i Azure SQL Database-tjänsten finns i hur du [använder skrivskyddade repliker för att läsa in arbetsbelastningar för skrivskyddad fråga](sql-database-read-scale-out.md).
- Information om en databassharding finns i [Skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md).
