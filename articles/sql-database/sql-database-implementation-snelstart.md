---
title: Azure SQL-databas Azure Fallstudie - Snelstart | Microsoft Docs
description: "Lär dig mer om hur SnelStart använder SQL-databas för att snabbt expanderat dess företagstjänster med 1 000 nya Azure SQL-databaser per månad"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: fab506b2-439d-4f1a-bdc5-d1d25c80d267
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: 4fa21cf9cbd1680ddd855189f50af50e1068ccd5
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="with-azure-snelstart-has-rapidly-expanded-its-business-services-at-a-rate-of-1000-new-azure-sql-databases-per-month"></a>Med Azure, har SnelStart snabbt utökats dess företagstjänster med 1 000 nya Azure SQL-databaser per månad
![SnelStartLogo](./media/sql-database-implementation-snelstart/snelstartlogo.png)

SnelStart gör populära finansiella - och business-hanteringsprogramvara för små och medelstora företag (SMB) i Nederländerna. Kunderna 55,000 underhålls av en avdelning för 110 anställda, inklusive en IT-personal 35. Genom att flytta från programvara för skrivbordet till en programvara som en-tjänst (SaaS) erbjuder bygger på Azure, skedde SnelStart merparten av inbyggda tjänster automatisera hanteringen med välbekant miljö i C# och optimera prestanda och skalbarhet genom att varken över - eller under-etablering företag med elastiska pooler. Med Azure ger SnelStart flytbarhet glidande kunder mellan lokalt och i molnet.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-SnelStart/player]
> 
> 

## <a name="why-snelstart-extended-services-from-the-desktop-to-the-cloud"></a>Varför SnelStart utökade tjänster från skrivbordet till molnet
> ”Arbeta med Azure innebär att vi kan leverera programvara snabbare, snabbt reagera på kundens behov och skala lösningar när kraven ökar”.
> 
> – Henrik har programvara systemarkitekt
> 
> 

SnelStart körde en lyckad programvara företag i år, med hjälp av en utvecklingsmodell för traditionella: code, paket, levereras och upprepa. Över tiden, takt ändring vuxen snabbare och snabbare. Förordningar som ändras ofta och kunder enklare sätt att bearbeta ekonomiska poster och samarbeta med deras revisorer och myndigheter Håll dig uppdaterad med de ändringarna som krävs.

”Skicka program till kunder har dyrt och begränsande”, enligt Henrik har programvara systemarkitekter. ”Produktion, förpackning och leveranskostnader begränsade hur ofta vi gick ut programvara. Vi var tvungna att paketet uppdateringar för periodiska leveranser, men som gjorde det svårt att uppfylla våra kunders föränderliga behov. Vi kan aldrig vara säker på att våra kunder uppgraderas till den senaste versionen av produkten. Därför kan vi var tvungna att stöd för flera versioner gör det väldigt svår att och stöd för jobbet ”.

Har läggs till ”, vill vi ett sätt att programmet och version uppdateringar i en snabbare pace, så att vi kan förnya snabbare och skapa nya tjänster för våra kunder. Vi ville också ett sätt att automatisera flera processer för att förenkla våra kunder business-administrationsbehov ”.

För SnelStart var lösningen att utöka dess tjänster genom att bli en molnbaserad SaaS-provider. Azure SQL Database-plattformen hjälpt SnelStart dit utan att det medför stora IT-omkostnader som skulle ha krävt en lösning för infrastruktur-som-en-tjänst (IaaS).

Molnmodellen kan också SnelStart att åtgärda fel och innehåller nya funktioner för snabbt, utan att behöva hämta och uppgradera programvaran kunder. Enligt har ”Using Azure cloud services ger oss möjlighet att agera snabbt på behoven från tredje part. I stället att leverera en ny version till tusentals kunder kan anpassa vi informationen som skickas från våra program till nya format som krävs av tredje part ”.

## <a name="a-modern-company-with-traditional-roots"></a>En modern företag med traditionella rötter
SnelStart är en modern, flexibel, högteknologiska företag med blygsam rötter långt tillbaka som 1964, när grundare igång företaget som tillverkar musik betalningsinstrument delar. Hjärtat i SnelStart programvara verksamheten igång verkligen beating i 1980 talet, under den ökande mängden av datorn. Företaget behövs ett bättre alternativ till redovisningsprogram som är tillgängliga på gång, så det tog frågor i sin egen händer. I 1982 skapat grunden för vad slutligen skulle bli SnelStart redovisningsprogram i företaget. Från start-och programvaran har admired för enkelhetens skull och hastighet, så mycket så att företaget slutligen ändras fokus och reinvented sig i ett programvaruföretag.

1995 släppt SnelStart dess första bokföring program för Windows. Det program som bygger på Microsoft Visual Basic 1.0 och Microsoft Access-databaser, hjälpt växa kunden grundläggande till mer än 5 000 användare.

Idag är SnelStart större tillhandahåller en branschspecifika (LOB) och företag administration programmet riktad mot nederländska små och medelstora företag och enskilda företagare. Eftersom Carlo Kuip, IT systemarkitekt säger är ”vårt mål att tillhandahålla 100 procent automation för företag-administrativa tjänster för våra kunder”.

## <a name="optimizing-performance-and-cost-with-elastic-pools"></a>Optimera prestanda och kostnad med elastiska pooler
SnelStart var en storskalig tidig stöd för elastiska pooler. Elastiska pooler hjälpa företaget begränsa kostnader och hantera prestandakrav mer effektivt. Enligt har ”med elastiska pooler kan vi kan optimera prestanda baserat på dina behov för våra kunder, utan överbelasta. Om vi var tvungna att etablera utifrån belastning, skulle det vara kostsamt. I stället alternativet för att dela resurser mellan flera låg belastning databaser kan vi skapa en lösning som utför väl och kostnadseffektivt ”.

## <a name="azure-sql-databases-help-containerize-data-for-isolation-and-security"></a>Azure SQL-databaser att containerize data för isolering och säkerhet
Azure SQL Database kan SnelStart enkelt och transparent flytta kunders lokala företag administration data till Azure. Azure SQL Database är en lämplig behållare som ger isolering, en gräns för autentisering, auktorisering och enkelt funktioner för säkerhetskopiering och återställning. Databaserna utgör en väl lämpade konceptuell modell för företag-administration. Enligt Carlo Kuip, IT systemarkitekt ”objekt inom den här behållaren innehåller känsliga data som är avgörande för ett företag och lagra objekten i en isolerad databas för att hålla dem väl skyddad. Vi kan hantera auktorisering på databasnivå och även automatisera hantering och skalbar för dessa databaser utan databasadministratörer (DBAs) på personal ”.

Azure SQL Data Warehouse spelar också en roll i artikeln SnelStart säkerhet och hantering genom att hjälpa företaget samla in telemetridata som intrångsidentifiering, användaren aktivitetsloggning och anslutningen.

## <a name="azure-removes-overhead-so-that-developers-can-spend-more-time-delivering-value"></a>Azure tar bort omkostnader så att utvecklare kan flera ägna tid åt att leverera värde
Azure-plattformen modellen bort infrastruktur kostnader och aktiverat SnelStart att automatisera distribution med hjälp av C# hantering av bibliotek. Som Kuip tillstånd gick ”det att växa våra aktuella åtgärder med en mycket liten avdelning när samtidigt ökad skalbarhet, hastighet och disaster recovery-alternativ för våra kunder. Övergången till frigjort resurser för att fokusera på nya tjänster och funktioner, i stället för bara uppdatera befintliga koden kan hålla jämna steg med nya förordningar eller skattekoder services-utveckling ”. Han lägger till ”genom att automatisera hanteringen av och använda SaaS erbjudande, för att ge mer värde för våra kunder utan att behöva göra stora investeringar i operativa personal”. Till exempel med hjälp av Azure och elastiska pooler kunde SnelStart lägga till en mängd nya funktioner, inklusive stabilare kundinformation integrering med banker, fakturering nya tjänster, småföretag bakgrundskontroller och e-tjänster.

> ”Vi utökat vårt Azure SQL Database-distributioner från om 5,500 till mer än 12 000 bara de första månaderna 2016, och vi för närvarande lägger till ca 1 000 databaser per månad”.
> 
> – Henrik har programvara systemarkitekt
> 
> 

Databashantering ytterligare automatiskt med hjälp av funktionen elastiska jobb. Eftersom Kuip tillstånd tack ”hög för automatisk identifiering av databaser på en [server] instans av SQL-databas”. På så sätt kan SnelStart att utföra hanteringsåtgärder över sina dynamiskt växande kundbas utan ytterligare kostnader.

SnelStart också utvecklar en API som fungerar som en koordinator mellan kundens data och appar som skapats av programvara från tredje part partners. Som Kuip tillstånd kan ”detta API andra leverantörer att lägga till funktioner i vår programvara, till exempel att dataposten för fakturor och andra dokument”. Kunder behöver inte längre manuellt ange fakturor i sina småföretag redovisningsprogram; SnelStart programvaran kommer direkt utbyta information som behövs. Detta gör att kunder kan ansluta till sina företag administrationsuppgifter med ekosystem med information som härrör från digitala transformation i branschen.  

## <a name="how-azure-services-enable-saas-for-snelstart"></a>Hur Azure-tjänster aktivera SaaS för SnelStart
Med hjälp av Azure kan SnelStart hantera sina kunder och deras revisorer mer sömlöst i molnet. Till exempel dela kunder och revisorer information med direkt åtkomst till Snelstarts klient-API som finns på Azure. Kuip tillstånd ”tjänsterna återanvändbara är tillgängliga för våra kund-riktade webbprogram och ger gemensam infrastruktur och funktioner för att tillåta åtkomst till företag administration för kunder och tredjepartsprogram som används av våra kunder. Exempel: tillhandahåller produktkonfigurationen funktioner, hantera brandväggsregler och hantera tidskrävande processer som säkerhetskopieringar ”.

> Vårt mål är att tillhandahålla 100 procent automation för företag-administrativa tjänster för våra kunder ”. 
> 
> – Carlo Kuip, IT systemarkitekt
> 
> 

Dessutom kan SnelStart webbtjänster både kunder och revisorer för att enkelt komma åt data i Azure SQL Database elastiska pooler. Den här SaaS-modellen, tillsammans med databasen elasticitet och Azure Resource Manager tillhandahåller SnelStart skalbarhet funktioner som passar alla Azure-distribution. Implementeringen automatiserad helt hantering av bibliotek C#.

![SnelStart arkitektur](./media/sql-database-implementation-snelstart/figure1.png)

Bild 1. Från och med juni 2016 har SnelStart mer än 11 000 databaser och mer än 50 elastiska pooler

## <a name="simplicity-from-the-cloud"></a>Enkelhet från molnet
Sedan flyttar till en Azure molnbaserad lösning, har SnelStart möjlighet att stödja snabb kunden tillväxt samtidigt som den erbjuder innovativa funktioner och tjänster. Enligt har ”med Azure, vi kan leverera nästan kontinuerlig uppdateringar för våra kunder, utan att expandera våra driftspersonal. Och vi får alla andra bra Azure funktioner – som skalbarhet och katastrofåterställning – finns i ”.

> ”När vi har faktiskt över i Redmond... Jag har fått ett anrop från utvecklaren tillbaka i Nederländerna ringa mig om ett specifikt problem. Det gick att hämta Microsoft att leverera en ändring i sin produktionsmiljö inom 48 timmar att lösa våra problemet ”.
> 
> – Henrik har programvara systemarkitekt
> 
> 

SnelStart appreciates också starkt partnerskap som de har utvecklats med Microsoft Azure SQL DB-teamet. Som Kuip tillstånd, ”vi har diskussioner på funktioner och hur du använder teknik som är något uppskattar på båda sidor”.
Omedelbar målet för SnelStart är att behålla växande nöjd kunden bas. Varit tillstånd ”utan teknik- och begränsningar som vi hade som en ISV, det finns ingen gräns för hur långt vi kan växa”.

## <a name="more-information"></a>Mer information
* Läs mer om Azure elastiska pooler i [elastiska pooler](sql-database-elastic-pool.md).
* Läs mer om Web och arbetsroller i [arbetsroller](../fundamentals-introduction-to-azure.md#compute).    
* Läs mer om Azure SQL Data Warehouse i [SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)
* Läs mer om SnelStart i [SnelStart](http://www.snelstart.nl).

