---
title: Azure SQL-databas Azure Fallstudie - GEP | Microsoft Docs
description: "Lär dig mer om hur GEP använder SQL-databas för att nå globala kunder och uppnå större effektivitet"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: ae8bcb10-c251-4bac-b666-10a253918583
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: bced4e04f541dde58410e25fe0c3aa5493a5e5fd
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="azure-gives-gep-global-reach-and-greater-efficiency"></a>Azure ger GEP globalt omfattande och större effektivitet
![GEP-logotyp](./media/sql-database-implementation-gep/geplogo.png)

GEP levererar programvaror och tjänster som gör inköp företag att maximera deras inverkan på sina företag operations strategier och finansiella resultaten. Förutom konsulter och hanterade tjänster erbjuder företaget SMART av GEP®, en molnbaserad omfattande inköp-program-plattform. Dock GEP råkade ut för begränsningar försöker stödja lösningar som SMARTKORT av GEP med egna lokala datacenter: investeringar som behövs har brant och regelkrav i andra länder skulle gjort nödvändiga investeringar mer överväldigande fortfarande. Genom att flytta till molnet har GEP frigjort IT-resurser, så att den kan oroa dig mindre om IT-avdelningen och fokusera på att utveckla nya källor med värdet för sina kunder över hela världen.

## <a name="expanding-services-and-growth-by-using-azure"></a>Expandera tjänster och tillväxt med hjälp av Azure
SMARTKORT som GEP kunder gillar plattformens funktioner och enkel användning. kunder kan hantera sina processer från var som helst, när som helst och på alla enheter, bärbara datorer, Tablet PC eller telefon. Genom att flytta till Microsoft Azure har GEP kunna hantera dess snabb ökning och risken att expandera till nya marknader. Enligt GEP'S VP of Technology, Dhananjay Nagalkar har ”Microsoft Azure spelat en viktig roll i GEPS lyckades genom att låta oss att snabbt skala tjänster med flexibilitet och genom att tillhandahålla regionala datacenter som hjälper oss passar kunderna globala regelverk”.

## <a name="the-limitations-of-a-do-it-yourself-datacenter"></a>Begränsningar för själv datacenter
I 2013 identifieras GEP utfyllnadstecken de behövs ett sätt att se till att skalbarhet och prestanda som de vuxen kunden bas. Nagalkar förklaras, ”för att uppfylla denna begäran med våra befintliga Datacenter kan vi fick du expandera vår infrastruktur och IT-resurser avsevärt. Investering och tidsintervall för som skulle ha varit enorma ”. Lokala fysiska och virtuella datorer som kräver omfattande konfiguration, hantering, skalning, säkerhetskopiering och korrigering med en hastighet som skulle ha varit kostsamt för GEP. Molnlösningar erbjuder å andra sidan enkelhet och bekvämlighet som aktiverad GEP att fokusera mer på utveckling i stället för att hantera stora – och växande – IT-avdelningen. Nagalkar visste att GEP kan minska dess infrastruktur inköp, konfiguration och hantering av arbetet genom att migrera till molnet.

GEP behövs också ett sätt att lösa regelverk barriärer som hålls utanför vissa globala marknader. För många av GEPS potentiella europeiska kunder kräver regelefterlevnad att data som lagras i sina lokala geografiska områden. Men den hade inte varit praktiska för GEP att bygga ut flera datacenter. ”Omfattande infrastrukturinvesteringar och IT arbete kostnader har stor betydelse marginaler” enligt Nagalkar. ”Därför kan vi var faktiskt tvungen att aktivera direkt potentiella kunder som krävs lokalt lagrade data”.

Nagalkar visste att en molnlösning kanske svaret på den här dilemma. Om GEP kan flyttas till en molntjänstleverantör med en global närvaro krävs det bättre kan uppfylla sina kunder regelverk och prestanda genom att lagra data närmare till kundernas fysiska platser.

## <a name="finding-smooth-skies-in-the-cloud"></a>Hitta smooth skies i molnet
Nagalkar och teamet utforskade du flera molnalternativ för, men de flesta har infrastructure-as-a-service (IaaS)-baserade lösningar som skulle ha krävt GEP ska investera kraftigt i IT-resurser för att konfigurera och hantera tjänsten. Azure plattform som en tjänst (PaaS) lösningen-visade sig vara mycket bättre passar.

”Med Azure, GEP inte behöver åtgärda databashantering, konfiguration av virtuell dator, uppdatering eller andra infrastruktur-hanteringsåtgärder” anges Nagalkar. ”Vi kan istället fokusera våra resurser på vad vi gör bäst: utnyttjar vår expertis i inköp att skriva program som verkligen ger resultat för våra kunder”. 

Gå till Azure har i själva verket aktiverat GEP att minska sina IT-driftspersonal medan samtidigt aktiveras bättre funktioner för kunder.

Genom att utnyttja Azure-Datacenter världen, utöka GEP enkelt dess reach över Europa och Asien. De globala Datacenter aktivera GEP att skala med flexibilitet och för att uppfylla kundernas behov för lokalt lagrade data som minskar svarstider och uppfyller krav.

## <a name="smart-by-gep-architecture"></a>SMART av GEP-arkitektur
GEP uppbyggd SMART av GEP från grunden på Azure. En kritisk skälet för GEP var större skalbarhet, minus avbrottstid och minskade underhållskostnader GEP uppleva med Azure SQL Database jämfört med vad GEP kan uppnå lokalt. När flyttas till molnet identifierade GEP nya möjligheter för utveckling i molnet, som snabb prototyper och lean tekniker för att bättre svara på kundernas behov. Utveckla i Azure kan GEP göra direkt med programvarulicenser headaches som dess utvecklare kan köras i lokalt. Kärnan i SMART av GEP är Azure SQL Database, även om GEP använder många andra Azure-tjänster för att snabbt och enkelt fortsätta att förbättra SMART av GEP.

![SMART av GEP arkitektur](./media/sql-database-implementation-gep/figure1.png) bild 1. SMART av GEP-arkitektur

## <a name="structured-data"></a>Strukturerade data
Hjärtat i SMART av GEP program är Azure SQL Database-instanser power inköp-företagshantering lösningen. När GEP bakåtkompilerade SMART av GEP, såg Azure SQL Database som ett perfekt val för arkitekturen som gör att företag kan få största möjliga dataskydd och uppfyller dess krav. GEP används av flera lager av dataskydd som Azure SQL Database-erbjudanden, inklusive:

* Kryptera data i vila via transparent datakryptering.
* Skydda autentiseringen genom att integrera Azure SQL Database med Azure Active Directory.
* Begränsa åtkomsten till en lämplig delmängd av data med säkerhet på radnivå.
* Maskering av data i realtid i principer.
* Spårar databashändelser via Azure SQL Database Auditing.

> ”Vi kan använda alla dessa alternativ utan att göra några viktiga koden och med minimal påverkan på prestanda” sägs Nagalkar.
> 
> 

Med hjälp av Azure SQL Database har GEP automatiskt större katastrofåterställning funktioner än den kan ha ekonomiskt bakåtkompilerade lokalt på grund av feltolerans funktionerna som är inbyggda i Azure SQL Database. GEP använder aktiv geo-replikering-funktionen i Azure SQL Database, tillsammans med flera aktiva, kan läsas och online sekundära repliker (alltid på Tillgänglighetsgrupper) i olika geografiska regioner, för att bilda par med hög tillgänglighet. Replikera SMART av GEP innebär data över regioner att, vid en region hela katastrof GEP kan enkelt återställa kundinformation med en minsta återställningspunkt återställningspunktmål och återställningstiden mål för Återställningstid.

Varje SMARTKORT av GEP kunden har två förekomster i Azure SQL Database: en för online transaktionsbearbetning (OLTP) och en för analys (till exempel kund tillbringar och rapportera analys). Azure SQL Database elastiska pooler aktivera GEP enkelt hantera tusentals databaser globalt för att hantera oförutsägbart databas-resurskrav. Elastiska pooler ger en möjlighet för GEP så att kunddatabaser kan skala vid behov utan att överbelasta eller under-etablering, samtidigt som GEP också att kontrollera kostnader. Eftersom detta är en PaaS-tjänst hämtar GEP dessutom alla nya Azure SQL Database-funktioner med automatiska uppgraderingar.

## <a name="unstructured-and-semi-structured-data"></a>Ostrukturerade och halvstrukturerade data
Vissa SMART av GEP kundinformation måste dock mindre strikt strukturerade lagringsplatsen. Den här typen av data använder GEP Azure Blob storage, Azure Table Storage och Azure Redis-Cache. Azure Blob storage inrymmer bifogade SMARTKORT från GEP användare överför till programmet. Det är också där smarta av GEP butiker statiskt innehåll, till exempel sammanhängande formatmallar (CSS) och JavaScript-filer.

GEP lagrar kunden vända data, till exempel SMARTKORT av GEP loggdata i Azure Table Storage, som ger GEP effektivt obegränsad kostnadseffektiv lagring och hämtning av snabb gånger utan att behöva bekymra dig om att ställa in ett schema för data. GEP använder Azure Redis-Cache för master-cache.

## <a name="authentication-and-routing"></a>Autentisering och Routning
Azure Access Control Service (ACS) ger SMART av GEP användare med en mängd olika alternativ för att logga in programvaran. Azure ACS kan federera med en identitetsprovider som utbyter autentiseringsdata med hjälp av Security Assertion Markup Language (SAML), till exempel Active Directory Domain Services, Ping identitet, OneLogin eller SiteMinder. Detta hjälper GEP implementera enkel inloggning (SSO) för kunder utan att bekymra dig om att lagra autentiseringsuppgifter och underhålla lösenordsprinciper för kunden.

Kunder har åtkomst till rätt business-resurser i SMARTKORT av GEP när du är inloggad. GEP använder Azure Traffic Manager dirigera och belastningsutjämning begäranden som kommer från kundernas mobila enheter och webbläsarsessioner.

## <a name="other-azure-services"></a>Andra Azure-tjänster
GEP använder ett antal andra Azure-tjänster så att SMART av GEP kan svara på kundens behov. GEP använder Azure-molntjänster (webb- och arbetsroller roller) värden program-presentation och säkra affärslogik tjänsterna. Molntjänster gör det möjligt för utvecklare att hantera infrastrukturen som kod (IAC) och distribuera nya smarta GEP program på en bråkdel av tiden som krävs för med lokala datacenter, utan någon inblandning från IT. GEP utvecklare kan använda molntjänsterna mellanlagring miljö för att testa nya versioner utan att påverka den aktuella produktionsdistributionen. När testas, använder GEP VIP-växlingen funktioner i Azure-molntjänster flytta fristående koden till produktionsplatsen inom en minut, vilket minskar distribution driftstopp.

För att minska svarstiden för programmet, använder GEP i Azure innehåll innehållsleveransnätverk (CDN) för att placera statiskt innehåll som lagras i Azure Blob storage (till exempel CSS- och JavaScript-filer) på edge servrar nära användare. GEP använder Azure Service Bus för att stödja programarkitektur mönster från publicera och prenumerera på delvis kommandot frågan Responsiv ansvarsfördelning (CQRS) och lager arkitektur med lösa kopplingar och asynkron kommunikation. GEP använder Azure Media Services för att förbättra sin kundsupport tjänst. GEP hitta den kunde enkelt skicka videor stöd för användare på Azure Media Services. Dessa videor besvara frågor till användaren, som hjälper dig att förbättra SMART genom GEP användartillfredsställelse när några av belastningen stöd från GEPS kundsupport personal.

Om du vill skicka tusentals transaktionella e-postmeddelanden som genereras dagligen av SMARTKORT av GEP använder företagets SendGrid .NET API för att integrera med Azure. För GEP utvecklare är enkelt göra – SendGrid-tillägg för Azure är tillgängliga direkt i Azure Marketplace. GEP utvecklare kan konfigurera SMARTKORT av GEP med hjälp av rättigheten SendGrid NuGet-paketet i Microsoft Visual Studio; GEP IT kan övervaka programvarans SendGrid e-trafik direkt från Azure.

Slutligen SMART av GEP använder Azure virtuella datorer – Azure IaaS-tjänst – till värd-program och tjänster som inte särskilt meningsfullt, vid tidpunkten för teknik, om du vill ersätta med programvara som en-tjänst (SaaS) eller PaaS-lösningar. Till exempel värd GEP API integreringstjänsterna på virtuella datorer för business-to-business (B2B) integration med kunders lokalt enterprise-resursplanering system som SAP, Oracle, PeopleSoft, JD Edwards, Microsoft Dynamics GP och Lawson och kunden SaaS-lösningar för att effektivt utbyta inköp dokument, till exempel fakturor.

> ”SMART skapande av GEP i Microsoft Azure-molnet har fullständigt bort behovet av lokala IT, inte bara för GEP utan även för våra kunder inköp operations”. 
> 
> – Dhananjay Nagalkar, VD för tekniska lösningar
> 
> 

## <a name="expand-customer-satisfaction-without-expanding-it"></a>Expandera nöjda utan att expandera IT
Sedan migrera från lokala Datacenter till Azure och skapa SMART av GEP från grunden i Azure-plattformen, ökat GEP skalbarhet och flexibilitet utan att behöva expandera sin infrastruktur eller IT-personal. Företaget har inte i själva verket lägga till IT-resurser i mer än fyra år. Lämplig PaaS-modell för Azure har aktiverat GEP att minska dess utgifter på leverantör support och åtgärder. Därför har GEP resurser kan fokusera på programutveckling; och utveckla i molnet gör att GEP utvecklare att snabbt testa nya idéer utan att behöva ägna tid koordinera med IT eller bekymra lokalt licenskraven. Azure SQL Database hjälper GEP bättre säkerställa att deras kunder alltid har särskilda service och prestanda.

## <a name="more-information"></a>Mer information
* Startsidan för GEP: [GEP](http://www.gep.com)
* Smart av GEP: [Smart av GEP](http://www.smartbygep.com)

## <a name="gep-contributors"></a>GEP deltagare
* Huzaifa Matawala associera Director – systemarkitekt, GEP
* Sathyan Narasingh, ingenjörer Manager GEP
* Deepa Velukutty, databasen systemarkitekt GEP

