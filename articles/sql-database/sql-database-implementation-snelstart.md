---
title: Azure SQL Database Azure Fallstudie - Snelstart | Microsoft Docs
description: Lär dig mer om hur SnelStart använder SQL-databas till expanderade dess företagstjänster enligt en taxa på 1 000 nya Azure SQL-databaser per månad
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: c919a3cb47017d2f65b141e358ab318f4b764627
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713722"
---
# <a name="with-azure-snelstart-has-rapidly-expanded-its-business-services-at-a-rate-of-1000-new-azure-sql-databases-per-month"></a>Med Azure kan har SnelStart expanderade dess företagstjänster enligt en taxa på 1 000 nya Azure SQL-databaser per månad
![SnelStartLogo](./media/sql-database-implementation-snelstart/snelstartlogo.png)

SnelStart gör populära finansiella - och business-hanteringsprogramvara för små och medelstora företag i Nederländerna. Kunderna 55,000 hanteras av en avdelning av 110 anställda, inklusive en IT-personal vid 35. Genom att flytta från programvara för fjärrskrivbord till ett software-as-a-service (SaaS)-erbjudande som bygger på Azure, SnelStart gjort det mesta av inbyggda tjänster automatisera hantering med välbekanta miljön i C# och optimera prestanda och skalbarhet genom att varken över - eller under etablering företag med elastiska pooler. Azure ger SnelStart flytbarhet glidande kunder mellan molnet och lokalt.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-SnelStart/player]
> 
> 

## <a name="why-snelstart-extended-services-from-the-desktop-to-the-cloud"></a>Varför SnelStart utökade tjänster från skrivbordet till molnet
> ”Arbeta med Azure innebär att vi kan leverera programvara snabbare, snabbt reagera på kundkrav och skala lösningar när efterfrågan ökar”.
> 
> – Henrik tagits Software Architect
> 
> 

SnelStart körde en lyckad programvaruföretag i år, en traditionella utvecklings-modell: koda, paketera, leverera och upprepa. Framöver kommer takten av ändring växte snabbare och snabbare. Bestämmelser ändras ofta och enklare sätt att bearbeta finansiella poster och samarbeta med deras revisorer och myndigheter för att hålla jämna steg med sådana ändringar krävs för kunder.

”Skicka program till kunder har kostsamma och begränsande”, enligt Henrik tagits software architect. ”Produktion, paketering och leveranskostnader begränsade hur ofta kunde vi lansera programvara. Var vi tvungna att paketuppdateringar för regelbundna leveranser, men det gjorde det svårt att uppfylla våra kunders behov. Vi kan aldrig vara säker på att våra kunder uppgraderas till den senaste versionen av produkten. Därför var vi tvungna att stöd för flera versioner gör det mycket svårt att brunnen support-jobbet ”.

Har tilläggs-”, Vi ville hitta ett sätt att program- och jobbpubliceringsaktiviteter uppdateringar i en snabbare takt, så att vi kan utveckla snabbare och skapa nya tjänster för våra kunder. Vi ville också ett sätt att automatisera fler processer för att underlätta våra kunders behov för företag-administration ”.

För SnelStart var lösningen att utöka sina tjänster genom att bli en molnbaserad SaaS-provider. Azure SQL Database-plattformen hjälpte SnelStart dit utan att det medför större IT-kostnaderna som skulle ha krävt en lösning för infrastruktur-as-a-service (IaaS).

En molnmodell kan också SnelStart att korrigera buggar och tillhandahålla nya funktioner snabbt, utan att kunder inte längre behöver ladda ned och uppgradera programvara. Enligt har ”med hjälp av Azure cloud services gör att vi kan snabbt vidta åtgärder behoven från tredje part. I stället att skicka en ny version till tusentals kunder, vi kan anpassa efter informationen som skickas från våra skrivbordsprogram i nya format som krävs av tredje part ”.

## <a name="a-modern-company-with-traditional-roots"></a>Ett modernt företag med traditionella rötter
SnelStart är en modern, snabb, avancerad affärer med humble rötter långt tillbaka som 1964, när grundarna startades företaget som tillverkare av musik betalningsinstrument delar. Hjärtat i programvaruföretag SnelStart igång verkligen beating i 1980 talet, under ökningen av datorn. Företaget behövs ett bättre alternativ för redovisning programvaran som är tillgänglig vid tidpunkten, så det tog saken i sin egen händer. I 1982 skapat grunden för vad blir så småningom SnelStart redovisningsprogram i företaget. Från start-och programvaran har admired för dess enkelhet och hastighet – så mycket så att företaget så småningom ändras fokus och pånyttfötts i ett programvaruföretag.

1995 släppte SnelStart sin första bokföring-program för Windows. De program som bygger på Microsoft Visual Basic 1.0 och Microsoft Access-databaser, hjälpte växa kunden grundläggande till mer än 5 000 användare.

Idag är SnelStart större leverantör av en line-of-business (LOB) och business-administration program som riktas mot nederländska små och medelstora företag och enskilda entreprenörer. Eftersom Carlo Kuip, IT architect, säger är ”vårt mål att tillhandahålla 100 procent automation för företag-administrativa tjänster för våra kunder”.

## <a name="optimizing-performance-and-cost-with-elastic-pools"></a>Optimera prestanda och kostnader med elastiska pooler
SnelStart var en tidig användare av storskaliga för elastiska pooler. Elastiska pooler hjälpa företaget att begränsa kostnaderna och mer effektivt hantera prestandakrav. Enligt har ”med elastiska pooler kan vi kan optimera prestanda baserat på dina behov för våra kunder, utan överetablering. Om vi hade haft att etablera baserat på hög belastning, skulle det vara kostsamt. I stället alternativet för att dela resurser mellan flera databaser med låg belastning kan vi skapa en lösning som utför även och kostnadseffektivt alternativ ”.

## <a name="azure-sql-databases-help-containerize-data-for-isolation-and-security"></a>Azure SQL-databaser att behållaranpassa data för isolering och säkerhet
Azure SQL Database gör det möjligt för SnelStart att enkelt och transparent flytta kundernas lokala företag administration data till Azure. Azure SQL Database är en praktisk behållare som tillhandahåller isolering, en gräns för autentisering, auktorisering och enkelt säkerhetskopierings- och återställningsfunktioner. Databaserna ger en konceptuell modell som passar bra för företag-administration. Enligt Carlo Kuip, IT architect, ”objekt i behållaren gränsen innehåller känsliga data som är avgörande för ett företag och lagra dessa objekt i en isolerad databas fortfarande skyddas. Vi kan hantera auktorisering på databasnivå och även automatisera hantering och skalbarhet för dessa databaser utan databasadministratörer (databasadministratörer) på personal ”.

Azure SQL Data Warehouse spelar också en roll i SnelStart säkerhet och hantering av artikeln genom att hjälpa företaget samla in telemetridata som intrångsidentifiering, användarinloggning för aktiviteten och anslutning.

## <a name="azure-removes-overhead-so-that-developers-can-spend-more-time-delivering-value"></a>Azure tar bort omkostnader så att utvecklare kan lägga mer tid som får värdet
Azure-plattformen modellen bort omkostnader för infrastruktur och aktiverat SnelStart att automatisera distributioner med C#-hanteringsbibliotek. Eftersom Kuip säger kunde ”vi utöka våra aktuella åtgärder med en mycket liten avdelning samtidigt som de samtidigt ökar skalbarhet, hastighet och disaster recovery alternativ för våra kunder. Övergången till frigjort resurser för att fokusera på nya tjänster och funktioner i stället för bara uppdatera befintlig kod att hålla jämna steg med nya föreskrifter eller skattekoder services-utveckling ”. Han lägger till ”genom att automatisera hantering och med hjälp av SaaS-erbjudande, det går att leverera bättre värde till våra kunder utan att behöva göra stora investeringar i driftpersonal”. Till exempel med hjälp av Azure och elastiska pooler kunde SnelStart Lägg till en mängd nya funktioner, inklusive den mer robust kunddata integrering med banker, nya fakturering tjänster, småföretag bakgrundskontroller och e-posttjänster.

> ”Vi har utökat vår Azure SQL Database-distributioner från cirka 5,500 till mer än 12 000 bara de första månaderna av 2016, och vi för närvarande lägger till ca 1 000 databaser per månad”.
> 
> – Henrik tagits Software Architect
> 
> 

Databashantering ytterligare automatiskt med hjälp av funktionen elastiska jobb. Eftersom Kuip säger tack ”mycket för automatisk identifiering av databaser på en [server]-instans av SQL-databas”. På så sätt kan SnelStart att utföra hanteringsåtgärder på deras dynamiskt växande kundbas utan ytterligare kostnader.

SnelStart också utvecklar en API som fungerar som koordinator mellan kundernas data och appar som skapats av partner för programvara från tredje part. Som Kuip står kan ”detta API andra leverantörer att lägga till funktioner i vår programvara, till exempel att dataposten för fakturor och andra dokument”. Kunder har inte längre skriva manuellt fakturor i sina småföretag redovisningsprogram; SnelStart programvaran kommer exchange nödvändig information direkt. På så sätt kan kunderna ansluta sina företag administrationsuppgifter med ekosystem med information som nya från digital omvandling i branschen.  

## <a name="how-azure-services-enable-saas-for-snelstart"></a>Hur Azure-tjänster aktivera SaaS för SnelStart
Genom att använda Azure betjäna SnelStart sina kunder och deras revisorer mer sömlöst i molnet. Till exempel både kunder och revisorer kan dela information genom att gå direkt till Snelstarts klient-API som finns på Azure. Kuip säger ”dessa återanvändbara tjänster är tillgängliga för våra kundinriktade web apps och ger gemensam infrastruktur och funktioner för att tillåta åtkomst till företag administration för kunder och tredje parts programvara som används av våra kunder. Exempel är att tillhandahålla produktkonfigurationen funktioner, hanteringen av brandväggsregler och hantera tidskrävande processer som säkerhetskopior ”.

> Vårt mål är att tillhandahålla 100 procent automation för företag-administrativa tjänster för våra kunder ”. 
> 
> – Carlo Kuip, IT Architect
> 
> 

Dessutom kan SnelStart webbtjänster både kunder och revisorer för enkel åtkomst till data i Azure SQL Database elastiska pooler. Den här SaaS-modellen, tillsammans med databasen elasticitet och Azure Resource Manager tillhandahåller SnelStart skalbarhet funktioner som passar varje Azure-distribution. Implementeringen automatiserad helt med hjälp av C#-hanteringsbibliotek.

![SnelStart arkitektur](./media/sql-database-implementation-snelstart/figure1.png)

Bild 1. Från och med juni 2016 har SnelStart mer än 11 000 databaser och mer än 50 elastiska pooler

## <a name="simplicity-from-the-cloud"></a>Enkelhet från molnet
Sedan flyttar till en Azure molnbaserad lösning, kunnat SnelStart för kunden snabb tillväxt och erbjuder innovativa funktioner och tjänster. Enligt har ”med Azure kan vi kan leverera nära kontinuerliga uppdateringar för våra kunder, utan att expandera vår personalstyrka. Och vi får alla andra Azure funktioner – som skalbarhet och katastrofåterställning – paketeras i ”.

> ”När vi har faktiskt över i Redmond... Jag har fått ett anrop från en utvecklare i Nederländerna, ringa mig om ett specifikt problem. Det gick att hämta Microsoft för att leverera en ändring i sin produktionsmiljö inom 48 timmar att lösa våra problemet ”.
> 
> – Henrik tagits Software Architect
> 
> 

SnelStart appreciates också starkt partnerskap som de har utvecklat med Microsoft Azure SQL DB-teamet. Som Kuip tillstånd, ”vi har diskussioner om funktioner och hur du använder teknik, något som uppskattas på båda sidorna”.
Omedelbar målet för SnelStart är att hålla växande dess nöjd kunden grundläggande. Varit säger ”utan teknisk hjälp och resurs begränsningarna som vi hade som en oberoende Programvaruleverantör, det finns ingen gräns för hur långt vi kan växa”.

## <a name="more-information"></a>Mer information
* Läs mer om Azure elastiska pooler i [elastiska pooler](sql-database-elastic-pool.md).
* Läs mer om Azure SQL Data Warehouse i [SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)
* Läs mer om SnelStart i [SnelStart](http://www.snelstart.nl).

