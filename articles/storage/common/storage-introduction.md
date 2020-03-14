---
title: Introduktion till Azure Storage – Molnlagring i Azure | Microsoft Docs
description: Azure Storage är Microsofts molntjänstlagringslösning. Azure Storage tillhandahåller lagring för dataobjekt som har hög tillgänglighet och är säkra, robusta, mycket skalbara och redundanta.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5bab70b6b023a4e6510e32368d407a38388cde2f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256073"
---
# <a name="introduction-to-azure-storage"></a>Introduktion till Azure Storage

Azure Storage är Microsofts molntjänstlagringslösning för moderna datalagringsscenarier. Azure Storage tillhandahåller mycket skalbar objektlagring för dataobjekt, en filsystemtjänst för molnet, meddelandelagring för tillförlitliga meddelandefunktioner och ett NoSQL-lager. Azure Storage är:

- **Pålitlig och mycket tillgänglig.** Redundans garanterar att dina data är säkra i händelse av tillfälliga maskinvarufel. Du kan också välja att replikera data i datacenter eller geografiska regioner för ytterligare skydd mot lokala allvarliga händelser eller naturkatastrofer. Data som replikeras på det här sättet förblir mycket tillgängliga vid ett oväntat avbrott.
- **Skydda.** Alla data som skrivs till Azure Storage krypteras av tjänsten. Med Azure Storage får du detaljerad kontroll över vem som har tillgång till dina data.
- **Skalbar.** Azure Storage är utformat för att vara mycket skalbart för att uppfylla krav på datalagring och prestanda för dagens program.
- **Hanterad.** Microsoft Azure hanterar maskinvaruunderhåll, uppdateringar och kritiska problem åt dig.
- **Åtkomlig.** Data i Azure Storage är åtkomlig från hela världen via HTTP eller HTTPS varifrån som helst. Microsoft tillhandahåller klient bibliotek för Azure Storage på flera olika språk, inklusive .NET, Java, Node. js, python, PHP, ruby, go, och en vuxen REST API. Azure Storage stöder skriptning i Azure PowerShell eller Azure CLI. Azure-portalen och Azure Storage Explorer erbjuder även enkla visuella lösningar för att arbeta med dina data.  

## <a name="azure-storage-services"></a>Azure Storage-tjänster

I Azure Storage finns dessa datatjänster:

- [Azure Blobs](../blobs/storage-blobs-introduction.md): En mycket skalbar objektlagring för textdata och binära data.
- [Azure Files](../files/storage-files-introduction.md): Hanterade filresurser för distributioner i molnet eller lokalt.
- [Azure Queues](../queues/storage-queues-introduction.md): Ett meddelandearkiv för tillförlitliga meddelandefunktioner mellan programkomponenter.
- [Azure Tables](../tables/table-storage-overview.md): En NoSQL-lagring av schemalös lagring av strukturerade data.

Varje tjänst nås via ett lagringskonto. För att komma igång läser du [Skapa ett lagringskonto](storage-account-create.md).

## <a name="blob-storage"></a>Blob Storage

Azure Blob Storage är Microsofts objektlagringslösning för molnet. Blob Storage är optimerat för att lagra stora mängder ostrukturerade data, exempelvis text eller binära data.

Blob Storage är perfekt för:

- Leverera bilder eller dokument direkt till en webbläsare.
- Lagra filer för distribuerad åtkomst.
- Direktuppspelning av video och ljud.
- Lagra data för säkerhetskopiering och återställning, haveriberedskap och arkivering.
- Lagra data för analys av en tjänst som kan vara lokal eller Azure-baserad.

Objekt i Blob storage kan nås från vilken plats som helst i världen via HTTP eller HTTPS. Användare eller klientprogram kan få åtkomst till blobar via URL-adresser, [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI](https://docs.microsoft.com/cli/azure/storage) eller ett Azure Storage-klientbibliotek. Det finns lagringsklientbibliotek för flera språk, bland annat [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet), [Java](https://docs.microsoft.com/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) och [Ruby](https://azure.github.io/azure-storage-ruby).

Mer information om Blob Storage finns i [Introduktion till bloblagring](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Azure Files

Med [Azure Files](../files/storage-files-introduction.md) kan du konfigurera nätverksfilresurser med hög tillgänglighet som kan nås via SMB-standardprotokollet (Server Message Block). Det innebär att flera virtuella datorer kan dela samma filer med både läs- och skrivbehörighet. Du kan också läsa filerna med hjälp av REST-gränssnittet eller klientbiblioteken för lagring.

En sak som skiljer Azure Files från filer på en företagsfilresurs är att du kan komma åt filerna var som helst i världen med en URL som pekar på filen och som innehåller en SAS-token (signatur för delad åtkomst). Du kan generera SAS-token, som ger specifik åtkomst till en privat resurs under en viss tidsperiod.

Filresurser kan användas för många vanliga scenarier:

- Många lokala program använder filresurser. Den här funktionen gör det enklare att migrera program som delar data till Azure. Om du monterar filresursen med samma enhetsbeteckning som det lokala programmet använder, bör den del av programmet som använder filresursen fungera med minimala, om några, ändringar.

- Konfigurationsfiler kan lagras på en filresurs och nås från flera virtuella datorer. Verktyg och hjälpmedel som används av flera utvecklare i ett team kan lagras på en filresurs, så att alla enkelt kan hitta dem och för att säkerställa att alla använder samma version.

- Diagnostikloggar, mätvärden och kraschdumpar är bara tre exempel på data som kan skrivas till en filresurs och bearbetas eller analyseras senare.

För närvarande stöds inte Active Directory-baserad autentisering och åtkomstkontrollistor (ACL:er). Detta kommer dock att implementeras i en framtida utgåva. Autentiseringsuppgifterna för lagringskontot används för att tillhandahålla autentisering för åtkomst till filresursen. Det betyder att alla som har resursen monterad har fullständig skriv- och läsåtkomst till resursen.

Mer information om Azure Files finns i [Introduktion till Azure Files](../files/storage-files-introduction.md).

## <a name="queue-storage"></a>Queue Storage

Azure-kötjänsten används för att lagra och hämta meddelanden. Kömeddelanden kan vara upp till 64 kB och en kö kan innehålla miljontals meddelanden. Köer används vanligtvis för att lagra listor med meddelanden som ska bearbetas asynkront.

Anta exempelvis att du vill att kunderna ska kunna ladda upp bilder och du vill skapa miniatyrer för varje bild. Du kan låta kunden vänta på att du skapar miniatyrerna medan bilderna laddas upp. Ett alternativ är att använda en kö. När kunden är klar med överföringen skriver du ett meddelande till kön. Sedan hämtar en Azure-funktion meddelandet från kön och skapar miniatyrerna. Alla delar av bearbetningen kan skalas separat, vilket ger dig större kontroll när du anpassar den för din användning.

Mer information om Azure Queues finns i [introduktionen till Queues](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Table Storage

Azure Table Storage är nu en del av Azure Cosmos DB. Du hittar dokumentationen till Azure Table Storage i [Översikt över Azure Table Storage](../tables/table-storage-overview.md). Utöver den befintliga Azure Table Storage-tjänsten finns ett nytt tabell-API för Azure Cosmos DB som tillhandahåller genomströmningsoptimerade tabeller, global distribution och automatiska sekundärindex. Om du vill veta mer om och prova den nya premiumtjänsten går du till avsnittet [Tabell-API för Azure Cosmos DB](https://aka.ms/premiumtables).

Mer information om Table Storage finns i [översikten över Azure Table Storage](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Disklagring

En Azure-hanterad disk är en virtuell hård disk (VHD). Du kan tänka på det som en fysisk disk på en lokal server, men virtualiserad. Azure Managed disks lagras som Page BLOB-objekt, som är ett slumpmässigt IO-lagringssystem i Azure. Vi kallar en hanterad disk hanterad eftersom det är en abstraktion över Page blobbar, BLOB-behållare och Azure Storage-konton. Med hanterade diskar behöver du bara etablera disken och Azure tar hand om resten.

Mer information om hanterade diskar finns i [Introduktion till Azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="types-of-storage-accounts"></a>Typer av lagringskonton

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

Mer information om typer av lagringskonton finns i [Översikt över Azure Storage-konton](storage-account-overview.md).

## <a name="securing-access-to-storage-accounts"></a>Skydda åtkomsten till lagrings konton

Varje begäran till Azure Storage måste vara auktoriserad. Azure Storage stöder följande autentiseringsmetoder:

- **Azure Active Directory (Azure AD)-integration för blob-och Queue-data.** Azure Storage stöder autentisering och auktorisering med Azure AD för blob-och Queue Services via rollbaserad åtkomst kontroll (RBAC). Att auktorisera begär Anden med Azure AD rekommenderas för överlägsen säkerhet och enkel användning. Mer information finns i [bevilja åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](storage-auth-aad.md).
- **Azure AD-auktorisering över SMB för Azure Files (för hands version).** Azure Files stöder Identity-baserad auktorisering över SMB (Server Message Block) via Azure Active Directory Domain Services. Dina domänanslutna virtuella Windows-datorer (VM) kan komma åt Azure-filresurser med hjälp av Azure AD-autentiseringsuppgifter. Mer information finns i [Översikt över Azure Active Directory auktorisering över SMB för Azure Files (för hands version)](../files/storage-files-active-directory-overview.md).
- **Auktorisering med delad nyckel.** Azure Storage Blob-, kö-och tabell tjänster och Azure Files stöd för auktorisering med delad nyckel. en klient som använder autentisering med delad nyckel skickar ett huvud till varje begäran som är signerad med åtkomst nyckeln för lagrings kontot. Mer information finns i [auktorisera med delad nyckel](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key).
- **Auktorisering med signaturer för delad åtkomst (SAS).** En signatur för delad åtkomst (SAS) är en sträng som innehåller en säkerhetstoken som kan läggas till i URI: n för en lagrings resurs. Säkerhetstoken kapslar in begränsningar, till exempel behörigheter och åtkomst intervallet. Mer information finns i [använda signaturer för delad åtkomst (SAS)](storage-sas-overview.md).
- **Anonym åtkomst till behållare och blobbar.** En behållare och dess blobbar kan vara offentligt tillgängliga. När du anger att en behållare eller BLOB är offentlig, kan vem som helst läsa den anonymt. Ingen autentisering krävs. Mer information finns i [Hantera anonym läsbehörighet till containrar och blobar](../blobs/storage-manage-access-to-resources.md).

## <a name="encryption"></a>Kryptering

Det finns några grundläggande typer av kryptering för Storage-tjänsterna. Mer information om säkerhet och kryptering finns i [Säkerhetsguiden för Azure Storage](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Vilande kryptering

Azure Storage kryptering skyddar och skyddar dina data så att de uppfyller organisationens säkerhets-och efterlevnads åtaganden. Azure Storage krypterar automatiskt alla data innan de bevaras kvar på lagrings kontot och dekrypterar det innan hämtning. Processerna för kryptering, dekryptering och nyckel hantering är helt transparenta för användarna. Kunder kan också välja att hantera sina egna nycklar med hjälp av Azure Key Vault. Mer information finns i [Azure Storage kryptering för vilande data](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Kryptering av klientsidan

De Azure Storage klient biblioteken innehåller metoder för att kryptera data från klient biblioteket innan de skickas över kabeln och Dekrypteringen av svaret. Data som krypteras via klient sidans kryptering krypteras också i vila genom att Azure Storage. Mer information om kryptering på klient sidan finns i [kryptering på klient sidan med .net för Azure Storage](storage-client-side-encryption.md).

## <a name="redundancy"></a>Redundans

För att säkerställa att dina data är beständiga, Azure Storage lagra flera kopior av dina data. När du konfigurerar ditt lagringskonto väljer du ett redundansalternativ.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="transferring-data-to-and-from-azure-storage"></a>Överföra data till och från Azure Storage

Du kan flytta data till eller från Azure Storage på flera olika sätt. Vilket alternativ du väljer beror på storleken på datauppsättningen och din nätverksbandbredd. Mer information finns i [Välja en Azure-lösning för dataöverföring](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Priser

Detaljerad information om priser för Azure Storage finns på [prissidan](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>API:er, bibliotek och verktyg för Azure Storage

Azure Storage-resurser kan nås med alla språk som kan skicka HTTP/HTTPS-förfrågningar. Dessutom erbjuder Azure Storage programmeringsbibliotek för flera populära språk. Dessa bibliotek förenklar många aspekter av arbetet med Azure Storage genom att hantera information som till exempel synkrona och asynkrona anrop, massbearbetning av åtgärder, undantagshantering, automatiska omförsök, funktionsbeteenden och så vidare. Bibliotek är för närvarande tillgängliga för följande språk och plattformar, med andra i pipeline:

### <a name="azure-storage-data-api-and-library-references"></a>Referenser till Azure Storage-data-API och -bibliotek

- [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/)
- [Azure Storage klient bibliotek för .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [Azure Storage klient bibliotek för Java/Android](https://docs.microsoft.com/java/api/overview/azure/storage)
- [Azure Storage klient bibliotek för Node. js](https://docs.microsoft.com/javascript/api/overview/azure/storage-overview)
- [Azure Storage klient bibliotek för python](https://github.com/Azure/azure-storage-python)
- [Azure Storage klient bibliotek för PHP](https://github.com/Azure/azure-storage-php)
- [Azure Storage klient bibliotek för ruby](https://github.com/Azure/azure-storage-ruby)
- [Azure Storage klient bibliotek förC++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Referenser till Azure Storage-hanterings-API och -bibliotek

- [REST-API för Storage Resource Provider](https://docs.microsoft.com/rest/api/storagerp/)
- [Klientbibliotek för Storage Resource Provider för .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
- [REST-API för Service Management för Storage (klassisk)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Referenser till Azure Storage-dataflyttnings-API och -bibliotek

- [REST-API för Storage Import/Export Service](https://docs.microsoft.com/rest/api/storageimportexport/)
- [Klientbibliotek för Storage Data Movement för .NET](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Verktyg och hjälpmedel

- [Azure PowerShell-cmdletar för Storage](https://docs.microsoft.com/powershell/module/az.storage)
- [Azure CLI-cmdletar för Storage](https://docs.microsoft.com/cli/azure/storage)
- [Kommandoradsverktyget AzCopy](https://aka.ms/downloadazcopy)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
- [Azure Storage-klientverktyg](../storage-explorers.md)
- [Azure-utvecklarverktyg](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Nästa steg

Kom igång med Azure Storage genom att läsa [Skapa ett lagringskonto](storage-account-create.md).
