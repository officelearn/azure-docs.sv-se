---
title: Azure SQL Database Azure Fallstudie - Daxko/CSI | Microsoft Docs
description: Lär dig mer om hur Daxko/CSI använder SQL-databas att påskynda dess utvecklingscykeln och för att förbättra sin kundtjänst och prestanda
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: ee06039242afe5b5e92cf2fecc4c3fa9bff0b78b
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214377"
---
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI används Azure att påskynda dess utvecklingscykeln och för att förbättra sin kundtjänst och prestanda
![Daxko/CSI-logotyp](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Daxko/CSI programvara inför en utmaning: sin kundbas med lämplighet och fritid Datacenter var växer snabbt, tack vare framgången för dess omfattande enterprise-programvarulösning, men att hålla igång med IT-infrastruktur behov för den växande kunden Base testning företagets IT-personal. Företaget har allt mer begränsade av ökande operations kostnader, särskilt för att hantera växande databaserna. Den overhead åtgärder ännu värre klippa ut till Utvecklingsresurser för nya initiativ, t.ex. nya mobility-funktioner för företagets programvara.

Enligt David Molina, Director produktutveckling på Daxko/CSI, tillhandahållna Azure CSI program med modellen platform as a service (PaaS) som det behövs för att förenkla hanteringen av databasen, öka skalbarheten och frigöra resurser för att fokusera på programvara i stället för ops. ”Azure SQL Database har ett bra alternativ för oss. Inte behöva bekymra dig om hur du underhåller en SQL Server, ett failover-kluster och alla andra infrastrukturen måste var perfekt för oss ”.

CSI programvara måste en personalstyrka bara två att hantera över 600 kunddatabaser sedan migrerar till Azure. Företaget använder Azure SQL Database elastiska pooler för att flytta kunddatabaser baserat på storleken och behöver.

Molina fortsätter ”våra kunder tyckte ändringen direkt. Innan du elastiska pooler kan hade de ibland tidsgränser och andra problem under burst-perioder. Med Azure elastiska pooler, kan de öka användningen efter behov och använda programvaran utan problem ”.

Förutom att förbättra prestandan för kunderna, Azure elastiska pooler frigjort CSI programvaruresurser för att fokusera på att utveckla nya tjänster och funktioner i stället för att hantera åtgärder och hantering. Resurserna IT hjälpte CSI programvara förbättra dess erbjudande, SpectrumNG för att engagera gymmet medlemmar, förbättra personaleffektiviteten, enterprise software och ge personal och medlemmar mobil åtkomst för interaktiva aktiviteter och aviseringar i realtid.

Azure har också hjälpte CSI programvara snabbare och förbättrad utvecklings- och kvalitetssäkring (kvalitetskontroll) cykel genom att aktivera automatiseringsalternativ. Med företagets Azure implementering, kan build chefer packa upp komponenter genom att klicka på en knapp. Eftersom Molina beskriver ”som en del av versionen cykel, QA kan nu distribuera till en testmiljö i Azure, vilket nära efterliknar stacken produktion. Vi kan distribuera versioner direkt till vår utvecklingsmiljö för att se ändringarna. Det är en stor fördel för oss, eftersom vi inte har paritet för testning innan dess ”.

## <a name="offloading-to-the-cloud"></a>Avlastning till molnet
Innan du migrerar till molnet, hade CSI programvara har byggt en egen infrastruktur för flera innehavare i ett lokalt datacenter i Houston (USA). Som företaget utökad inför ökade growing framgång från inköp, etablering och underhåll av alla maskinvara och programvara som behövs för sina kunder. IT personal för att hantera åtgärder som blev en annan flaskhals som ledde till en långsammare genom att etablera nya resurser och lansera nya tjänster till kunder.

CSI programvara undersökt molnalternativ för att ta bort den overhead så att den kan fokusera på koden, i stället för driften. Företaget identifierade att många av de främsta molnleverantörerna erbjuder infrastruktur som tjänst (IaaS) lösningar som fortfarande kräver en stor IT-avdelning för att hantera IaaS-stack. I slutändan fastställt CSI programvara att Azure PaaS-lösning har bäst för sina behov. Molina förklarar ”Azure hämtar maskin- och programvaran undan den, så vi kan fokusera på våra programvaruerbjudanden, samtidigt som våra IT-kostnaderna”.

## <a name="making-the-transition-to-azure"></a>Övergången till Azure
När du har valt Azure för dess PaaS-lösning, började CSI programvara migrera dess backend-infrastruktur och databaser till molnet. Innan Azure, SpectrumNG kunderna tvungna att installera ett klientprogram som förmedlas med en Windows Communication Foundation (WCF)-tjänst på serverdelen. Enligt Molina, ”även om vissa kunder finns allt innehåll i sina egna datacenter, vi har byggt i produkten som ska vara med flera klienter. Vi hanteras allt i ett datacenter i Houston (USA), med hjälp av SQL Server som dataarkiv.

”Vår produkt som erbjuder också ingår en medlem webbservergrupper webbportal (skriven i ASP.net), som har utformats för att vara vit etikett som motsvarar kundens webbnärvaro och ett SOAP-API för online-sidor och någon tredje parts-integrering”.

Migrering till molnet inte att ta lång tid för arkitektur. Enligt Molina, ”flesta av Microsofts arbete behandlas ändra hur vi läsa konfigurationsinformation för filen, en centraliserad anslutningssträngen ändringar, och automatisera förpackningen, ladda upp och distribuera vår versioner”.

För att utveckla build-automation används CSI programvara tekniker Azure PowerShell och REST API: er för att skapa paket och överföra dem till en mellanlagringsmiljö för version varje natt.
Övergripande övergången till en Azure-moln-baserad distribution gick snabbt och smidigt för CSI programvara IT-teamet. Molina förklarar ”i alla, vi hade en beta-miljö i molnet inom tre till fyra veckor att ta sig an projektet. Det var en överraskande vinster för oss ”.

När du konfigurerar och testar miljön, började CSI programvara migrera kunder. För kunder som använder redan CSI programvara som är värd, var övergången smidig. För kunder som migrerar från en lokal distribution, migrering till molnet tog lite extra tid, men har fortfarande främst smärtfri för kunder och CSI programvara.

För nya kunder, CSI programvaras IT-personal använder du följande process integrera dem till Azure:

1. Azure PowerShell-skript som används för att skapa en ny databas för kunden. alla kunder börjar på en premium-nivån för att se till att tillräckligt med inledande genomströmning för övergången.
2. Om det är möjligt använder CSI programvaran Azure SQL-Migreringsguide för att flytta befintliga data till en Azure SQL Database-instans.
3. Slutligen används Microsoft SQL Server Integration Services (SSIS) att stämma av avvikelser i data eller för att rensa alla data som krävs.

Idag finns omkring 99% av CSI Software kunder i Azure, i fyra regionala datacenter (norra centrala, södra centrala, Öst och västra). Genom att ha datacenter i varje kunds geografisk region sparas svarstid till ett minimum.

## <a name="azure-elastic-pools-free-up-it-resources"></a>Azure elastiska pooler Frigör IT-resurser
Flera funktioner i Azure har hjälpt CSI programvara SKIFT från att infrastrukturen och åtgärder som fokuserar till funktionen och utveckling fokus. Kanske har den största fördelen från elastiska pooler.

CSI Software erbjuder för närvarande cirka 550 databaser till kunder. Det var svårt att hantera många databaser inom en nivå struktur innan elastiska pooler. OPS-hanterare var tvungen att tilldela prestandanivåer utifrån burst-behoven hos kunder som krävde stora IT-resurs overhead. Med elastiska pooler kan chefer tilldela klienterna en premium- eller standard-pool efter behov, och flytta kunder baserat på storleken och behöver. Kunder tyckte effekterna av elastiska pooler nästan omedelbart. kunder hade tidsgränser och andra problem under burst-usage perioder före elastiska pooler, men med elastiska pooler kunder kan uppleva aktivitetstoppar efter behov och de kan fortsätta att använda SpectrumNG utan problem.

## <a name="azure-active-geo-replication-accelerates-reporting"></a>Azure aktiv geo-replikering kan införas snabbare rapportering
Flera CSI programvara kunder även utnyttjar Azure aktiv geo-replikering. Med aktiv geo-replikering, kan upp till fyra läsbara sekundära databaser konfigureras i samma eller olika datacenterregioner. CSI programvaran använder aktiv geo-replikering på två sätt: först sekundära databaser är tillgängliga när det gäller ett strömavbrott i ett datacenter eller det inte går att ansluta till den primära databasen. och andra sekundära databaser kan läsas och kan användas för att avlasta skrivskyddade arbetsbelastningar som rapportering av jobb. Vissa CSI programvara kunder använda den här förmånen för att påskynda reporting arbetsflöden.

## <a name="csi-software-application-logic-and-architecture"></a>CSI programvara programlogiken och arkitektur
SpectrumNG använder web-roller. Eftersom programmet är flera innehavare, används en WCF-tjänst för att hantera den första anslutningsbegäran från våra kunder. Eftersom Molina säger identifierar ”begäran varje kund, som sedan kan vi skapa en anslutningssträng ut till sina databaser att göra allt vi behöver göra”.

Web-nivån av sin tjänst utnyttjar CSI programvara Azure automatisk skalning, baserat på dag och tid. Tillgängliga resurser ökas automatiskt för att hantera högre användning under kontorstid, enligt tidszonen för varje regionala datacenter. Resurser även är inställda att skala ned helger, när kundernas behov är lägre.

![Daxko/CSI-arkitektur](./media/sql-database-implementation-daxko/figure1.png)

Bild 1. En cloud services-arbetsroll ritar strukturerade data från Azure SQL Database och delvis strukturerade data från table storage. SpectrumNG användarna samverkar med att data via en cloud services web-roll.

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>Med web apps och en webbplan nivå för mobila appar
Använda Azure SQL Database frigjort resurser för CSI programvara för att aktivera nya initiativ, inklusive en komplett mobil plattform utifrån ett anpassat API som finns i Azure web apps. Plattformen gör det möjligt för gymmet medlemmar och personal att använda mobila enheter för att kontrollera scheman, boka klasser och ta emot meddelanden.

Plattformen använder tjänstorienterad arkitektur (SOA) för att ta en enskild komponent, t.ex. ett kassasystem (POS) eller ett försäljningssystem – flytta den direkt till en annan webbplan och sedan skapa en tjänst för att komponenten och lämna allt annat på den ursprungliga webbplan. Den här möjligheten ger CSI programvara enorm flexibilitet, och det hjälper dig att hålla nere kostnaderna.

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Azure kan CSI programvara utvecklare fokusera på appar och tjänster
Azure SQL-databasen inte bara en stor fördel för SpectrumNG kunder, som få snabb och tillförlitlig tjänst, det är också en stor fördel för CSI programvara IT-personal och utvecklare. Genom att avlasta ops till Azure i molnet, CSI programvara minskat sina kostnader för resurser och infrastruktur, accelerated avsevärt sin utvecklingscykler och inte längre måste för micromanage databaser för att optimera prestandan för sina klienter.

## <a name="more-information"></a>Mer information
* Läs mer om Azure elastiska pooler i [elastiska pooler](sql-database-elastic-pool.md).
* Läs mer om Databasverktyg och elastisk skalning i [elastiska Databasverktyg och elastisk skalning](sql-database-elastic-scale-get-started.md).
* Mer information om hur du migrerar en SQL Server-databas finns [migrera en SQL Server-databas till Azure](sql-database-cloud-migrate.md).
* Mer information om aktiv geo-replikering finns [aktiv geo-replikering](sql-database-geo-replication-overview.md).
* Läs mer om Azure Service Bus i [Azure Service Bus](https://azure.microsoft.com/services/service-bus/).
* Läs mer om automatisk skalning i [skalning av cloud services](../cloud-services/cloud-services-how-to-scale-portal.md).

