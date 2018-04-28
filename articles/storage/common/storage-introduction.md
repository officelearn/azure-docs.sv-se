---
title: Introduktion till Azure Storage – Molnlagring i Azure | Microsoft Docs
description: Azure Storage är Microsofts molntjänstlagringslösning. Azure Storage tillhandahåller lagring för dataobjekt som har hög tillgänglighet och är säkra, robusta, mycket skalbara och redundanta.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: get-started-article
ms.date: 04/05/2018
ms.author: tamram
ms.openlocfilehash: 071b209ffa8ffeb8ef6d998f08bcd68868e29911
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="introduction-to-azure-storage"></a>Introduktion till Azure Storage

Azure Storage är Microsofts molntjänstlagringslösning för moderna datalagringsscenarier. Azure Storage tillhandahåller mycket skalbar objektlagring för dataobjekt, en filsystemtjänst för molnet, meddelandelagring för tillförlitliga meddelandefunktioner och ett NoSQL-lager. Azure Storage är:

- **Pålitlig och mycket tillgänglig.** Redundans garanterar att dina data är säkra i händelse av tillfälliga maskinvarufel. Du kan också välja att replikera data i datacenter eller geografiska regioner för ytterligare skydd mot lokala allvarliga händelser eller naturkatastrofer. Data som replikeras på det här sättet förblir mycket tillgängliga vid ett oväntat avbrott. 
- **Skydda.** Alla data som skrivs till Azure Storage krypteras av tjänsten. Med Azure Storage får du detaljerad kontroll över vem som har tillgång till dina data.
- **Skalbar.** Azure Storage är utformat för att vara mycket skalbart för att uppfylla krav på datalagring och prestanda för dagens program. 
- **Hanterad.** Microsoft Azure tar hand om underhåll och hanterar kritiska problem åt dig.
- **Åtkomlig.** Data i Azure Storage är åtkomlig från hela världen via HTTP eller HTTPS varifrån som helst. Microsoft tillhandahåller SDK:er för Azure Storage på många olika språk – .NET, Java, Node.js, Python, PHP, Ruby, Go med flera – samt en mogen REST API. Azure Storage stöder skrivning i Azure PowerShell eller Azure CLI. Azure-portalen och Azure Storage Explorer erbjuder även enkla visuella lösningar för att arbeta med dina data.  

## <a name="azure-storage-services"></a>Azure Storage-tjänster

I Azure Storage finns dessa datatjänster: 

- [Azure Blobs](../blobs/storage-blobs-introduction.md): En mycket skalbar objektlagring för text och binär dag.
- [Azure Files](../files/storage-files-introduction.md): Hanterade filresurser för distributioner i molnet eller lokalt.
- [Azure Queues](../queues/storage-queues-introduction.md): Ett meddelandearkiv för tillförlitliga meddelandefunktioner mellan programkomponenter. 
- [Azure Tables](../../cosmos-db/table-storage-overview.md): En NoSQL-lagring av schemalös lagring av strukturerade data.

Varje tjänst nås via ett lagringskonto. För att komma igång läser du [Skapa ett lagringskonto](storage-quickstart-create-account.md).

## <a name="blob-storage"></a>Blob Storage

Azure Blob Storage är Microsofts objektlagringslösning för molnet. Blob Storage är optimerat för att lagra stora mängder ostrukturerade data, exempelvis text eller binära data. 

Blob Storage är perfekt för:

* Leverera bilder eller dokument direkt till en webbläsare.
* Lagra filer för distribuerad åtkomst.
* Direktuppspelning av video och ljud.
* Lagra data för säkerhetskopiering och återställning, haveriberedskap och arkivering.
* Lagra data för analys av en tjänst som kan vara lokal eller Azure-baserad.

Objekt i Blob storage kan nås från vilken plats som helst i världen via HTTP eller HTTPS. Användare eller klientprogram kan få åtkomst till blobar via URL-adresser, [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage) eller ett Azure Storage-klientbibliotek. Det finns lagringsklientbibliotek för flera språk, bland annat [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/en/latest/index.html), [PHP](http://azure.github.io/azure-storage-php/) och [Ruby](http://azure.github.io/azure-storage-ruby).

Mer information om Blob Storage finns i [introduktionen till objektlagring på Azure](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Azure Files
Med [Azure Files](../files/storage-files-introduction.md) kan du konfigurera nätverksfilresurser med hög tillgänglighet som kan nås via SMB-standardprotokollet (Server Message Block). Det innebär att flera virtuella datorer kan dela samma filer med både läs- och skrivbehörighet. Du kan också läsa filerna med hjälp av REST-gränssnittet eller klientbiblioteken för lagring.

En sak som skiljer Azure Files från filer på en företagsfilresurs är att du kan komma åt filerna var som helst i världen med en URL som pekar på filen och som innehåller en SAS-token (signatur för delad åtkomst). Du kan generera SAS-token, som ger specifik åtkomst till en privat resurs under en viss tidsperiod.

Filresurser kan användas för många vanliga scenarier:

* Många lokala program använder filresurser. Den här funktionen gör det enklare att migrera program som delar data till Azure. Om du monterar filresursen med samma enhetsbeteckning som det lokala programmet använder, bör den del av programmet som använder filresursen fungera med minimala, om några, ändringar.

* Konfigurationsfiler kan lagras på en filresurs och nås från flera virtuella datorer. Verktyg och hjälpmedel som används av flera utvecklare i ett team kan lagras på en filresurs, så att alla enkelt kan hitta dem och för att säkerställa att alla använder samma version.

* Diagnostikloggar, mätvärden och kraschdumpar är bara tre exempel på data som kan skrivas till en filresurs och bearbetas eller analyseras senare.

För närvarande stöds inte Active Directory-baserad autentisering och åtkomstkontrollistor (ACL:er). Detta kommer dock att implementeras i en framtida utgåva. Autentiseringsuppgifterna för lagringskontot används för att tillhandahålla autentisering för åtkomst till filresursen. Det betyder att alla som har resursen monterad har fullständig skriv- och läsåtkomst till resursen.

Mer information om Azure Files finns i [Introduktion till Azure Files](../files/storage-files-introduction.md).

## <a name="queue-storage"></a>Queue Storage

Azure-kötjänsten används för att lagra och hämta meddelanden. Kömeddelanden kan vara upp till 64 kB och en kö kan innehålla miljontals meddelanden. Köer används vanligtvis för att lagra listor med meddelanden som ska bearbetas asynkront.

Anta exempelvis att du vill att kunderna ska kunna ladda upp bilder och du vill skapa miniatyrer för varje bild. Du kan låta kunden vänta på att du skapar miniatyrerna medan bilderna laddas upp. Ett alternativ är att använda en kö. När kunden är klar med uppladdningen skrivs ett meddelande till kön. Sedan hämtar en Azure-funktion meddelandet från kön och skapar miniatyrerna. Alla delar av bearbetningen kan skalas separat, vilket ger dig större kontroll när du anpassar den för din användning.

Mer information om Azure Queues finns i [introduktionen till Queues](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Table Storage

Azure Table Storage är nu en del av Azure Cosmos DB. Du hittar dokumentationen till Azure Table Storage i [Översikt över Azure Table Storage](../../cosmos-db/table-storage-overview.md). Utöver den befintliga Azure Table Storage-tjänsten finns ett nytt tabell-API för Azure Cosmos DB som tillhandahåller genomströmningsoptimerade tabeller, global distribution och automatiska sekundärindex. Om du vill veta mer om och prova den nya premiumtjänsten går du till avsnittet [Tabell-API för Azure Cosmos DB](https://aka.ms/premiumtables).

Mer information om Table Storage finns i [översikten över Azure Table Storage](../../cosmos-db/table-storage-overview.md).

## <a name="disk-storage"></a>Disklagring

Azure Storage omfattar även hanterade och ohanterade diskfunktioner som används av virtuella datorer. Mer information om dessa funktioner finns i [Compute Services-dokumentationen](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).

## <a name="types-of-storage-accounts"></a>Typer av lagringskonton

I den här tabellen visas de olika typerna av lagringskonton och vilka objekt som kan användas med var och en.

|**Typ av lagringskonto**|**Allmän Standard**|**Allmän Premium**|**Blob Storage, frekvent och lågfrekvent åtkomstnivå**|
|-----|-----|-----|-----|
|**Tjänster som stöds**| Blob-, fil- och kötjänster | Blob-tjänst | Blob-tjänst|
|**Typer av blobbar som stöds**|Blockblobbar, sidblobbar och tilläggsblobbar | Sidblobbar | Blockblobbar och tilläggsblobbar|

### <a name="general-purpose-storage-accounts"></a>Allmänna lagringskonton

Det finns två typer av allmänna lagringskonton.

#### <a name="standard-storage"></a>Standard Storage

De vanligaste lagringskontona är standardlagringskonton, som kan användas för alla typer av data. För standardlagringskonton används magnetiska media för att lagra data.

#### <a name="premium-storage"></a>Premium Storage

Premium Storage ger högpresterande lagring för sidblobbar, som främst används för VHD-filer. För Premium Storage-konton används SSD för att lagra data. Microsoft rekommenderar att du använder Premium Storage för alla dina virtuella datorer.

### <a name="blob-storage-accounts"></a>Blob Storage-konton

Blob Storage-kontot är ett specialanpassat lagringskonto som används för att lagra blockblobbar och tilläggsblobbar. Du kan inte lagra sidblobbar i dessa konton, och därför kan du inte lagra VHD-filer. Med dessa konton kan du ange en frekvent eller lågfrekvent åtkomstnivå – nivån kan ändras när som helst.

Frekvent åtkomstnivå används för filer som används ofta – du betalar en högre kostnad för lagring, men kostnaden för åtkomst till blobbarna är mycket lägre. För blobbar som lagras i lågfrekvent åtkomstnivå betalar du en högre kostnad för åtkomst till blobbarna, men kostnaden för lagring är mycket lägre.

## <a name="accessing-your-blobs-files-and-queues"></a>Åtkomst till dina blobbar, filer och köer

Varje lagringskonto har två autentiseringsnycklar, och båda kan användas för alla åtgärder. Det finns två nycklar så att du kan växla nycklar ibland för att förbättra säkerheten. Det är mycket viktigt att dessa nycklar skyddas, eftersom tillgång till dem tillsammans med kontonamnet ger obegränsad tillgång till alla data i lagringskontot.

Det här avsnittet tar upp två sätt att skydda lagringskontot och dess data. Detaljerad information om hur du skyddar ditt lagringskonto och dina data finns i [säkerhetsguiden för Azure Storage](storage-security-guide.md).

### <a name="securing-access-to-storage-accounts-using-azure-ad"></a>Skydda åtkomst till lagringskonton med hjälp av Azure AD

Ett sätt att skydda åtkomst till dina lagringsdata är genom att kontrollera åtkomst till lagringskontonycklarna. Med rollbaserad åtkomstkontroll (RBAC) i Resource Manager kan du tilldela roller till användare, grupper eller program. Rollerna är knutna till en specifik uppsättning åtgärder som är tillåtna eller otillåtna. Om du använder RBAC för att bevilja åtkomst till ett lagringskonto hanteras endast hanteringsåtgärderna för det lagringskontot, till exempel ändring av åtkomstnivå. Du kan inte använda RBAC för att bevilja åtkomst till dataobjekt, till exempel en specifik behållare eller filresurs. Du kan dock använda RBAC för att bevilja åtkomst till lagringskontonycklarna, som sedan kan användas för att läsa dataobjekten.

### <a name="securing-access-using-shared-access-signatures"></a>Skydda åtkomst med hjälp av signaturer för delad åtkomst

Du kan använda signaturer för delad åtkomst och lagrade åtkomstprinciper för att skydda dina dataobjekt. En signatur för delad åtkomst (SAS) är en sträng som innehåller en säkerhetstoken som kan kopplas till URI:n för en tillgång som gör att du kan delegera åtkomst till specifika lagringsobjekt och ange begränsningar, till exempel behörigheter och datum/tid-intervallet för åtkomst. Den här funktionen har omfattande möjligheter. Detaljerad information finns i [Använda signaturer för delad åtkomst (SAS)](storage-dotnet-shared-access-signature-part-1.md).

### <a name="public-access-to-blobs"></a>Offentlig åtkomst till blobbar

Med Blob Service kan du tillhandahålla offentlig åtkomst till en behållare och dess blobbar, eller en specifik blob. När du anger att en behållare eller blobb är offentlig kan alla läsa den anonymt; ingen autentisering krävs. Ett exempel på när du kan göra det är när du har en webbplats med bilder, video eller dokument från Blob Storage. Mer information finns i [Hantera anonym läsbehörighet till behållare och blobbar](../blobs/storage-manage-access-to-resources.md).

## <a name="encryption"></a>Kryptering

Det finns några grundläggande typer av kryptering för Storage-tjänsterna. Mer information om säkerhet och kryptering finns i [Säkerhetsguiden för Azure Storage](storage-security-guide.md).

### <a name="encryption-at-rest"></a>Vilande kryptering

Vilande Azure Storage Service-kryptering (SSE) stöder kryptering av vilande data och skyddar dina data så att din verksamhet uppfyller både interna och externa krav och åtaganden. Med den här funktionen krypterar Azure Storage automatiskt dina data för beständig lagring och dekrypterar dem före hämtning. Användarna har fullständig insyn i kryptering, dekryptering och nyckelhantering.

SSE krypterar automatiskt data på alla prestandanivåer (Standard och Premium), alla distributionsmodeller (Azure Resource Manager och klassisk) och alla Azure Storage-tjänster (blob, kö, tabell och fil). SSE påverkar inte Azure Storage-prestanda.

Läs mer om vilande SSE-kryptering på [Azure Storage Service-kryptering för vilande data](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Kryptering av klientsidan

Lagringsklientbiblioteken har metoder som du kan anropa för att programmässigt kryptera data innan de skickas via kabeln från klienten till Azure. De lagras krypterade, vilket innebär att de också är krypterade i vila. När du läser data dekrypteras informationen när du har fått den.

Mer information om kryptering på klientsidan finns i [Kryptering på klientsidan med .NET för Microsoft Azure Storage](storage-client-side-encryption.md).

## <a name="replication"></a>Replikering

För att säkerställa att dina data är beständiga kan Azure Storage replikera flera kopior av dina data. När du konfigurerar ditt lagringskonto väljer du en replikeringstyp. I de flesta fall kan den här inställningen ändras efter att lagringskontot har skapats. 

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Information om haveriberedskap finns i [Vad du gör om ett avbrott i Azure Storage inträffar?](storage-disaster-recovery-guidance.md).

## <a name="transferring-data-to-and-from-azure-storage"></a>Överföra data till och från Azure Storage

Du kan använda kommandoradsverktyget AzCopy för att kopiera blob- och fildata inom ditt lagringskonto eller mellan lagringskonton. Läs någon av följande artiklar om du behöver hjälp:

* [Överföra data med AzCopy för Windows](storage-use-azcopy.md)
* [Överföra data med AzCopy för Linux](storage-use-azcopy-linux.md)

AzCopy är byggt ovanpå [biblioteket för Azure-dataflyttningar](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), som för närvarande är tillgängligt som en förhandsversion.

Azure Import/Export-tjänsten kan användas för att importera eller exportera stora mängder blob-data till eller från ditt lagringskonto. Du förbereder och skickar flera hårddiskar per post till ett Azure-datacenter, där de överför data till/från hårddiskarna och skickar tillbaka hårddiskarna till dig. Mer information om tjänsten Import/Export finns i [Använda tjänsten Microsoft Azure Import/Export för att överföra data till Blob Storage](../storage-import-export-service.md).

## <a name="pricing"></a>Prissättning

Detaljerad information om priser för Azure Storage finns på [prissidan](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>API:er, bibliotek och verktyg för Azure Storage
Azure Storage-resurser kan nås med alla språk som kan skicka HTTP/HTTPS-förfrågningar. Dessutom erbjuder Azure Storage programmeringsbibliotek för flera populära språk. Dessa bibliotek förenklar många aspekter av arbetet med Azure Storage genom att hantera information som till exempel synkrona och asynkrona anrop, massbearbetning av åtgärder, undantagshantering, automatiska omförsök, funktionsbeteenden och så vidare. Bibliotek är för närvarande tillgängliga för följande språk och plattformar, med andra i pipeline:

### <a name="azure-storage-data-api-and-library-references"></a>Referenser till Azure Storage-data-API och -bibliotek
* [REST-API för Storage Services](https://docs.microsoft.com/rest/api/storageservices/)
* [Storage-klientbibliotek för .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
* [Storage-klientbibliotek för Java/Android](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Storage-klientbibliotek för Node.js](https://docs.microsoft.com/en-us/javascript/api/azure-storage)
* [Storage-klientbibliotek för Python](https://github.com/Azure/azure-storage-python)
* [Storage-klientbibliotek för PHP](https://github.com/Azure/azure-storage-php)
* [Storage-klientbibliotek för Ruby](https://github.com/Azure/azure-storage-ruby)
* [Storage-klientbibliotek för C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Referenser till Azure Storage-hanterings-API och -bibliotek
* [REST-API för Storage Resource Provider](https://docs.microsoft.com/rest/api/storagerp/)
* [Klientbibliotek för Storage Resource Provider för .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
* [REST-API för Service Management för Storage (klassisk)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Referenser till Azure Storage-dataflyttnings-API och -bibliotek
* [REST-API för Storage Import/Export Service](https://docs.microsoft.com/rest/api/storageimportexport/)
* [Klientbibliotek för Storage Data Movement för .NET](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.datamovement)

### <a name="tools-and-utilities"></a>Verktyg och hjälpmedel
* [Azure PowerShell-cmdletar för Storage](https://docs.microsoft.com/powershell/module/azure.storage)
* [Azure CLI-cmdletar för Storage](https://docs.microsoft.com/cli/azure/storage)
* [Kommandoradsverktyget AzCopy](http://aka.ms/downloadazcopy)
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
* [Azure Storage-klientverktyg](../storage-explorers.md)
* [Azure-utvecklarverktyg](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Nästa steg

Kom igång med Azure Storage genom att läsa [Skapa ett lagringskonto](storage-quickstart-create-account.md).
