---
title: Introduktion till Azure Storage – Molnlagring i Azure | Microsoft Docs
description: Azure Storage är Microsofts molntjänstlagringslösning. Azure Storage tillhandahåller lagring för dataobjekt som har hög tillgänglighet och är säkra, robusta, mycket skalbara och redundanta.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: b3f2b499d34d4982e607549dc1bb588764a001fb
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770433"
---
# <a name="introduction-to-azure-storage"></a>Introduktion till Azure Storage

Azure Storage är Microsofts molntjänstlagringslösning för moderna datalagringsscenarier. Azure Storage tillhandahåller mycket skalbar objektlagring för dataobjekt, en filsystemtjänst för molnet, meddelandelagring för tillförlitliga meddelandefunktioner och ett NoSQL-lager. Azure Storage är:

- **Pålitlig och mycket tillgänglig.** Redundans garanterar att dina data är säkra i händelse av tillfälliga maskinvarufel. Du kan också välja att replikera data i datacenter eller geografiska regioner för ytterligare skydd mot lokala allvarliga händelser eller naturkatastrofer. Data som replikeras på det här sättet förblir mycket tillgängliga vid ett oväntat avbrott. 
- **Skydda.** Alla data som skrivs till Azure Storage krypteras av tjänsten. Med Azure Storage får du detaljerad kontroll över vem som har tillgång till dina data.
- **Skalbar.** Azure Storage är utformat för att vara mycket skalbart för att uppfylla krav på datalagring och prestanda för dagens program. 
- **Hanterad.** Microsoft Azure hanterar maskinvaruunderhåll, uppdateringar och kritiska problem åt dig.
- **Åtkomlig.** Data i Azure Storage är åtkomlig från hela världen via HTTP eller HTTPS varifrån som helst. Microsoft tillhandahåller SDK:er för Azure Storage på många olika språk – .NET, Java, Node.js, Python, PHP, Ruby, Go med flera – samt en mogen REST API. Azure Storage stöder skriptning i Azure PowerShell eller Azure CLI. Azure-portalen och Azure Storage Explorer erbjuder även enkla visuella lösningar för att arbeta med dina data.  

## <a name="azure-storage-services"></a>Azure Storage-tjänster

I Azure Storage finns dessa datatjänster: 

- [Azure Blobs](../blobs/storage-blobs-introduction.md): En mycket skalbar objektlagring för textdata och binära data.
- [Azure Files](../files/storage-files-introduction.md): Hanterade filresurser för distributioner i molnet eller lokalt.
- [Azure Queues](../queues/storage-queues-introduction.md): Ett meddelandearkiv för tillförlitliga meddelandefunktioner mellan programkomponenter. 
- [Azure Tables](../tables/table-storage-overview.md): En NoSQL-lagring av schemalös lagring av strukturerade data.

Varje tjänst nås via ett lagringskonto. För att komma igång läser du [Skapa ett lagringskonto](storage-quickstart-create-account.md).

## <a name="blob-storage"></a>Blob Storage

Azure Blob Storage är Microsofts objektlagringslösning för molnet. Blob Storage är optimerat för att lagra stora mängder ostrukturerade data, exempelvis text eller binära data. 

Blob Storage är perfekt för:

* Leverera bilder eller dokument direkt till en webbläsare.
* Lagra filer för distribuerad åtkomst.
* Direktuppspelning av video och ljud.
* Lagra data för säkerhetskopiering och återställning, haveriberedskap och arkivering.
* Lagra data för analys av en tjänst som kan vara lokal eller Azure-baserad.

Objekt i Blob storage kan nås från vilken plats som helst i världen via HTTP eller HTTPS. Användare eller klientprogram kan få åtkomst till blobar via URL-adresser, [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage) eller ett Azure Storage-klientbibliotek. Det finns lagringsklientbibliotek för flera språk, bland annat [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](http://azure.github.io/azure-storage-php/) och [Ruby](http://azure.github.io/azure-storage-ruby).

Mer information om Blob Storage finns i [Introduktion till bloblagring](../blobs/storage-blobs-introduction.md).

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

Azure Table Storage är nu en del av Azure Cosmos DB. Du hittar dokumentationen till Azure Table Storage i [Översikt över Azure Table Storage](../tables/table-storage-overview.md). Utöver den befintliga Azure Table Storage-tjänsten finns ett nytt tabell-API för Azure Cosmos DB som tillhandahåller genomströmningsoptimerade tabeller, global distribution och automatiska sekundärindex. Om du vill veta mer om och prova den nya premiumtjänsten går du till avsnittet [Tabell-API för Azure Cosmos DB](https://aka.ms/premiumtables).

Mer information om Table Storage finns i [översikten över Azure Table Storage](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Disklagring

Azure Storage omfattar även hanterade och ohanterade diskfunktioner som används av virtuella datorer. Mer information om dessa funktioner finns i [Compute Services-dokumentationen](https://docs.microsoft.com/azure/#pivot=products&panel=Compute).

## <a name="types-of-storage-accounts"></a>Typer av lagringskonton

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

Mer information om typer av lagringskonton finns i [Översikt över Azure Storage-konton](storage-account-overview.md). 

## <a name="accessing-your-blobs-files-and-queues"></a>Åtkomst till dina blobbar, filer och köer

Varje lagringskonto har två autentiseringsnycklar, och båda kan användas för alla åtgärder. Det finns två nycklar så att du kan växla nycklar ibland för att förbättra säkerheten. Det är mycket viktigt att dessa nycklar skyddas, eftersom tillgång till dem tillsammans med kontonamnet ger obegränsad tillgång till alla data i lagringskontot.

Det här avsnittet tar upp två sätt att skydda lagringskontot och dess data. Detaljerad information om hur du skyddar ditt lagringskonto och dina data finns i [säkerhetsguiden för Azure Storage](storage-security-guide.md).

### <a name="securing-access-to-storage-accounts-using-azure-ad"></a>Skydda åtkomst till lagringskonton med hjälp av Azure AD

Ett sätt att skydda åtkomst till dina lagringsdata är genom att kontrollera åtkomst till lagringskontonycklarna. Med rollbaserad åtkomstkontroll (RBAC) i Resource Manager kan du tilldela roller till användare, grupper eller program. Rollerna är knutna till en specifik uppsättning åtgärder som är tillåtna eller otillåtna. Om du använder RBAC för att bevilja åtkomst till ett lagringskonto hanteras endast hanteringsåtgärderna för det lagringskontot, till exempel ändring av åtkomstnivå. Du kan inte använda RBAC för att bevilja åtkomst till dataobjekt, till exempel en specifik container eller filresurs. Du kan dock använda RBAC för att bevilja åtkomst till lagringskontonycklarna, som sedan kan användas för att läsa dataobjekten.

### <a name="securing-access-using-shared-access-signatures"></a>Skydda åtkomst med hjälp av signaturer för delad åtkomst

Du kan använda signaturer för delad åtkomst och lagrade åtkomstprinciper för att skydda dina dataobjekt. En signatur för delad åtkomst (SAS) är en sträng som innehåller en säkerhetstoken som kan kopplas till URI:n för en tillgång som gör att du kan delegera åtkomst till specifika lagringsobjekt och ange begränsningar, till exempel behörigheter och datum/tid-intervallet för åtkomst. Den här funktionen har omfattande möjligheter. Detaljerad information finns i [Använda signaturer för delad åtkomst (SAS)](storage-dotnet-shared-access-signature-part-1.md).

### <a name="public-access-to-blobs"></a>Offentlig åtkomst till blobbar

Med Blob Service kan du tillhandahålla offentlig åtkomst till en container och dess blobar, eller en specifik blob. När du anger att en container eller blob är offentlig kan alla läsa den anonymt; ingen autentisering krävs. Ett exempel på när du kan göra det är när du har en webbplats med bilder, video eller dokument från Blob Storage. Mer information finns i [Hantera anonym läsbehörighet till containrar och blobar](../blobs/storage-manage-access-to-resources.md).

## <a name="encryption"></a>Kryptering

Det finns några grundläggande typer av kryptering för Storage-tjänsterna. Mer information om säkerhet och kryptering finns i [Säkerhetsguiden för Azure Storage](storage-security-guide.md).

### <a name="encryption-at-rest"></a>Vilande kryptering

Vilande Azure Storage Service-kryptering (SSE) stöder kryptering av vilande data och skyddar dina data så att din verksamhet uppfyller både interna och externa krav och åtaganden. Med den här funktionen krypterar Azure Storage automatiskt dina data för beständig lagring och dekrypterar dem före hämtning. Användarna har fullständig insyn i kryptering, dekryptering och nyckelhantering.


SSE krypterar automatiskt data på alla prestandanivåer (Standard och Premium), alla distributionsmodeller (Azure Resource Manager och klassisk) och alla Azure Storage-tjänster (blob, kö, tabell och fil). SSE påverkar inte Azure Storage-prestanda.

Läs mer om vilande SSE-kryptering på [Azure Storage Service-kryptering för vilande data](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Kryptering av klientsidan

Lagringsklientbiblioteken har metoder som du kan anropa för att programmässigt kryptera data innan de skickas via kabeln från klienten till Azure. De lagras krypterade, vilket innebär att de också är krypterade i vila. När du läser data dekrypteras informationen när du har fått den.

Mer information om kryptering på klientsidan finns i [Kryptering på klientsidan med .NET för Microsoft Azure Storage](storage-client-side-encryption.md).

## <a name="redundancy"></a>Redundans

För att säkerställa att dina data är beständiga kan Azure Storage replikera flera kopior av dina data. När du konfigurerar ditt lagringskonto väljer du ett redundansalternativ. 

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Mer information om haveriberedskap finns i avsnittet om [haveriberedskap och redundans för lagringskonto (förhandsversion) i Azure Storage](storage-disaster-recovery-guidance.md).

## <a name="transferring-data-to-and-from-azure-storage"></a>Överföra data till och från Azure Storage

Du kan flytta data till eller från Azure Storage på flera olika sätt. Vilket alternativ du väljer beror på storleken på datauppsättningen och din nätverksbandbredd. Mer information finns i [Välja en Azure-lösning för dataöverföring](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Prissättning

Detaljerad information om priser för Azure Storage finns på [prissidan](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>API:er, bibliotek och verktyg för Azure Storage
Azure Storage-resurser kan nås med alla språk som kan skicka HTTP/HTTPS-förfrågningar. Dessutom erbjuder Azure Storage programmeringsbibliotek för flera populära språk. Dessa bibliotek förenklar många aspekter av arbetet med Azure Storage genom att hantera information som till exempel synkrona och asynkrona anrop, massbearbetning av åtgärder, undantagshantering, automatiska omförsök, funktionsbeteenden och så vidare. Bibliotek är för närvarande tillgängliga för följande språk och plattformar, med andra i pipeline:

### <a name="azure-storage-data-api-and-library-references"></a>Referenser till Azure Storage-data-API och -bibliotek
* [REST-API för Storage Services](https://docs.microsoft.com/rest/api/storageservices/)
* [Storage-klientbibliotek för .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
* [Storage-klientbibliotek för Java/Android](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Storage-klientbibliotek för Node.js](https://docs.microsoft.com/javascript/api/azure-storage)
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
* [Azure PowerShell-cmdletar för Storage](https://docs.microsoft.com/powershell/module/az.storage)
* [Azure CLI-cmdletar för Storage](https://docs.microsoft.com/cli/azure/storage)
* [Kommandoradsverktyget AzCopy](https://aka.ms/downloadazcopy)
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
* [Azure Storage-klientverktyg](../storage-explorers.md)
* [Azure-utvecklarverktyg](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Nästa steg

Kom igång med Azure Storage genom att läsa [Skapa ett lagringskonto](storage-quickstart-create-account.md).
