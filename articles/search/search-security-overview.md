---
title: Säkerhets- och sekretessnivåer i Azure Search | Microsoft Docs
description: Azure Search är kompatibel med SOC 2, HIPAA och andra certifikat. Anslutning och data kryptering, autentisering och identitet åtkomst via användare och grupp säkerhets-ID i Azure Search filter.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: heidist
ms.openlocfilehash: 888f7c3ced0ef48cff222bffdbf0f278fa5f42b3
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285737"
---
# <a name="security-and-data-privacy-in-azure-search"></a>Säkerhets- och sekretessnivåer i Azure Search

Omfattande säkerhetsfunktioner och åtkomstkontroll är inbyggda i Azure Search så personligt innehåll fungerar på samma sätt. Den här artikeln räknar säkerhet-funktioner och standarder regelefterlevnad inbyggda i Azure Search.

Azure Search-säkerhetsarkitekturen omfattar fysisk säkerhet, krypterade överföringar, krypterade lagring och standarder för plattform hela kompatibilitet. Azure Search funktionsmässigt krävs accepterar bara autentiserade begäranden. Du kan också kan du lägga till per användare åtkomstkontroller på innehåll via säkerhetsfilter. Den här artikeln vidrör på säkerheten på varje nivå, men är i första hand på hur data och åtgärder skyddas i Azure Search.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Överensstämmelse med standarder: ISO 27001 SOC 2 HIPAA

En ofullständig lista över överensstämmelse med standarder innehåller SOC 2 typ 2 och HIPAA för allmänt tillgängliga funktioner. Förhandsgranskningsfunktioner är certifierade som en del av allmän tillgänglighet och får inte användas i lösningar med krav på specifika krav. Kompatibilitet certifikatutfärdare dokumenteras i [översikt över Microsoft Azure-kompatibilitet](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) och [Säkerhetscenter](https://www.microsoft.com/en-us/trustcenter). 

Certifiering för följande standarder var [tillkännages i juni 2018](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/).

+ [ISO 27001: 2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [Kompatibilitet för SOC 2 typ 2](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) för hela rapporten går du till [Azure- och Azure Government SOC 2 II rapport](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Health Insurance Portability and Accountability Act (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR del 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS nivå 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Australien IRAP oklassificerade DLM](https://asd.gov.au/infosec/irap/certified_clouds.htm)

## <a name="encrypted-transmission-and-storage"></a>Krypterad överföring och lagring

Kryptering utökar under hela den hela pipelinen för fulltextindexering: från anslutningar via överföring och nedåt indexerade data som lagras i Azure Search.

| Säkerhetsskiktet | Beskrivning |
|----------------|-------------|
| Kryptering under överföring | Azure Search lyssnar på HTTPS-port 443. Anslutningar till Azure-tjänster krypteras över hela plattformen. |
| Vilande kryptering | Kryptering är fullständigt internalized i indexering processen utan mätbara inverkan på indexering tid för slutförande eller Indexstorlek. Det sker automatiskt på alla indexering, inklusive på inkrementella uppdateringar till ett index som inte är fullständigt krypterade (som skapats före januari 2018).<br><br>Internt, kryptering baseras på [Azure Storage Service-kryptering](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), med hjälp av 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).|

Kryptering är interna för Azure Search med certifikat och krypteringsnycklar hanteras internt av Microsoft och tillämpas universellt. Du kan inte aktivera kryptering eller inaktivera, hantera eller ersätta egna nycklar eller visa krypteringsinställningar i portalen eller programmässigt. 

Kryptering i vila har angivits i 24 januari 2018 och gäller för alla servicenivåer, inklusive delade (kostnadsfritt), i alla regioner. För fullständig kryptering index som skapades före denna tidpunkt släppas och återskapas för att kryptering ska ske. Annars krypteras endast nya data som lagts till efter 24 januari.

## <a name="azure-wide-user-access-controls"></a>Azure hela åtkomstkontroller

Flera säkerhetsmekanismer finns tillgängliga Azure hela och därmed automatiskt tillgängliga för Azure Search-resurser som du skapar.

+ [Lås i prenumerationen eller resursen nivå för att förhindra att tas bort](../azure-resource-manager/resource-group-lock-resources.md)
+ [Rollbaserad åtkomstkontroll (RBAC) att styra åtkomsten till information och administrativa åtgärder](../role-based-access-control/overview.md)

Alla Azure-tjänster stöder rollbaserad åtkomstkontroll (RBAC) för att ställa in åtkomstnivåer konsekvent över alla tjänster. Till exempel är visa känsliga data, till exempel admin-nyckel begränsad till ägare och deltagare roller, visa Tjänststatus för är tillgänglig för medlemmar i någon roll. RBAC ger ägare, deltagare och läsare. Som standard är alla administratörer medlemmar i rollen som ägare.

## <a name="service-access-and-authentication"></a>Åtkomst till tjänsten och autentisering

Medan Azure Search ärver säkerhetsåtgärder i Azure-plattformen, ger också en egen nyckel för autentisering. En api-nyckel är en sträng som består av slumpmässigt genererat siffror och bokstäver. Typ av nyckel (admin eller fråga) avgör vilken åtkomstnivå. Överföring av en giltig nyckel anses bevis begäran kommer från en betrodd enhet. Två typer av nycklar används för åtkomst till din söktjänst:

* Admin (gäller för alla skrivskyddad åtgärder mot tjänsten)
* Fråga (gäller för skrivskyddade åtgärder, till exempel frågor mot ett index)

Admin nycklar skapas när tjänsten har etablerats. Det finns två admin-nycklar, utses *primära* och *sekundära* för att hålla dem direkt, men i själva verket de är utbytbara. Varje tjänst har två admin-nycklar så att rulla en över utan att förlora åtkomsten till tjänsten. Du kan återskapa antingen administrationsnyckeln, men du kan inte lägga till antalet totala admin. Det finns högst två admin nycklar per söktjänst.

Frågan nycklar skapas som krävs och är utformade för klientprogram som anropar Sök direkt. Du kan skapa upp till 50 frågan nycklar. I programkoden anger du URL: en för sökning och fråga api-nyckel för att tillåta skrivskyddad åtkomst till tjänsten. Programkoden anger också det index som används av ditt program. Tillsammans definiera slutpunkten, en api-nyckel för skrivskyddad åtkomst och ett mål index omfång och åtkomst för anslutningen från klientprogrammet.

Autentisering krävs för varje begäran, där varje begäran består av en obligatorisk för en åtgärd och ett objekt. De två behörighetsnivåerna (fullständig eller skrivskyddad) plus kontext (till exempel en frågeåtgärden i ett index) är tillräckliga för att tillhandahålla fullständig spektrumet säkerhet på tjänståtgärder när kopplats samman. Mer information om nycklar finns [skapa och hantera api-nycklar](search-security-api-keys.md).

## <a name="index-access"></a>Index-åtkomst

Ett enskilt index är inte ett skyddbara objekt i Azure Search. I stället bestäms åtkomst till ett index på tjänstnivå (läs- eller skrivbehörighet), tillsammans med kontexten för en åtgärd.

För slutanvändaren kan strukturerar du frågebegäranden att ansluta med en nyckel för frågan som gör en begäran skrivskyddad och inkludera specifika index som används av din app. Det finns något begrepp om Koppla index eller åt flera index samtidigt så att alla begäranden mål ett index per definition i en fråga. Skapa fråga sig själv (en nyckel plus ett enda mål index) definierar som sådana säkerhetsgränsen.

Administratörers och utvecklares åtkomst till index är odifferentierad: båda ha skrivbehörighet för att skapa, ta bort och uppdatera objekt som hanteras av tjänsten. Vem som helst med en administrationsnyckel till tjänsten kan läsa, ändra eller ta bort alla index i samma tjänst. För skydd mot oavsiktlig eller skadliga borttagning av index är din interna källkontrollen för koden tillgångar remedy för återföring en oönskad index tas bort eller ändras. Azure Search har växling vid fel i klustret för att säkerställa tillgänglighet, men inte lagra och köra egna koden används för att skapa eller läsa in index.

Multitenancy lösningar kräver säkerhetsgränser på indexnivå kan vanligtvis dessa lösningar mellannivå, som kunder använder att hantera index isolering. Mer information om flera användningsfall finns [designmönster för multitenant SaaS-program och Azure Search](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access-from-client-apps"></a>Administratörsåtkomst från klientprogram

Azure Search Management REST API är ett tillägg för Azure Resource Manager och delar av dess beroenden. Därför måste krävs Active Directory för att tjänsten administration av Azure Search. Alla administrativa förfrågningar från klienter måste autentiseras med hjälp av Azure Active Directory innan begäran når Resource Manager.

Data-förfrågningar mot Azure Search-tjänsteslutpunkt, till exempel skapa Index (Azure Söktjänsts-REST API) eller Sök dokument (Azure Söktjänsts-REST API), använda en api-nyckel i huvudet i begäran.

Om din programkod hanterar administration tjänståtgärder samt dataåtgärder på sökindexen eller dokument, implementera två autentiseringsmetoder i koden: snabbtangent inbyggt i Azure Search och Active Directory-autentisering metoder som krävs av Resource Manager. 

Information om hur du strukturerar en begäran i Azure Search finns [Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice/). Läs mer om autentiseringskrav för Resource Manager [autentisering-Använd Resource Manager API för åtkomst prenumerationer](../azure-resource-manager/resource-manager-api-authentication.md).

## <a name="user-access-to-index-content"></a>Användaråtkomst till innehåll för index

Per användare åtkomst till innehållet i ett index implementeras via säkerhetsfilter på dina frågor som returnerar dokument som är associerade med en viss säkerhetsidentitet. I stället för fördefinierade roller och rolltilldelningar har identitetsbaserade åtkomstkontroll implementerats som ett filter som tar bort sökresultat för dokument och innehåll baserat på identiteter. I följande tabell beskrivs två metoder för hur sökresultat av obehöriga innehåll.

| Metoden | Beskrivning |
|----------|-------------|
|[Säkerhet trimning baserat på identiteten filter](search-security-trimming-for-azure-search.md)  | Dokument det grundläggande arbetsflödet för att implementera åtkomstkontroll för användarens identitet. Den omfattar att lägga till säkerhets-ID till ett index och därefter beskrivs filtrering mot det fältet att ta bort resultaten av otillåtna innehåll. |
|[Säkerhet trimning baserat på Azure Active Directory identiteter](search-security-trimming-for-azure-search-with-aad.md)  | Den här artikeln kan utökas med föregående artikel, vilket ger steg för att hämta identiteter från Azure Active Directory (AAD), en av de [kostnadsfria tjänster](https://azure.microsoft.com/free/) i plattformen Azure-molnet. |

## <a name="table-permissioned-operations"></a>Tabeller: Permissioned åtgärder för

I följande tabell sammanfattas de åtgärder som tillåts i Azure Search och vilka nyckeln låser åtkomst till en viss åtgärd.

| Åtgärd | Behörigheter |
|-----------|-------------------------|
| Skapa en tjänst | Innehavaren för Azure-prenumeration|
| Skala en tjänst | Admin-nyckel, RBAC ägare eller deltagare för resursen  |
| Ta bort en tjänst | Admin-nyckel, RBAC ägare eller deltagare för resursen |
| Skapa, ändra, ta bort objekt i tjänsten: <br>Index och delar (inklusive analyzer definitioner, bedömningsprofil profiler, alternativ för CORS), indexerare, datakällor, synonymer, suggesters. | Admin-nyckel, RBAC ägare eller deltagare för resursen  |
| Fråga en index | Administratören eller fråga nyckel (RBAC ej tillämpligt) |
| Fråga Systeminformation, till exempel returnerar statistik, antal och listor över objekt. | Admin-nyckel, RBAC på resurs (ägare, deltagare, läsare) |
| Hantera admin-nycklar | Administrationsnyckeln RBAC ägare eller deltagare för resursen. |
| Hantera frågenycklar |  Administrationsnyckeln RBAC ägare eller deltagare för resursen.  |

## <a name="physical-security"></a>Fysisk säkerhet

Microsoft-datacenter ger branschledande fysisk säkerhet och är kompatibla med en omfattande uppsättning standarder och föreskrifter. Mer information, gå till den [globala Datacenter](https://www.microsoft.com/cloud-platform/global-datacenters) sidan eller titta på en kort video om data center säkerhet.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Se också

+ [Kom igång .NET (visas hur du använder en administratör för att skapa ett index)](search-create-index-dotnet.md)
+ [Kom igång REST (visas hur du använder en administratör för att skapa ett index)](search-create-index-rest-api.md)
+ [Identity-baserad åtkomstkontroll med Azure Search filter](search-security-trimming-for-azure-search.md)
+ [Active Directory identitetsbaserade åtkomstkontroll med Azure Search filter](search-security-trimming-for-azure-search-with-aad.md)
+ [Filter i Azure Search](search-filters.md)