---
title: Säkerhet och data sekretess
titleSuffix: Azure Cognitive Search
description: Azure Kognitiv sökning är kompatibelt med SOC 2, HIPAA och andra certifieringar. Anslutning och data kryptering, autentisering och identitets åtkomst genom säkerhets identifierare för användare och grupper i filter uttryck.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/25/2020
ms.openlocfilehash: 68355ac4238aba3deaa951881bc164fe9dc08e28
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183440"
---
# <a name="security-and-data-privacy-in-azure-cognitive-search"></a>Säkerhet och data sekretess i Azure Kognitiv sökning

Omfattande säkerhetsfunktioner och åtkomst kontroller är inbyggda i Azure Kognitiv sökning för att säkerställa att privat innehåll förblir på det sättet. Den här artikeln räknar upp inbyggda säkerhetsfunktioner och standarder i Azure Kognitiv sökning.

Azure Kognitiv sökning säkerhets arkitekturen omfattar fysisk säkerhet, krypterad överföring, krypterad lagring och kompatibilitet med plattforms oberoende standarder. Azure Kognitiv sökning accepterar bara autentiserade begär Anden. Alternativt kan du lägga till åtkomst kontroller per användare på innehåll via säkerhets filter. Den här artikeln rör säkerhet på varje lager, men fokuserar främst på hur data och åtgärder skyddas i Azure Kognitiv sökning.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Standarder kompatibilitet: ISO 27001, SOC 2, HIPAA

Azure Kognitiv sökning är certifierat för följande standarder, som visas [i juni 2018](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/):

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [SOC 2 typ 2-kompatibilitet](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) Om du vill ha en fullständig rapport går du till [Azure-och Azure Government SOC 2 Type II-rapport](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Hälso vårds barhet och ansvar Act (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR Part 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS nivå 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)

Standardkompatibiliteten gäller för allmänt tillgängliga funktioner. För hands versions funktionerna certifieras när de övergår till allmän tillgänglighet och får inte användas i lösningar som har strikta krav. Certifiering av efterlevnad dokumenteras i [Översikt över Microsoft Azure efterlevnad](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) och [säkerhets Center](https://www.microsoft.com/en-us/trustcenter). 

## <a name="encrypted-transmission-and-storage"></a>Krypterad överföring och lagring

Kryptering utökar hela hela indexerings pipelinen: från anslutningar, via överföring och nedåt till indexerade data som lagras i Azure Kognitiv sökning.

| Säkerhets skikt | Beskrivning |
|----------------|-------------|
| Kryptering under överföring <br>(HTTPS/TLS) | Azure Kognitiv sökning lyssnar på HTTPS-port 443. Anslutningar till Azure-tjänster på plattformen är krypterade. <br/><br/>Alla Azure Kognitiv sökning-interaktioner från klient till tjänst använder TLS 1,2-kryptering. Tidigare versioner (1,0 eller 1,1) stöds inte.|
| Vilande kryptering <br>Microsoft-hanterade nycklar | Kryptering är helt inaktive ras i indexerings processen, utan mätbar påverkan på indexerings tiden för slut för ande eller index storlek. Det sker automatiskt vid all indexering, inklusive för stegvisa uppdateringar av ett index som inte är fullständigt krypterat (skapat före januari 2018).<br><br>Internt är kryptering baserat på [Azure Storage tjänst kryptering](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)med 256-bitars AES- [kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).<br><br> Kryptering är intern för Azure Kognitiv sökning, med certifikat och krypterings nycklar som hanteras internt av Microsoft och som används av universellt. Du kan inte aktivera eller inaktivera kryptering, hantera eller ersätta dina egna nycklar eller Visa krypterings inställningar i portalen eller program mässigt.<br><br>Kryptering i vila presenterades den 24 januari 2018 och gäller för alla tjänst nivåer, inklusive den kostnads fria nivån, i alla regioner. För fullständig kryptering måste index som skapats före det datumet släppas och återskapas för att krypteringen ska ske. Annars krypteras endast nya data som lagts till efter 24 januari.|
| Vilande kryptering <br>Kundhanterade nycklar | Kryptering med Kundhanterade nycklar är nu allmänt tillgängligt för Sök tjänster som skapats den 2019 januari. Det stöds inte på kostnads fria (delade) tjänster.<br><br>Azure Kognitiv sökning index och synonym Maps kan nu krypteras i vila med kundens hanterade nycklar i Azure Key Vault. Läs mer i [Hantera krypterings nycklar i Azure kognitiv sökning](search-security-manage-encryption-keys.md).<br><br>Den här funktionen ersätter inte standard krypteringen i vilo läge, utan gäller även för den.<br><br>Om du aktiverar den här funktionen ökar du antalet index och försämrar frågans prestanda. Utifrån observationer som datum kan du vänta på att se en ökning på 30%-60% i fråge tiderna, även om den faktiska prestandan varierar beroende på index definitionen och typer av frågor. På grund av den här prestandan rekommenderar vi att du bara aktiverar den här funktionen på index som verkligen kräver det.

## <a name="azure-wide-user-access-controls"></a>Azure-wide User Access-kontroller

Flera säkerhetsmekanismer är tillgängliga i hela Azure och är därmed automatiskt tillgängliga för de Azure Kognitiv sökning-resurser som du skapar.

+ [Lås på prenumerations-eller resurs nivå för att förhindra borttagning](../azure-resource-manager/management/lock-resources.md)
+ [Rollbaserad Access Control (RBAC) för att styra åtkomsten till information och administrativa åtgärder](../role-based-access-control/overview.md)

Alla Azure-tjänster har stöd för rollbaserad åtkomst kontroll (RBAC) för att ställa in åtkomst nivåer konsekvent över alla tjänster. Om du till exempel visar känsliga data, till exempel administratörs nyckeln, begränsas rollen ägare och deltagare. Att Visa tjänst status är dock tillgängligt för medlemmar i vilken roll som helst. RBAC tillhandahåller roller för ägare, deltagare och läsare. Som standard är alla tjänst administratörer medlemmar i ägar rollen.

<a name="service-access-and-authentication"></a>

## <a name="endpoint-access"></a>Slut punkts åtkomst

### <a name="public-access"></a>Offentlig åtkomst

Azure Kognitiv sökning ärver säkerhets skyddet för Azure-plattformen och ger sin egen nyckelbaserade autentisering. En API-nyckel är en sträng som består av slumpmässigt genererade siffror och bokstäver. Typ av nyckel (administratör eller fråga) fastställer åtkomst nivån. Inlämning av en giltig nyckel betraktas som bevis för att begäran härstammar från en betrodd entitet. 

Det finns två åtkomst nivåer till din Sök tjänst, som Aktiver ATS av två typer av nycklar:

* Administratörs åtkomst (gäller för alla Läs-och skriv åtgärder mot tjänsten)
* Fråga åtkomst (giltigt för skrivskyddade åtgärder, till exempel frågor, mot dokument samlingen för ett index)

*Administratörs nycklar* skapas när tjänsten är etablerad. Det finns två administratörs nycklar som anges som *primär* och *sekundär* för att hålla dem direkt, men i själva verket är de utbytbara. Varje tjänst har två administratörs nycklar så att du kan återställa en över utan att förlora åtkomsten till tjänsten. Du kan [Återskapa administratörs nyckeln](search-security-api-keys.md#regenerate-admin-keys) regelbundet enligt rekommendationer för Azure-säkerhet, men du kan inte lägga till det totala antalet admin-nycklar. Det finns högst två administrations nycklar per Sök tjänst.

*Frågeinställningar* skapas som de behövs och är utformade för klient program som utfärdar frågor. Du kan skapa upp till 50 frågeinställningar. I program kod anger du Sök-URL och en fråge-API-nyckel för att tillåta skrivskyddad åtkomst till dokument samlingen för ett särskilt index. Tillsammans är slut punkten, en API-nyckel för skrivskyddad åtkomst och ett mål index som definierar omfattning och åtkomst nivå för anslutningen från klient programmet.

Autentisering krävs för varje begäran, där varje begäran består av en obligatorisk nyckel, en åtgärd och ett objekt. Vid samman koppling är de två behörighets nivåerna (fullständig eller skrivskyddad) plus kontexten (till exempel en fråga-åtgärd för ett index) tillräckliga för att tillhandahålla fullständig säkerhet för tjänst åtgärder. Mer information om nycklar finns i [skapa och hantera API-nycklar](search-security-api-keys.md).

### <a name="restricted-access"></a>Begränsad åtkomst

När du har en offentlig tjänst och vill begränsa användningen av tjänsten kan du använda regeln för IP-begränsning i hanterings REST API version: 2020-03-13, [IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule). Med IpRule kan du begränsa åtkomsten till din tjänst genom att identifiera IP-adresser, individuellt eller i ett intervall, som du vill bevilja åtkomst till din Sök tjänst. 

### <a name="private-access"></a>Privat åtkomst

[Privata slut punkter](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) för Azure kognitiv sökning tillåta en klient i ett virtuellt nätverk att på ett säkert sätt få åtkomst till data i ett Sök index över en [privat länk](https://docs.microsoft.com/azure/private-link/private-link-overview). Den privata slut punkten använder en IP-adress från det virtuella nätverkets adress utrymme för Sök tjänsten. Nätverks trafiken mellan klienten och Sök tjänsten passerar över det virtuella nätverket och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet.

[Azure Virtual Network (VNet)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) möjliggör säker kommunikation mellan resurser, med ditt lokala nätverk och Internet. 

## <a name="index-access"></a>Index åtkomst

I Azure Kognitiv sökning är ett enskilt index inte ett skydds Bart objekt. I stället fastställs åtkomst till ett index på tjänst lagret (Läs-eller Skriv behörighet), tillsammans med kontexten för en åtgärd.

För slut användar åtkomst kan du strukturera förfrågningar om att ansluta med hjälp av en frågegrupp, som gör begäran skrivskyddad och inkludera det särskilda index som används av din app. I en förfrågan finns det ingen idé att koppla index eller att komma åt flera index samtidigt, så att alla begär Anden är riktade mot ett enda index med definition. Därför definierar själva begäran (en nyckel plus ett enskilt mål index) säkerhets gränserna.

Administratörs-och utvecklarens åtkomst till index är inte differentierad: båda behöver Skriv behörighet för att skapa, ta bort och uppdatera objekt som hanteras av tjänsten. Alla med en administratörs nyckel till din tjänst kan läsa, ändra eller ta bort alla index i samma tjänst. För att skydda mot oavsiktlig eller skadlig borttagning av index, är din interna käll kontroll för kod till gångar en påföljd för att återföra en oönskad index borttagning eller ändring. Azure Kognitiv sökning har redundans i klustret för att säkerställa tillgängligheten, men den lagrar eller kör inte din egna kod som används för att skapa eller läsa in index.

För lösningar med flera innehavare som kräver säkerhets gränser på index nivån omfattar sådana lösningar vanligt vis en mellan nivå, som kunder använder för att hantera index isolering. Mer information om användnings fallet för flera innehavare finns i [design mönster för SaaS-program för flera innehavare och Azure kognitiv sökning](search-modeling-multitenant-saas-applications.md).

## <a name="authentication"></a>Autentisering

### <a name="admin-access"></a>Administratörs åtkomst

[Rollbaserad åtkomst (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) avgör om du har åtkomst till kontroller över tjänsten och dess innehåll. Om du är ägare eller deltagare i en Azure Kognitiv sökning-tjänst kan du använda portalen eller PowerShell- **AZ. search** -modulen för att skapa, uppdatera eller ta bort objekt i tjänsten. Du kan också använda [Azure kognitiv sökning Management-REST API](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

### <a name="user-access"></a>Användaråtkomst

Som standard bestäms användar åtkomst till ett index av åtkomst nyckeln för förfrågan. De flesta utvecklare skapar och tilldelar [*frågeinställningar*](search-security-api-keys.md) för Sök begär Anden på klient sidan. En frågegrupp ger Läs behörighet till allt innehåll i indexet.

Om du behöver detaljerad kontroll över innehåll för varje användare kan du bygga säkerhets filter för dina frågor och returnera dokument som är associerade med en viss säkerhets identitet. I stället för fördefinierade roller och roll tilldelningar implementeras identitetsbaserade åtkomst kontroller som ett *filter* som trimmar Sök Resultat för dokument och innehåll baserat på identiteter. I följande tabell beskrivs två metoder för att trimma Sök Resultat av obehörigt innehåll.

| Metod | Beskrivning |
|----------|-------------|
|[Säkerhets trimning baserat på identitets filter](search-security-trimming-for-azure-search.md)  | Dokumenterar det grundläggande arbets flödet för att implementera åtkomst kontroll för användar identitet. Det omfattar att lägga till säkerhets identifierare i ett index och sedan förklarar filtreringen för fältet för att trimma resultat från otillåtet innehåll. |
|[Säkerhets trimning baserat på Azure Active Directory identiteter](search-security-trimming-for-azure-search-with-aad.md)  | Den här artikeln är utökad i föregående artikel, som innehåller steg för att hämta identiteter från Azure Active Directory (AAD), en av de [kostnads fria tjänsterna](https://azure.microsoft.com/free/) i Azure Cloud Platform. |

## <a name="table-permissioned-operations"></a>Tabell: behörighets åtgärder

I följande tabell sammanfattas de åtgärder som tillåts i Azure Kognitiv sökning och vilken nyckel som låser upp åtkomsten till en viss åtgärd.

| Åtgärd | Behörigheter |
|-----------|-------------------------|
| Skapa en tjänst | Azure-prenumerations innehavare|
| Skala en tjänst | Administratörs nyckel, RBAC-ägare eller deltagare på resursen  |
| Ta bort en tjänst | Administratörs nyckel, RBAC-ägare eller deltagare på resursen |
| Skapa, ändra och ta bort objekt på tjänsten: <br>Index och komponent delar (inklusive analys definitioner, bedömnings profiler, CORS-alternativ), indexerare, data källor, synonymer, förslag. | Administratörs nyckel, RBAC-ägare eller deltagare på resursen  |
| Fråga ett index | Administratör eller frågegrupp (RBAC-ej tillämpligt) |
| Fråga system information, till exempel för att returnera statistik, antal och listor över objekt. | Administratörs nyckel, RBAC på resursen (ägare, deltagare, läsare) |
| Hantera administratörs nycklar | Administratörs nyckel, RBAC-ägare eller deltagare på resursen. |
| Hantera frågeinställningar |  Administratörs nyckel, RBAC-ägare eller deltagare på resursen.  |

## <a name="physical-security"></a>Fysisk säkerhet

Microsoft Data Center ger branschledande fysiska säkerhet och är kompatibla med en omfattande portfölj med standarder och föreskrifter. Om du vill veta mer går du till sidan [globala data Center](https://www.microsoft.com/cloud-platform/global-datacenters) eller tittar på en kort video om säkerhet i Data Center.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Se även

+ [Kom igång med .NET (visar hur du använder en administratörs nyckel för att skapa ett index)](search-create-index-dotnet.md)
+ [Kom igång REST (visar hur man använder en administratörs nyckel för att skapa ett index)](search-create-index-rest-api.md)
+ [Identitets-baserad åtkomst kontroll med Azure Kognitiv sökning filter](search-security-trimming-for-azure-search.md)
+ [Active Directory identitets baserad åtkomst kontroll med Azure Kognitiv sökning-filter](search-security-trimming-for-azure-search-with-aad.md)
+ [Filter i Azure Kognitiv sökning](search-filters.md)