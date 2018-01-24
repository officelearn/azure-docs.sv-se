---
title: "Skydda data och åtgärder i Azure Search | Microsoft Docs"
description: "Azure Search-säkerhet baseras på SOC 2 kompatibilitet, kryptering, autentisering och identitet åtkomst via användare och grupp säkerhets-ID i Azure Search filter."
services: search
documentationcenter: 
author: HeidiSteen
manager: cgronlun
editor: 
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/19/2018
ms.author: heidist
ms.openlocfilehash: c3aa4883e33b1f3494f8502fe7f8b12f7d64a72f
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="security-and-controlled-access-in-azure-search"></a>Säkerhet och kontrollerad åtkomst i Azure Search

Azure Search är [SOC 2 kompatibla](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports), med en omfattande arkitektur spanning fysisk säkerhet, krypterade överföringar, krypterade lagring och plattform hela programvara skydd. Azure Search funktionsmässigt krävs accepterar bara autentiserade begäranden. Du kan också kan du lägga till kontroller för varje användare åtkomst för innehåll. Den här artikeln vidrör på säkerheten på varje nivå, men är i första hand på hur data och åtgärder skyddas i Azure Search.

![Blockdiagram där säkerhetslager](media/search-security-overview/azsearch-security-diagram.png)

## <a name="physical-security"></a>Fysisk säkerhet

Microsoft-datacenter ger branschledande fysisk säkerhet och är kompatibla med en omfattande uppsättning standarder och föreskrifter. Mer information, gå till den [globala Datacenter](https://www.microsoft.com/cloud-platform/global-datacenters) sidan eller titta på en kort video om data center säkerhet.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]

## <a name="encrypted-transmission-and-storage"></a>Krypterad överföring och lagring

Kryptering utökar under hela den hela pipelinen för fulltextindexering: från anslutningar via överföring och nedåt indexerade data som lagras i Azure Search.

| Säkerhetsskiktet | Beskrivning |
|----------------|-------------|
| Kryptering under överföring | Azure Search lyssnar på HTTPS-port 443. Anslutningar till Azure-tjänster krypteras över hela plattformen. |
| Vilande kryptering | Kryptering är fullständigt internalized i indexering processen utan mätbara inverkan på indexering tid för slutförande eller Indexstorlek. Det sker automatiskt på alla indexering, inklusive på inkrementella uppdateringar till ett index som inte är fullständigt krypterade (som skapats före januari 2018).<br><br>Internt, kryptering baseras på [Azure Storage Service-kryptering](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), med hjälp av 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).|
| [Kompatibilitet för SOC 2](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) | Alla search-tjänster är fullständigt AICPA SOC 2 kompatibla, alla Datacenter tillhandahåller Azure Search. Om du vill granska hela rapporten, gå till [Azure- och Azure Government SOC 2 II rapport](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). |

Kryptering är interna för Azure Search med certifikat och krypteringsnycklar hanteras internt av Microsoft och tillämpas universellt. Du kan inte aktivera kryptering eller inaktivera, hantera eller ersätta egna nycklar eller visa krypteringsinställningar i portalen eller programmässigt. 

Kryptering i vila har angivits i 24 januari 2018 och gäller för alla servicenivåer, inklusive delade (kostnadsfritt), i alla regioner. För fullständig kryptering index som skapades före denna tidpunkt släppas och återskapas för att kryptering ska ske. Annars krypteras endast nya data som lagts till efter 24 januari.

## <a name="azure-wide-logical-security"></a>Azure hela logisk säkerhet

Flera säkerhetsmekanismer finns tillgängliga via Azure-stacken och därmed automatiskt tillgängliga i Azure Search-resurser som du skapar.

+ [Lås i prenumerationen eller resursen nivå för att förhindra att tas bort](../azure-resource-manager/resource-group-lock-resources.md)
+ [Rollbaserad åtkomstkontroll (RBAC) att styra åtkomsten till information och administrativa åtgärder](../active-directory/role-based-access-control-what-is.md)

Alla Azure-tjänster stöder rollbaserad åtkomstkontroll (RBAC) för att ställa in åtkomstnivåer konsekvent över alla tjänster. Till exempel är visa känsliga data, till exempel admin-nyckel begränsad till ägare och deltagare roller, visa Tjänststatus för är tillgänglig för medlemmar i någon roll. RBAC ger ägare, deltagare och läsare. Som standard är alla administratörer medlemmar i rollen som ägare.

## <a name="service-access-and-authentication"></a>Åtkomst till tjänsten och autentisering

Medan Azure Search ärver säkerhetsåtgärder i Azure-plattformen, ger också en egen nyckel för autentisering. Typ av nyckel (admin eller fråga) avgör vilken åtkomstnivå. Överföring av en giltig nyckel anses bevis begäran kommer från en betrodd enhet. 

Autentisering krävs för varje begäran, där varje begäran består av en obligatorisk för en åtgärd och ett objekt. När kopplats samman räcker två behörighetsnivåer (fullständig eller skrivskyddad) plus kontexten för att tillhandahålla fullständig spektrumet säkerhet på tjänståtgärder. 

|Nyckel|Beskrivning|Begränsningar|  
|---------|-----------------|------------|  
|Administratör|Ger fullständig behörighet till alla åtgärder, inklusive möjligheten att hantera tjänsten, skapa och ta bort index, indexerare och datakällor.<br /><br /> Två admin **api-nycklar**, kallas *primära* och *sekundära* i portalen genereras när tjänsten har skapats och individuellt genereras på begäran . Har två nycklar kan du återställa över en nyckel när du använder den andra nyckeln för fortsatt tillgång till tjänsten.<br /><br /> Admin nycklar endast anges i HTTP-huvuden för begäran. Du kan placera en admin api-nyckel i en URL.|Maximalt 2 per tjänst|  
|Fråga|Ger läsbehörighet till index och dokument och distribueras vanligen till klientprogram som kan utfärda search-begäranden.<br /><br /> Frågan nycklar skapas på begäran. Du kan skapa dem manuellt på portalen eller programmässigt via den [Management REST API](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Frågan nycklar kan anges i ett HTTP-huvud för begäran för sökning, förslag eller sökning. Du kan också ange en fråga nyckel som en parameter på en URL. Beroende på hur ditt klientprogram formulates begäran, kan det vara enklast att överföra nyckeln som en frågeparameter:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01&api-key=A8DA81E03F809FE166ADDB183E9ED84D`|50 per tjänst|  

 Visuellt, görs ingen åtskillnad mellan en administrationsnyckeln eller Frågenyckeln. Båda nycklarna genereras strängar som består av 32 slumpmässigt alfanumeriska tecken. Om du tappar bort reda på vilken typ av nyckel har angetts i ditt program kan du [Kontrollera nyckelvärdena i portalen](https://portal.azure.com) eller använda den [REST API](https://docs.microsoft.com/rest/api/searchmanagement/) att returnera värdet och nyckeltyp.  

> [!NOTE]  
>  En dålig säkerhetsrutin att skicka känslig information som anses en `api-key` i URI-begäran. Därför accepterar Azure Search bara en fråga nyckel som en `api-key` i frågan sträng, och du bör undvika att du gör det om innehållet i ditt index måste vara offentligt tillgängliga. Som en allmän regel rekommenderar vi skicka din `api-key` som ett huvud.  

### <a name="how-to-find-the-access-keys-for-your-service"></a>Så här hittar du åtkomstnycklarna för tjänsten

Du kan hämta snabbtangenter i portalen eller via den [Management REST API](https://docs.microsoft.com/rest/api/searchmanagement/). Mer information finns i [hantera nycklar](search-manage.md#manage-api-keys).

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Lista de [söktjänster](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) för din prenumeration.
3. Välj tjänsten och hitta på sidan service **inställningar** >**nycklar** att visa administratör och fråga nycklar.

![Portalsida, inställningar, nycklar avsnitt](media/search-security-overview/settings-keys.png)

## <a name="index-access"></a>Index-åtkomst

Ett enskilt index är inte ett skyddbara objekt i Azure Search. I stället bestäms åtkomst till ett index på tjänstnivå (läs- eller skrivbehörighet), tillsammans med kontexten för en åtgärd.

När det gäller slutanvändarnas åtkomst strukturerar du frågebegäranden i ditt program att ansluta med en nyckel för frågan som gör en begäran skrivskyddad och inkludera specifika index som används av din app. Det finns något begrepp om Koppla index eller åt flera index samtidigt så att alla begäranden mål ett index per definition i en fråga. Därför definierar strukturen för fråga sig själv (en nyckel plus ett enda mål index) säkerhetsgränsen.

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
| Hantera frågenycklar |  Administrationsnyckeln RBAC ägare eller deltagare för resursen. RBAC läsare kan visa frågan nycklar. |


## <a name="see-also"></a>Se också

+ [Kom igång .NET (visas hur du använder en administratör för att skapa ett index)](search-create-index-dotnet.md)
+ [Kom igång REST (visas hur du använder en administratör för att skapa ett index)](search-create-index-rest-api.md)
+ [Identity-baserad åtkomstkontroll med Azure Search filter](search-security-trimming-for-azure-search.md)
+ [Active Directory identitetsbaserade åtkomstkontroll med Azure Search filter](search-security-trimming-for-azure-search-with-aad.md)
+ [Filter i Azure Search](search-filters.md)