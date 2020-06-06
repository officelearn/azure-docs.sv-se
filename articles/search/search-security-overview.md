---
title: Säkerhetsöversikt
titleSuffix: Azure Cognitive Search
description: Azure Kognitiv sökning är kompatibelt med SOC 2, HIPAA och andra certifieringar. Anslutning och data kryptering, autentisering och identitets åtkomst genom säkerhets identifierare för användare och grupper i filter uttryck.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: fb79c3546037aabf5ce60905044901f0d5793990
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465634"
---
# <a name="security-in-azure-cognitive-search---overview"></a>Säkerhet i Azure Kognitiv sökning – översikt

I den här artikeln beskrivs viktiga säkerhetsfunktioner i Azure Kognitiv sökning som kan skydda innehåll och åtgärder. 

+ Vid lagrings nivån är kryptering vid vila en på plattforms nivån, men Kognitiv sökning erbjuder även alternativet "Double Encryption" för kunder som vill ha det dubbla skyddet av både användar-och Microsoft-hanterade nycklar.

+ Inkommande säkerhet skyddar Sök tjänstens slut punkt på ökade säkerhets nivåer: från API-nycklar på begäran till inkommande regler i brand väggen till privata slut punkter som helt skyddar din tjänst från det offentliga Internet.

+ Utgående säkerhet gäller indexerare som hämtar innehåll från externa källor. För utgående begär Anden ställer du in en hanterad identitet för att söka efter en betrodd tjänst vid åtkomst till data från Azure Storage, Azure SQL, Cosmos DB eller andra Azure-datakällor. En hanterad identitet är en ersättning för autentiseringsuppgifter eller åtkomst nycklar på anslutningen. Utgående säkerhet beskrivs inte i den här artikeln. Mer information om den här funktionen finns i [Anslut till en data källa med hjälp av en hanterad identitet](search-howto-managed-identities-data-sources.md).

Titta på den här videon med snabb takt för en översikt över säkerhets arkitekturen och varje funktions kategori.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="encrypted-transmissions-and-storage"></a>Krypterad överföring och lagring

Kryptering är genomgripande i Azure Kognitiv sökning, från och med anslutningar och överföring, som utökas till innehåll som lagras på disk. För Sök tjänster på det offentliga Internet lyssnar Azure Kognitiv sökning på HTTPS-port 443. Alla klient-till-tjänst-anslutningar använder TLS 1,2-kryptering. Tidigare versioner (1,0 eller 1,1) stöds inte.

### <a name="data-encryption-at-rest"></a>Vilande datakryptering

Azure Kognitiv sökning lagrar index definitioner och innehåll, definitioner av data källor, Indexer definitioner, färdigheter-definitioner och synonym Maps.

Data krypteras på disk via lagrings lagret med hjälp av nycklar som hanteras av Microsoft. Du kan inte aktivera eller inaktivera kryptering eller Visa krypterings inställningar i portalen eller program mässigt. Kryptering är helt inaktive ras, utan mätbar påverkan på indexerings tiden för slut för ande eller index storlek. Det sker automatiskt vid all indexering, inklusive för stegvisa uppdateringar av ett index som inte är fullständigt krypterat (skapat före januari 2018).

Internt är kryptering baserat på [Azure Storage tjänst kryptering](../storage/common/storage-service-encryption.md)med 256-bitars AES- [kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).

> [!NOTE]
> Kryptering i vila presenterades den 24 januari 2018 och gäller för alla tjänst nivåer, inklusive den kostnads fria nivån, i alla regioner. För fullständig kryptering måste index som skapats före det datumet släppas och återskapas för att krypteringen ska ske. Annars krypteras endast nya data som lagts till efter 24 januari.

### <a name="customer-managed-key-cmk-encryption"></a>CMK-kryptering (Customer Managed Key)

Kunder som vill ha ytterligare lagrings skydd kan kryptera data och objekt innan de lagras och krypteras på disk. Den här metoden baseras på en användardefinierad nyckel som hanteras och lagras via Azure Key Vault, oberoende av Microsoft. Kryptering av innehåll innan det krypteras på disk kallas "Double Encryption". För närvarande kan du selektivt dubblera kryptering av index och synonymer. Mer information finns i [kund hanterade krypterings nycklar i Azure kognitiv sökning](search-security-manage-encryption-keys.md).

> [!NOTE]
> CMK-kryptering är allmänt tillgängligt för Sök tjänster som skapats efter 2019 januari. Det stöds inte på kostnads fria (delade) tjänster. 
>
>Om du aktiverar den här funktionen ökar du antalet index och försämrar frågans prestanda. Utifrån observationer som datum kan du vänta på att se en ökning på 30%-60% i fråge tiderna, även om den faktiska prestandan varierar beroende på index definitionen och typer av frågor. På grund av den här prestandan rekommenderar vi att du bara aktiverar den här funktionen på index som verkligen kräver det.

<a name="service-access-and-authentication"></a>

## <a name="inbound-security-and-endpoint-protection"></a>Inkommande säkerhet och Endpoint Protection

Inkommande säkerhetsfunktioner skyddar Sök tjänstens slut punkt genom att öka säkerhets-och komplexitets nivåerna. För det första krävs en API-nyckel för autentiserad åtkomst för alla begär Anden. För det andra kan du ange brand Väggs regler som begränsar åtkomsten till vissa IP-adresser. För avancerat skydd är ett tredje alternativ att aktivera Azures privata länk för att skydda tjänstens slut punkt från all Internet trafik.

### <a name="public-access-using-api-keys"></a>Offentlig åtkomst med API-nycklar

Som standard nås en Sök tjänst via det offentliga molnet med hjälp av nyckelbaserad autentisering för administratörs-eller frågans åtkomst till Sök tjänstens slut punkt. En API-nyckel är en sträng som består av slumpmässigt genererade siffror och bokstäver. Typ av nyckel (administratör eller fråga) fastställer åtkomst nivån. Inlämning av en giltig nyckel betraktas som bevis för att begäran härstammar från en betrodd entitet. 

Det finns två åtkomst nivåer till din Sök tjänst, som aktive ras med följande API-nycklar:

+ Administratörs nyckel (tillåter Läs-och skriv åtkomst för [create-Read-Update-Delete-Delete-Delete-](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) åtgärder på Sök tjänsten)

+ Fråga efter nyckel (tillåter skrivskyddad åtkomst till dokument samlingen för ett index)

*Administratörs nycklar* skapas när tjänsten är etablerad. Det finns två administratörs nycklar som anges som *primär* och *sekundär* för att hålla dem direkt, men i själva verket är de utbytbara. Varje tjänst har två administratörs nycklar så att du kan återställa en över utan att förlora åtkomsten till tjänsten. Du kan [Återskapa administratörs nyckeln](search-security-api-keys.md#regenerate-admin-keys) regelbundet enligt rekommendationer för Azure-säkerhet, men du kan inte lägga till det totala antalet admin-nycklar. Det finns högst två administrations nycklar per Sök tjänst.

*Frågeinställningar* skapas som de behövs och är utformade för klient program som utfärdar frågor. Du kan skapa upp till 50 frågeinställningar. I program kod anger du Sök-URL och en fråge-API-nyckel för att tillåta skrivskyddad åtkomst till dokument samlingen för ett särskilt index. Tillsammans är slut punkten, en API-nyckel för skrivskyddad åtkomst och ett mål index som definierar omfattning och åtkomst nivå för anslutningen från klient programmet.

Autentisering krävs för varje begäran, där varje begäran består av en obligatorisk nyckel, en åtgärd och ett objekt. Vid samman koppling är de två behörighets nivåerna (fullständig eller skrivskyddad) plus kontexten (till exempel en fråga-åtgärd för ett index) tillräckliga för att tillhandahålla fullständig säkerhet för tjänst åtgärder. Mer information om nycklar finns i [skapa och hantera API-nycklar](search-security-api-keys.md).

### <a name="ip-restricted-access"></a>IP-begränsad åtkomst

Om du vill kontrol lera åtkomsten till din Sök tjänst ytterligare kan du skapa ingående brand Väggs regler som tillåter åtkomst till en speciell IP-adress eller ett intervall med IP-adresser. Alla klient anslutningar måste göras via en tillåten IP-adress, annars nekas anslutningen.

Du kan använda portalen för att [Konfigurera inkommande åtkomst](service-configure-firewall.md). 

Du kan också använda REST-API: er för hantering. Med hjälp av [IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule) -parametern i API version 2020-03-13 kan du begränsa åtkomsten till din tjänst genom att identifiera IP-adresser, individuellt eller i ett intervall, som du vill bevilja åtkomst till din Sök tjänst. 

### <a name="private-endpoint-no-internet-traffic"></a>Privat slut punkt (ingen Internet trafik)

En [privat slut punkt](../private-link/private-endpoint-overview.md) för Azure kognitiv sökning tillåter en klient i ett [virtuellt nätverk](../virtual-network/virtual-networks-overview.md) att säkert komma åt data i ett sökindex över en [privat länk](../private-link/private-link-overview.md). 

Den privata slut punkten använder en IP-adress från det virtuella nätverkets adress utrymme för anslutningar till din Sök tjänst. Nätverks trafiken mellan klienten och Sök tjänsten passerar över det virtuella nätverket och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet. Ett VNET möjliggör säker kommunikation mellan resurser, med ditt lokala nätverk och Internet. 

Även om den här lösningen är den säkraste, är användningen av ytterligare tjänster en extra kostnad, så se till att du har en tydlig förståelse av fördelarna innan du simhopp i. eller mer information om kostnader finns på [sidan med priser](https://azure.microsoft.com/pricing/details/private-link/). Mer information om hur dessa komponenter fungerar tillsammans finns på videon överst i den här artikeln. Täckning av privat slut punkts alternativ börjar på 5:48 i videon. Instruktioner för hur du konfigurerar slut punkten finns i [skapa en privat slut punkt för Azure kognitiv sökning](service-create-private-endpoint.md).

## <a name="index-access"></a>Index åtkomst

I Azure Kognitiv sökning är ett enskilt index inte ett skydds Bart objekt. I stället fastställs åtkomst till ett index på tjänst lagret (Läs-eller skriv åtkomst till tjänsten), tillsammans med kontexten för en åtgärd.

För slut användar åtkomst kan du strukturera förfrågningar om att ansluta med hjälp av en frågegrupp, som gör begäran skrivskyddad och inkludera det särskilda index som används av din app. I en förfrågan finns det ingen idé att koppla index eller att komma åt flera index samtidigt, så att alla begär Anden är riktade mot ett enda index med definition. Därför definierar själva begäran (en nyckel plus ett enskilt mål index) säkerhets gränserna.

Administratörs-och utvecklarens åtkomst till index är inte differentierad: båda behöver Skriv behörighet för att skapa, ta bort och uppdatera objekt som hanteras av tjänsten. Alla med en administratörs nyckel till din tjänst kan läsa, ändra eller ta bort alla index i samma tjänst. För att skydda mot oavsiktlig eller skadlig borttagning av index, är din interna käll kontroll för kod till gångar en påföljd för att återföra en oönskad index borttagning eller ändring. Azure Kognitiv sökning har redundans i klustret för att säkerställa tillgängligheten, men den lagrar eller kör inte din egna kod som används för att skapa eller läsa in index.

För lösningar med flera innehavare som kräver säkerhets gränser på index nivån omfattar sådana lösningar vanligt vis en mellan nivå, som kunder använder för att hantera index isolering. Mer information om användnings fallet för flera innehavare finns i [design mönster för SaaS-program för flera innehavare och Azure kognitiv sökning](search-modeling-multitenant-saas-applications.md).

## <a name="user-access"></a>Användaråtkomst

Hur en användare kommer åt ett index och andra objekt bestäms av typen av API-nyckel i begäran. De flesta utvecklare skapar och tilldelar [*frågeinställningar*](search-security-api-keys.md) för Sök begär Anden på klient sidan. En frågegrupp beviljar skrivskyddad åtkomst till sökbart innehåll i indexet.

Om du behöver detaljerad kontroll över varje användare över Sök resultat kan du bygga säkerhets filter på dina frågor och returnera dokument som är associerade med en viss säkerhets identitet. I stället för fördefinierade roller och roll tilldelningar implementeras identitetsbaserade åtkomst kontroller som ett *filter* som trimmar Sök Resultat för dokument och innehåll baserat på identiteter. I följande tabell beskrivs två metoder för att trimma Sök Resultat av obehörigt innehåll.

| Metod | Beskrivning |
|----------|-------------|
|[Säkerhets trimning baserat på identitets filter](search-security-trimming-for-azure-search.md)  | Dokumenterar det grundläggande arbets flödet för att implementera åtkomst kontroll för användar identitet. Det omfattar att lägga till säkerhets identifierare i ett index och sedan förklarar filtreringen för fältet för att trimma resultat från otillåtet innehåll. |
|[Säkerhets trimning baserat på Azure Active Directory identiteter](search-security-trimming-for-azure-search-with-aad.md)  | Den här artikeln är utökad i föregående artikel, som innehåller steg för att hämta identiteter från Azure Active Directory (AAD), en av de [kostnads fria tjänsterna](https://azure.microsoft.com/free/) i Azure Cloud Platform. |

## <a name="administrative-rights"></a>Administrativa rättigheter

[Rollbaserad åtkomst (RBAC)](../role-based-access-control/overview.md) är ett auktoriserings system som bygger på [Azure Resource Manager](../azure-resource-manager/management/overview.md) för etablering av Azure-resurser. I Azure Kognitiv sökning används Resource Manager för att skapa eller ta bort tjänsten, hantera API-nycklar och skala tjänsten. Det innebär att RBAC-roll tilldelningar avgör vilka som kan utföra dessa uppgifter, oavsett om de använder [portalen](search-manage.md), [POWERSHELL](search-manage-powershell.md)eller [hantering REST-API: er](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

Administratörs rättigheter över innehåll som är värd för tjänsten, till exempel möjligheten att skapa eller ta bort ett index, härleds genom API-nycklar enligt beskrivningen i [föregående avsnitt](#index-access).

> [!TIP]
> Med hjälp av Azure-omfattande mekanismer kan du låsa en prenumeration eller resurs för att förhindra oavsiktlig eller obehörig borttagning av Sök tjänsten av användare med administratörs behörighet. Mer information finns i [låsa resurser för att förhindra oväntad borttagning](../azure-resource-manager/management/lock-resources.md).

## <a name="certifications-and-compliance"></a>Certifieringar och efterlevnad

Azure Kognitiv sökning har certifierats vara kompatibel för flera globala, regionala och branschspecifika standarder för både det offentliga molnet och Azure Government. För den fullständiga listan hämtar du [fakta bladet om **Microsoft Azure regelefterlevnad** ](https://aka.ms/azurecompliance) från sidan officiella gransknings rapporter.

## <a name="see-also"></a>Se även

+ [Grunderna i Azure-säkerhet](../security/fundamentals/index.yml)
+ [Azure-säkerhet](https://azure.microsoft.com/overview/security)
+ [Azure Security Center](../security-center/index.yml)