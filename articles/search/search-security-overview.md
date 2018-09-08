---
title: Säkerhet och sekretess i Azure Search | Microsoft Docs
description: Azure Search är kompatibla med SOC 2, HIPAA och andra certifieringar. Anslutning och kryptering, autentisering och identitet åtkomst via användare och grupp säkerhets-ID i Azure Search filter.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: heidist
ms.openlocfilehash: 4b1307aa00fae26d7425c9a95ed673b11ba2e9b4
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092639"
---
# <a name="security-and-data-privacy-in-azure-search"></a>Säkerhet och sekretess i Azure Search

Omfattande funktioner för säkerhet och åtkomstkontroll är inbyggda i Azure Search för att se till att personligt innehåll fungerar på samma sätt. Den här artikeln räknar upp security funktioner och standarder för efterlevnad som är inbyggda i Azure Search.

Azure Search-säkerhetsarkitekturen omfattar fysisk säkerhet, krypterade överföringar, krypterad lagring och plattformen standarder efterlevnad. Azure Search accepterar funktionsmässigt endast autentiserade begäranden. Alternativt kan du lägga till kontroller för varje användare åtkomst för innehåll via säkerhetsfilter. Den här artikeln vidrör på säkerheten på varje nivå, men fokuserar främst på hur data och åtgärder skyddas i Azure Search.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Överensstämmelse med standarder: ISO 27001, SOC 2, HIPAA

Azure Search är certifierad för följande standarder som [tillkännagav i juni 2018](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/):

+ [ISO 27001: 2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [SOC 2 typ 2-efterlevnad](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) för den fullständiga rapporten, gå till [Azure- och Azure Government SOC 2 typ II rapporten](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Health Insurance Portability and Accountability Act (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR Part 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS nivå 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Australien IRAP oklassificerade DLM](https://asd.gov.au/infosec/irap/certified_clouds.htm)

Överensstämmelse med standarder gäller för allmänt tillgängliga funktioner. Förhandsversionsfunktioner är certifierade när de övergång till allmän tillgänglighet och får inte användas i lösningar med strikta standarder för krav. Efterlevnadscertifiering dokumenteras i [översikt över Microsoft Azure-efterlevnad](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) och [Säkerhetscenter](https://www.microsoft.com/en-us/trustcenter). 

## <a name="encrypted-transmission-and-storage"></a>Krypterad överföring och lagring

Kryptering utökar i hela för hela indexering av pipeline: från anslutningar, överföring och ned indexerade data som lagras i Azure Search.

| Säkerhetslager | Beskrivning |
|----------------|-------------|
| Kryptering under överföring <br>(HTTPS/SSL/TLS) | Azure Search lyssnar på port 443 för HTTPS. Anslutningar till Azure-tjänster krypteras på plattformen som helst. <br/><br/>Alla klient-till-tjänst Azure Search interaktioner är SSL/TLS 1.2-kompatibla.  Glöm inte att använda TLSv1.2 för SSL-anslutningar till din tjänst.|
| Vilande kryptering | Kryptering är helt internalized i indexeringsprocessen mätbara påverkan på indexering tid att slutföra eller Indexstorlek. Det sker automatiskt på alla indexering, inklusive på inkrementella uppdateringar till ett index som inte är helt krypterad (som skapats före januari 2018).<br><br>Internt, kryptering baseras på [Azure Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), med 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).|

Kryptering är interna för Azure Search med certifikat och krypteringsnycklar hanteras internt av Microsoft och tillämpas universellt. Du kan inte aktivera kryptering eller inaktivera, hantera eller ersätta dina egna nycklar eller visa krypteringsinställningar i portalen eller via programmering. 

Kryptering i vila lanserades i 24 januari 2018 och gäller för alla tjänstnivåer, inklusive delade (gratis) tjänster i alla regioner. Index som skapats före detta datum måste släppas och återskapas för att kryptering ska ske för fullständig kryptering. I annat fall krypteras endast nya data som lagts till efter januari 24.

## <a name="azure-wide-user-access-controls"></a>Azure hela åtkomstkontroller

Flera säkerhetsmekanismer finns tillgängliga på hela Azure och därmed automatiskt tillgängliga i Azure Search-resurser som du skapar.

+ [Lås i prenumerationen eller resursnivå om du vill förhindra att tas bort](../azure-resource-manager/resource-group-lock-resources.md)
+ [Rollbaserad åtkomstkontroll (RBAC) att styra åtkomsten till information och administrativa åtgärder](../role-based-access-control/overview.md)

Alla Azure-tjänster stöder rollbaserad åtkomstkontroll (RBAC) för att ställa in åtkomstnivåer konsekvent för alla tjänster. Till exempel är visa känsliga data, till exempel admin-nyckel begränsad till ägare och deltagare roller, visa Tjänststatus för är tillgänglig för medlemmar i en roll. RBAC ger ägare, deltagare och läsare. Som standard är alla administratörer medlemmar i rollen ägare.

## <a name="service-access-and-authentication"></a>Tjänståtkomst och autentisering

Medan Azure Search ärver säkerhetsåtgärder i Azure-plattformen, ger också en egen nyckel för autentisering. En api-nyckel är en sträng som består av slumpmässigt genererat siffror och bokstäver. Typen av nyckel (admin eller fråga) avgör vilken åtkomstnivå. Överföring av en giltig nyckel anses vara bevis begäran kommer från en betrodd enhet. Två typer av nycklar används för att få åtkomst till din söktjänst:

* Admin (gäller för alla skrivskyddade åtgärder mot tjänsten)
* Fråga (gäller för skrivskyddade åtgärder, till exempel frågor mot ett index)

Admin-nycklar skapas när tjänsten har etablerats. Det finns två administratörsnycklar utsetts *primära* och *sekundära* för att hålla dem direkt, men i själva verket de är utbytbara. Varje tjänst har två administratörsnycklar så att du kan växla en utan att förlora åtkomst till din tjänst. Du kan återskapa antingen admin-nyckel, men du kan inte lägga till antalet totala administratör. Det finns två administratörsnycklar per söktjänst maximalt.

Frågenycklar skapas när det behövs och är utformade för klientprogram som anropar Search direkt. Du kan skapa upp till 50 frågenycklar. I programkoden anger du URL: en för sökning och en api-Frågenyckeln för att tillåta skrivskyddad åtkomst till tjänsten. Programkoden anger också det index som används av ditt program. Tillsammans definiera slutpunkten, en api-nyckel för skrivskyddad åtkomst och ett målindex omfång och åtkomst anslutningen från klientprogrammet.

Autentisering krävs för varje begäran, där varje begäran består av en obligatorisk nyckel och en åtgärd som ett objekt. De två behörighetsnivåerna (fullständig eller skrivskyddad) plus kontext (till exempel en frågeåtgärden för ett index) är tillräckliga för att tillhandahålla fullständigt spektrum säkerhet på tjänståtgärder när kopplats samman. Mer information om nycklar finns i [skapa och hantera api-nycklar](search-security-api-keys.md).

## <a name="index-access"></a>Index åtkomst

Ett enskilt index är inte ett skyddsbara objekt i Azure Search. I stället bestäms åtkomst till ett index på tjänstnivå (läs- eller skrivbehörighet), tillsammans med kontexten för en åtgärd.

Du kan strukturera frågebegäranden att ansluta med en frågenyckel, vilket gör eventuella begäran skrivskyddad och är specifikt index som används av din app för slutanvändarnas åtkomst. I en fråga finns det ingen anslutning till index eller åt flera index samtidigt så att alla förfrågningar rikta en enda index per definition. Därför definierar konstruktion av frågebegäran själva (en nyckel plus ett enda mål-index) säkerhetsgränsen.

Administratörers och utvecklares åtkomst till index är odifferentierade: båda behöver skrivbehörighet för att skapa, ta bort och uppdatera objekt som hanteras av tjänsten. Vem som helst med en administratörsnyckel till din tjänst kan läsa, ändra eller ta bort ett index i samma tjänst. För skydd mot oavsiktlig eller skadlig borttagning av index är din interna källkontroll för kod tillgångar remedy för återföring en oönskad index borttagning eller ändring av. Azure Search har redundans i klustret för att säkerställa tillgänglighet, men det lagrar eller köra egna kod används för att skapa eller läsa in index.

För multitenancy-lösningar kräver säkerhetsgränser på nivån indexet innehåller sådana lösningar vanligtvis en mellannivå vilka Använd för att hantera index isolering. Läs mer om multitenant användningsfallet [designmönster för SaaS-program för flera innehavare och Azure Search](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access-from-client-apps"></a>Administratörsåtkomst från klientappar

Azure Search Management REST API är ett tillägg för Azure Resource Manager och delar dess beroenden. Därför måste krävs Active Directory för att tjänstadministration av Azure Search. Alla administrativa förfrågningar från klientkod måste autentiseras med Azure Active Directory innan begäran når Resource Manager.

Begäranden mot Azure Search-tjänstens slutpunkt, till exempel skapa Index (Azure Search Service REST API) eller söka efter dokument (Azure Search Service REST API), Använd en api-nyckeln i rubriken.

Om din programkod hanterar tjänsten administrationsåtgärder, samt åtgärder på search-index eller dokument, implementera två autentiseringsmetoder i din kod: åtkomstnyckel som är inbyggt i Azure Search och Active Directory-autentisering metoder som krävs av Resource Manager. 

Information om att strukturera en begäran i Azure Search finns i [Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice/). Mer information om autentiseringskrav för Resource Manager finns i [autentisering använda Resource Manager API att få åtkomst till prenumerationer](../azure-resource-manager/resource-manager-api-authentication.md).

## <a name="user-access-to-index-content"></a>Användaråtkomst till indexera innehåll

Per användare åtkomst till innehållet i ett index implementeras via säkerhetsfilter i dina frågor som returnerar ett dokument som är associerade med en viss säkerhetsidentitet. I stället för fördefinierade roller och rolltilldelningar implementeras identitetsbaserade åtkomstkontroll som ett filter som tar bort sökresultat för dokument och innehåll utifrån identiteter. I följande tabell beskrivs två metoder för att ta bort sökresultaten av obehörig innehåll.

| Metoden | Beskrivning |
|----------|-------------|
|[Säkerhetsoptimering baserat på identitetsfilter](search-security-trimming-for-azure-search.md)  | Det grundläggande arbetsflödet för att implementera Användaråtkomstkontroll för identity-dokument. Det omfattar att lägga till säkerhets-ID till ett index och sedan förklarar filtrering mot fältet att trimma resultatet av otillåten innehåll. |
|[Säkerhetsoptimering baserat på Azure Active Directory-identiteter](search-security-trimming-for-azure-search-with-aad.md)  | Den här artikeln kan utökas med föregående artikeln för att tillhandahålla steg för att hämta identiteter från Azure Active Directory (AAD), en av de [kostnadsfria tjänster](https://azure.microsoft.com/free/) i Azure-molnplattformen. |

## <a name="table-permissioned-operations"></a>Tabeller: Permissioned åtgärder för

I följande tabell sammanfattas de åtgärder som tillåts i Azure Search och vilken nyckel ger åtkomst till en viss åtgärd.

| Åtgärd | Behörigheter |
|-----------|-------------------------|
| Skapa en tjänst | Azure prenumeranten|
| Skala en tjänst | Admin-nyckel, RBAC ägare eller deltagare för resursen  |
| Ta bort en tjänst | Admin-nyckel, RBAC ägare eller deltagare för resursen |
| Skapa, ändra och ta bort objekt i tjänsten: <br>Index och komponenter (inklusive analyzer definitioner, bedömningsprofiler, CORS-alternativ), indexerare, datakällor, synonymer, förslagsställare. | Admin-nyckel, RBAC ägare eller deltagare för resursen  |
| Fråga ett index | Administratören eller fråga nyckeln (RBAC ej tillämpligt) |
| Fråga Systeminformation, till exempel returnerar statistik, antal och en lista över objekt. | Admin-nyckel, RBAC på resursen (ägare, deltagare, läsare) |
| Hantera administratörsnycklar | Administratörsnyckel RBAC ägare eller deltagare för resursen. |
| Hantera frågenycklar |  Administratörsnyckel RBAC ägare eller deltagare för resursen.  |

## <a name="physical-security"></a>Fysisk säkerhet

Microsoft-datacenter ger branschledande fysisk säkerhet och är kompatibla med en omfattande portfölj med standarder och föreskrifter. Om du vill veta mer kan du gå till den [globala Datacenter](https://www.microsoft.com/cloud-platform/global-datacenters) sidan eller titta på en kort video om data center säkerhet.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Se också

+ [Kom igång .NET (visar hur du använder en administratörsnyckel för att skapa ett index)](search-create-index-dotnet.md)
+ [Kom igång REST (visar hur du använder en administratörsnyckel för att skapa ett index)](search-create-index-rest-api.md)
+ [Identitetsbaserad åtkomstkontroll med hjälp av Azure Search filter](search-security-trimming-for-azure-search.md)
+ [Active Directory identity-baserad åtkomstkontroll med hjälp av Azure Search filter](search-security-trimming-for-azure-search-with-aad.md)
+ [Filter i Azure Search](search-filters.md)