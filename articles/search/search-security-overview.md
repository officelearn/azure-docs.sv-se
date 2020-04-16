---
title: Säkerhet och datasekretess
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search är kompatibelt med SOC 2, HIPAA och andra certifieringar. Anslutnings- och datakryptering, autentisering och identitetsåtkomst via användar- och gruppsäkerhetsidentifierare i filteruttryck.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: fe7d076fab6a70736843fc644cd56bef44a55df2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415129"
---
# <a name="security-and-data-privacy-in-azure-cognitive-search"></a>Säkerhet och datasekretess i Azure Cognitive Search

Omfattande säkerhetsfunktioner och åtkomstkontroller är inbyggda i Azure Cognitive Search för att säkerställa att privat innehåll förblir så. Den här artikeln räknar upp säkerhetsfunktioner och standarder som är inbyggda i Azure Cognitive Search.

Azure Cognitive Search-säkerhetsarkitekturen omfattar fysisk säkerhet, krypterade överföringar, krypterad lagring och efterlevnad av standardnormer för hela plattformen. I drift accepterar Azure Cognitive Search endast autentiserade begäranden. Du kan också lägga till åtkomstkontroller per användare på innehåll via säkerhetsfilter. Den här artikeln berör säkerhet i varje lager, men fokuserar främst på hur data och åtgärder skyddas i Azure Cognitive Search.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Standarder överensstämmelse: ISO 27001, SOC 2, HIPAA

Azure Cognitive Search är certifierat för följande standarder, enligt vad som [tillkännagavs i juni 2018:](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/)

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [SOC 2 Typ 2-efterlevnad](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) Den fullständiga rapporten finns i [Azure – och Azure Government SOC 2 Type II Report](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Health Insurance Portability and Accountability Act (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR Del 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST (PÅ)](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS nivå 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)

Standardefterlevnad gäller för allmänt tillgängliga funktioner. Förhandsversionsfunktioner är certifierade när de övergår till allmän tillgänglighet och får inte användas i lösningar som har strikta standardkrav. Efterlevnadscertifiering dokumenteras i [Översikt över Microsoft Azure-efterlevnad](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) och [Trust Center](https://www.microsoft.com/en-us/trustcenter). 

## <a name="encrypted-transmission-and-storage"></a>Krypterad överföring och lagring

Krypteringen utökas i hela indexeringspipelinen: från anslutningar, genom överföring och ned till indexerade data som lagras i Azure Cognitive Search.

| Säkerhetslager | Beskrivning |
|----------------|-------------|
| Kryptering under överföring <br>(HTTPS/SSL/TLS) | Azure Cognitive Search lyssnar på HTTPS-port 443. På plattformen krypteras anslutningar till Azure-tjänster. <br/><br/>Alla Azure Cognitive Search-interaktioner från klient till tjänst är SSL/TLS 1.2 som kan.  Var noga med att använda TLSv1.2 för SSL-anslutningar till din tjänst.|
| Vilande kryptering <br>Microsoft hanterade nycklar | Krypteringen är helt internaliserad i indexeringsprocessen, utan mätbar inverkan på indexering av tid till slutförande eller indexstorlek. Den sker automatiskt på all indexering, inklusive på inkrementella uppdateringar av ett index som inte är helt krypterat (skapas före januari 2018).<br><br>Internt baseras krypteringen på [Azure Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), med 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).<br><br> Kryptering är internt för Azure Cognitive Search, med certifikat och krypteringsnycklar som hanteras internt av Microsoft och tillämpas universellt. Du kan inte aktivera eller inaktivera kryptering, hantera eller ersätta dina egna nycklar eller visa krypteringsinställningar i portalen eller programmässigt.<br><br>Kryptering i vila tillkännagavs i januari 24, 2018 och gäller för alla tjänstnivåer, inklusive den kostnadsfria nivån, i alla regioner. För fullständig kryptering måste index som skapats före det datumet tas bort och återskapas för att kryptering ska kunna ske. Annars krypteras endast nya data som läggs till efter den 24 januari.|
| Vilande kryptering <br>Kundhanterade nycklar | Kryptering med kundhanterade nycklar är nu allmänt tillgänglig för söktjänster som skapats på eller efter januari 2019. Det stöds inte på kostnadsfria (delade) tjänster.<br><br>Azure Cognitive Search index och synonymmappningar kan nu krypteras i vila med kundhanterade nycklar i Azure Key Vault. Mer information finns [i Hantera krypteringsnycklar i Azure Cognitive Search](search-security-manage-encryption-keys.md).<br><br>Den här funktionen ersätter inte standardkrypteringen i vila, utan tillämpas utöver den.<br><br>Om du aktiverar den här funktionen ökar indexstorleken och frågeprestanda försämras. Baserat på observationer hittills kan du förvänta dig att se en ökning med 30%-60% i frågetider, även om den faktiska prestandan varierar beroende på indexdefinition och typer av frågor. På grund av den här prestandapåverkan rekommenderar vi att du bara aktiverar den här funktionen på index som verkligen kräver det.

## <a name="azure-wide-user-access-controls"></a>Azure-omfattande användaråtkomstkontroller

Flera säkerhetsmekanismer är tillgängliga Azure-wide och därmed automatiskt tillgängliga för Azure Cognitive Search-resurser som du skapar.

+ [Lås på prenumerations- eller resursnivå för att förhindra borttagning](../azure-resource-manager/management/lock-resources.md)
+ [Rollbaserad åtkomstkontroll (RBAC) för att kontrollera åtkomsten till information och administrativa åtgärder](../role-based-access-control/overview.md)

Alla Azure-tjänster stöder rollbaserade åtkomstkontroller (RBAC) för att ange åtkomstnivåer konsekvent för alla tjänster. Att till exempel visa känsliga data, till exempel administratörsnyckeln, är begränsad till rollerna Ägare och Deltagare. Visa tjänststatus är dock tillgänglig för medlemmar i alla roller. RBAC tillhandahåller roller ägare, deltagare och läsare. Som standard är alla tjänstadministratörer medlemmar i ägarrollen.

<a name="service-access-and-authentication"></a>

## <a name="endpoint-access"></a>Åtkomst till slutpunkt

### <a name="public-access"></a>Allmänhetens tillgång

Azure Cognitive Search ärver säkerhetsskydden för Azure-plattformen och tillhandahåller sin egen nyckelbaserade autentisering. En api-nyckel är en sträng som består av slumpmässigt genererade siffror och bokstäver. Typen av nyckel (administratör eller fråga) bestämmer åtkomstnivån. Inlämning av en giltig nyckel anses vara ett bevis på att begäran kommer från en betrodd entitet. 

Det finns två åtkomstnivåer till söktjänsten, aktiverade av två typer av nycklar:

* Administratörsåtkomst (gäller för alla läs-skriv-åtgärder mot tjänsten)
* Frågeåtkomst (gäller för skrivskyddade åtgärder, till exempel frågor, mot dokumentsamlingen för ett index)

*Administratörsnycklar* skapas när tjänsten etableras. Det finns två admin nycklar, betecknas som *primära* och *sekundära* för att hålla dem raka, men i själva verket är de utbytbara. Varje tjänst har två administratörsnycklar så att du kan rulla en över utan att förlora åtkomsten till din tjänst. Du kan [återskapa administratörsnyckeln](search-security-api-keys.md#regenerate-admin-keys) med jämna mellanrum enligt bästa azure-säkerhetspraxis, men du kan inte lägga till det totala antalet administratörsnyckeler. Det finns högst två administratörsnycklar per söktjänst.

*Frågenycklar* skapas efter behov och är utformade för klientprogram som utfärdar frågor. Du kan skapa upp till 50 frågenycklar. I programkoden anger du sök-URL:en och en fråge-api-nyckel för att tillåta skrivskyddad åtkomst till dokumentsamlingen för ett visst index. Tillsammans definierar slutpunkten, en api-nyckel för skrivskyddad åtkomst och ett målindex omfattningen och åtkomstnivån för anslutningen från klientprogrammet.

Autentisering krävs på varje begäran, där varje begäran består av en obligatorisk nyckel, en åtgärd och ett objekt. När de två behörighetsnivåerna (fullständig eller skrivskyddad) är sammankopplade är de två behörighetsnivåerna (fullständiga eller skrivskyddade) plus kontexten (till exempel en frågeåtgärd på ett index) tillräckliga för att tillhandahålla fullspektrumsäkerhet för tjänståtgärder. Mer information om nycklar finns i [Skapa och hantera api-nycklar](search-security-api-keys.md).

### <a name="restricted-access"></a>Begränsad åtkomst

När du har en offentlig tjänst och du vill begränsa användningen av tjänsten kan du använda IP-begränsningsregeln i REST API-versionen för hantering: 2020-03-13, [IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule). IpRule ger dig möjlighet att begränsa åtkomsten till din tjänst genom att identifiera IP-adresser, individuellt eller i ett intervall, som du vill ge åtkomst till din söktjänst. 

### <a name="private-access"></a>Privat åtkomst

[Privata slutpunkter](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) för Azure Cognitive Search gör det möjligt för en klient i ett virtuellt nätverk att på ett säkert sätt komma åt data i ett sökindex via en [privat länk](https://docs.microsoft.com/azure/private-link/private-link-overview). Den privata slutpunkten använder en IP-adress från det virtuella nätverksadressutrymmet för söktjänsten. Nätverkstrafik mellan klienten och söktjänsten passerar över det virtuella nätverket och en privat länk i Microsofts stamnät, vilket eliminerar exponering från det offentliga internet.

[Azure Virtual Network (VNet)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) möjliggör säker kommunikation mellan resurser, med ditt lokala nätverk och Internet. 

## <a name="index-access"></a>Tillgång till index

I Azure Cognitive Search är ett enskilt index inte ett objekt som kan värdepapperas. I stället bestäms åtkomsten till ett index på servicelagret (läs- eller skrivåtkomst), tillsammans med kontexten för en åtgärd.

För slutanvändarens åtkomst kan du strukturera frågebegäranden för att ansluta med hjälp av en frågenyckel, vilket gör alla begäranden skrivskyddade och inkluderar det specifika index som används av din app. I en frågebegäran finns det inget begrepp om att sammanfoga index eller komma åt flera index samtidigt så att alla begäranden riktar in sig på ett enda index per definition. Därför definierar konstruktionen av själva frågebegäran (en nyckel plus ett enda målindex) säkerhetsgränsen.

Administratörs- och utvecklaråtkomst till index är odifferentierad: båda behöver skrivåtkomst för att skapa, ta bort och uppdatera objekt som hanteras av tjänsten. Alla som har en administratörsnyckel till din tjänst kan läsa, ändra eller ta bort alla index i samma tjänst. För skydd mot oavsiktlig eller skadlig borttagning av index är din interna källkontroll för kodtillgångar ett botemedel mot att vända en oönskad indexborttagning eller ändring. Azure Cognitive Search har redundans i klustret för att säkerställa tillgänglighet, men den lagrar eller kör inte din egen kod som används för att skapa eller läsa in index.

För multitenancy-lösningar som kräver säkerhetsgränser på indexnivå innehåller sådana lösningar vanligtvis en mellannivå som kunderna använder för att hantera indexisolering. Mer information om användningsfallet för flera emande finns i [Designmönster för SaaS-program med flera egenskaper och Azure Cognitive Search](search-modeling-multitenant-saas-applications.md).

## <a name="authentication"></a>Autentisering

### <a name="admin-access"></a>Åtkomst till administratör

[Rollbaserad åtkomst (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) avgör om du har åtkomst till kontroller över tjänsten och dess innehåll. Om du är ägare eller deltagare i en Azure Cognitive Search-tjänst kan du använda portalen eller PowerShell **Az.Search-modulen** för att skapa, uppdatera eller ta bort objekt på tjänsten. Du kan också använda REST API FÖR [Azure Cognitive Search Management.You](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)can also use the Azure Cognitive Search Management REST API .

### <a name="user-access"></a>Användaråtkomst

Som standard bestäms användaråtkomst till ett index av åtkomstnyckeln på frågebegäran. De flesta utvecklare skapar och tilldelar [*frågenycklar*](search-security-api-keys.md) för sökbegäranden på klientsidan. En frågenyckel ger läsåtkomst till allt innehåll i indexet.

Om du behöver detaljerad, per användare kontroll över innehållet kan du skapa säkerhetsfilter på dina frågor och returnera dokument som är associerade med en viss säkerhetsidentitet. I stället för fördefinierade roller och rolltilldelningar implementeras identitetsbaserad åtkomstkontroll som ett *filter* som trimmar sökresultat för dokument och innehåll baserat på identiteter. I följande tabell beskrivs två metoder för att trimma sökresultat av obehörigt innehåll.

| Metod | Beskrivning |
|----------|-------------|
|[Säkerhetstrimning baserat på identitetsfilter](search-security-trimming-for-azure-search.md)  | Dokumenterar det grundläggande arbetsflödet för implementering av användaridentitetsåtkomstkontroll. Det omfattar att lägga till säkerhetsidentifierare i ett index och sedan förklaras filtrering mot det fältet för att trimma resultat av förbjudet innehåll. |
|[Säkerhetstrimning baserat på Azure Active Directory-identiteter](search-security-trimming-for-azure-search-with-aad.md)  | Den här artikeln utökar den föregående artikeln och innehåller steg för att hämta identiteter från Azure Active Directory (AAD), en av de [kostnadsfria tjänsterna](https://azure.microsoft.com/free/) i Azure-molnplattformen. |

## <a name="table-permissioned-operations"></a>Tabell: Behörighetsåtgärder

I följande tabell sammanfattas de åtgärder som tillåts i Azure Cognitive Search och vilken nyckel som låser upp åtkomst till en viss åtgärd.

| Åtgärd | Behörigheter |
|-----------|-------------------------|
| Skapa en tjänst | Azure-prenumerationsinnehavare|
| Skala en tjänst | Administratörsnyckel, RBAC-ägare eller deltagare på resursen  |
| Ta bort en tjänst | Administratörsnyckel, RBAC-ägare eller deltagare på resursen |
| Skapa, ändra, ta bort objekt på tjänsten: <br>Index och komponentdelar (inklusive analysatordefinitioner, bedömningsprofiler, CORS-alternativ), indexerare, datakällor, synonymer, förslagsställare. | Administratörsnyckel, RBAC-ägare eller deltagare på resursen  |
| Fråga ett index | Admin- eller frågenyckel (RBAC är inte tillämpligt) |
| Frågesysteminformation, till exempel returnera statistik, antal och listor över objekt. | Admin nyckel, RBAC på resursen (Ägare, Deltagare, Reader) |
| Hantera administratörsnycklar | Administratörsnyckel, RBAC-ägare eller deltagare på resursen. |
| Hantera frågenycklar |  Administratörsnyckel, RBAC-ägare eller deltagare på resursen.  |

## <a name="physical-security"></a>Fysisk säkerhet

Microsofts datacenter ger branschledande fysisk säkerhet och följer en omfattande portfölj av standarder och föreskrifter. Mer information finns på sidan [Globala datacenter](https://www.microsoft.com/cloud-platform/global-datacenters) eller tittar på en kort video om datacentersäkerhet.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Se även

+ [Komma igång .NET (demonstrerar med hjälp av en administratörsnyckel för att skapa ett index)](search-create-index-dotnet.md)
+ [Komma igång REST (demonstrerar med hjälp av en administratörsnyckel för att skapa ett index)](search-create-index-rest-api.md)
+ [Identitetsbaserad åtkomstkontroll med hjälp av Azure Cognitive Search-filter](search-security-trimming-for-azure-search.md)
+ [Active Directory-identitetsbaserad åtkomstkontroll med hjälp av Azure Cognitive Search-filter](search-security-trimming-for-azure-search-with-aad.md)
+ [Filter i Azure Cognitive Search](search-filters.md)