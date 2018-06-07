---
title: Azure SQL-databas Azure Fallstudie - Daxko/CSI | Microsoft Docs
description: Lär dig mer om hur Daxko/CSI använder SQL-databas som påskyndar dess utvecklingscykeln och förbättra dess kundtjänst och prestanda
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: b7c1d4f1b8ca0f8899ebff8178a481c7dd1d5779
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648895"
---
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI används Azure för att öka takten dess utvecklingscykeln och förbättra dess kundtjänst och prestanda
![Daxko/CSI-logotyp](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Daxko/CSI programvara inför en utmaning: dess kundbas för lämplighet och fritid växer snabbt, tack vare genomförandet av dess omfattande företagsprogram lösning, men behåller med IT-infrastruktur behov för växande kunden Base testning företagets IT-personal. Företaget har allt mer begränsad av ökande operations kostnader, särskilt för att hantera växande databaserna. Arbetet som operations värre, klippa ut till Utvecklingsresurser för nya initiativ som nya mobilitet för företagets program.

Enligt David Molina, Director av produktutveckling på Daxko/CSI medföljer Azure CSI programvara plattform som en tjänst (PaaS) modellen som krävdes för att förenkla hanteringen av databasen, öka skalbarheten och frigöra resurser för att fokusera på programvara i stället för ops. ”Azure SQL Database har ett bra alternativ för oss. Inte behöver bry dig om att underhålla en SQL Server, ett failover-kluster och alla andra infrastrukturen måste var perfekt för oss ”.

Sedan migrera till Azure måste CSI programvara en driftspersonal bara två att hantera över 600 kunddatabaser. Företaget använder Azure SQL Database elastiska pooler för att flytta kunddatabaser baserat på storleken och behöver.

Molina fortsätter ”våra kunder tyckte ändringen direkt. Innan du elastiska pooler hade de ibland tidsgränser och andra problem under burst perioder. Med Azure elastiska pooler de burst efter behov och använda programvaran utan problem ”.

Förutom att förbättra prestanda för kunder, Azure elastiska pooler frigjort CSI programvara resurser för att fokusera på att utveckla nya tjänster och funktioner, i stället för att hantera åtgärder och hantering. Resurserna IT hjälpt CSI programvara förbättra enterprise programvaran erbjudande SpectrumNG att engagera gymmet medlemmar kan förbättra personal effektivitet och ge personal och medlemmar mobil åtkomst för interaktiva aktiviteter och meddelanden i realtid.

Azure har också hjälpt CSI programvara påskynda och förbättra utveckling och kvalitet säkerhet (QA) cykeln genom att aktivera automatiseringsalternativ. Med företagets Azure implementering, kan bygga chefer paketera komponenter klickar på en knapp. Eftersom Molina beskriver ”som en del av den versionen, QA kan nu distribuera till en testmiljö i Azure, vilket nära efterliknar våra produktions-stacken. Vi kan distribuera versioner direkt till vår utvecklingsmiljö för att se ändringarna. Som är en stor win för oss, eftersom vi inte hade paritet för testning innan ”.

## <a name="offloading-to-the-cloud"></a>Avlastning till molnet
Innan du flyttar till molnet, hade CSI programvara har skapat in sin egen multitenant infrastruktur i ett lokalt datacenter i Houston. Som företaget utökad inför ökande growing framgång från inköp, etablering och underhåll av alla maskin- och programvara som krävs för att stödja sina kunder. IT personal för att hantera åtgärder blev en annan flaskhals som ledde till en nedgång i etablera nya resurser och lansera nya tjänster till kunder.

CSI programvara undersökt molnalternativ för att ta bort den kostnader, så att den kan fokusera på koden, i stället för åtgärderna. Företaget identifierade att många av de översta molntjänstleverantörer erbjuder infrastruktur som en tjänst (IaaS) lösningar som behöver fortfarande en stora IT-personal för att hantera IaaS-stacken. I slutändan fastställt CSI programvara att Azure PaaS-lösningen var bäst för dess behov. Molina förklarar ”Azure hämtar maskin- och programvaran undan, så vi kan fokusera på vår programvara erbjudanden samtidigt minska vår IT-omkostnaderna”.

## <a name="making-the-transition-to-azure"></a>Att göra övergången till Azure
När du har valt Azure för dess PaaS-lösning, började CSI programvara migrera dess backend-infrastruktur och databaser till molnet. Innan Azure, SpectrumNG kunder behövs för att installera ett klientprogram som kommunicerat med en Windows Communication Foundation (WCF)-tjänst på serverdelen. Enligt Molina byggt ”även om vissa kunder finns allt innehåll i sina egna Datacenter vi ut produkten ska multitenant. Det finns allt innehåll i ett datacenter i Houston, använder SQL Server som datalager.

”Vår produkt erbjuder också med en medlem riktade webbportal (skrivs i ASP.net), som har utformats för att vara vit etikett att matcha kundens webben och ett SOAP-API som stöd för online-sidor och någon tredje parts-integrering”.

Migrering till molnet inte ta lång tid för måldatorns arkitektur. Enligt Molina ”merparten av behandlas ändra hur vi läsa konfigurationsinformation för filen, en centraliserad anslutningssträngen ändring och automatisera paketeringen, överföring och distribution av våra versioner”.

För att utveckla build-automation för CSI program Azure PowerShell och REST API: er skapa paket och överför dem till mellanlagringsmiljön för versionen varje natt.
Övergripande övergången till Azure-distribution molnbaserade gick smidigt och snabbt för CSI programvara IT-teamet. Molina förklarar ”i alla, vi hade en beta-miljö i molnet inom tre till fyra veckor efter tar i projektet. Som har en konstigt win för oss ”.

När du konfigurerar och testar miljön, CSI programvara började migrera kunder. För kunder som redan använder CSI programvara värd var övergången nästan sömlös. För kunder som migrerar från en lokal distribution, migrering till molnet tog vissa extra tid, men var fortfarande främst smärta kostnadsfri för kunder och CSI programvara.

För nya kunder, CSI programvaras IT-personal använda följande process för inbyggd dem till Azure:

1. Azure PowerShell-skript som används för att få igång en ny databas för kunden. alla kunder starta på en premiumnivån för att säkerställa tillräckligt med inledande genomströmning för övergången.
2. När det är möjligt använder CSI programvaran Azure SQL-Migreringsguiden för att flytta befintliga data till en Azure SQL Database-instans.
3. Slutligen används Microsoft SQL Server Integration Services (SSIS) för att stämma av skillnader i data eller rensa alla data som krävs.

Idag finns om 99 procent av CSI Software kunder i Azure, i fyra regionala datacenter (norra centrala, södra centrala, Öst och Väst). Genom att datacenter i geografisk region för varje kund, sparas svarstid till ett minimum.

## <a name="azure-elastic-pools-free-up-it-resources"></a>Azure elastiska pooler Frigör IT-resurser
Flera funktioner i Azure har hjälpt CSI programvara SKIFT från att infrastrukturen och åtgärder som fokuserar blir funktion och utveckling fokuserar. Den största fördelen har kanske från elastiska pooler.

CSI programvara innehåller för närvarande cirka 550 databaser för kunder. Det var svårt att hantera så många databaser i en nivå struktur innan elastiska pooler. OPS chefer var tvungen att tilldela prestandanivåer baserat på kundernas som krävde betydande IT-resurs omkostnader burst behov. Med elastiska pooler kan chefer tilldela klienterna en premium eller Standardpool efter behov, och sedan flytta kunder baserat på storleken och behöver. Kunder tyckte effekterna av elastiska pooler nästan omedelbart. kunder har tidsgränser och andra problem under burst-användning perioder innan elastiska pooler, men med elastiska pooler kunder kan drabbas av aktiviteten belastning vid behov och de kan fortsätta att använda SpectrumNG utan problem.

## <a name="azure-active-geo-replication-accelerates-reporting"></a>Azure active geo-replikering accelererar reporting
Flera CSI programvara kunder också dra nytta av Azure active geo-replikering. Med aktiv geo-replikering, kan upp till fyra läsbara sekundära databaser konfigureras i samma eller olika datacenterregioner. CSI programvara använder aktiv geo-replikering på två sätt: först sekundära databaser är tillgängliga när det gäller ett avbrott i datacenter eller oförmåga att ansluta till den primära databasen. och andra sekundära databaser kan läsas och kan användas för att avlasta skrivskyddade arbetsbelastningar som rapportering jobb. Vissa kunder CSI programvara använda detta för att öka takten reporting arbetsflöden.

## <a name="csi-software-application-logic-and-architecture"></a>CSI programvara programlogiken och arkitektur
SpectrumNG använder webbroller. Eftersom programmet är flera innehavare, används en WCF-tjänst för att hantera den första anslutningsbegäran från kunder. Eftersom Molina tillstånd identifierar ”begäran varje kund, vilket gör att vi kan skapa en anslutningssträng ut till sina databaser att göra allt vi behöver göra”.

För webbnivå av sin tjänst utnyttjar CSI program Azure automatisk skalning, baserat på dag och tid. Tillgängliga resurser ökas automatiskt för senare användning under kontorstid, enligt tidszonen för varje regionala datacenter. Resurser även är inställda att skala helger, när kundens behov är lägre.

![Daxko/CSI-arkitektur](./media/sql-database-implementation-daxko/figure1.png)

Bild 1. En arbetsroll för cloud services ritar strukturerade data från Azure SQL Database och halvstrukturerade data från tabellagring. SpectrumNG användarna samverkar med att webbroll services-data via ett moln.

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>Med hjälp av webbprogram och en nivå med web-plan för mobila appar
Använda Azure SQL Database frigjort resurser för CSI programvara för att aktivera nya initiativ, inklusive en komplett mobil plattform baserat på en anpassad API finns i Azure-webbappar. Plattformen gör gymmet medlemmar och personal att använda mobila enheter för att kontrollera scheman bok klasser och ta emot meddelanden.

Plattformen använder tjänstorienterad arkitektur (SOA) för att ta en enda komponent, som ett kassan system (POS) eller ett system för försäljning – flytta den direkt till en annan webbserver plan och sedan få igång en tjänsten för att stödja komponent, och lämna allt annat på den den ursprungliga web plan. Den här möjligheten ger CSI programvara enorm flexibilitet och det hjälper håll nere kostnaderna.

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Azure låter CSI programvara utvecklare att fokusera på appar och tjänster
Azure SQL-databas är inte bara en stor fördel för SpectrumNG kunder som få snabb, tillförlitlig tjänsten, utan även en stor win för CSI programvara IT-personal och utvecklare. Genom att avlasta ops till Azure i molnet, CSI programvara minskar dess kostnader för resurser och infrastruktur, snabbare betydligt sin utvecklingscykler och inte längre måste för micromanage databaser för att optimera prestanda för sina klienter.

## <a name="more-information"></a>Mer information
* Läs mer om Azure elastiska pooler i [elastiska pooler](sql-database-elastic-pool.md).
* Mer information om Databasverktyg och elastiska skalning finns [elastisk Databasverktyg och elastiska skalning](sql-database-elastic-scale-get-started.md).
* Mer information om hur du migrerar en SQL Server-databas finns i avsnittet [migrera en SQL Server-databas till Azure](sql-database-cloud-migrate.md).
* Mer information om aktiv geo-replikering finns [aktiv geo-replikering](sql-database-geo-replication-overview.md).
* Läs mer om Azure Service Bus i [Azure Service Bus](https://azure.microsoft.com/services/service-bus/).
* Läs mer om Autoskala i [skalning molntjänster](../cloud-services/cloud-services-how-to-scale-portal.md).

