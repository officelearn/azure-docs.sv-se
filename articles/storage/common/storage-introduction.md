---
title: Introduktion till Azure Storage – Molnlagring i Azure | Microsoft Docs
description: Azure Storage-plattformen är Microsofts molnlagringslösning. Azure Storage tillhandahåller lagring för dataobjekt som har hög tillgänglighet och är säkra, robusta, mycket skalbara och redundanta.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 6c75b9c2feeab32c139998f617cacf540733e3bd
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456353"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>Introduktion till de grundläggande Azure Storage-tjänsterna

Azure Storage-plattformen är Microsofts molnlagringslösning för moderna datalagringsscenarier. Kärnlagringstjänster erbjuder ett enormt skalbart objektlager för dataobjekt, disklagring för virtuella Azure-datorer , en filsystemtjänst för molnet, ett meddelandearkiv för tillförlitliga meddelanden och ett NoSQL-arkiv. Tjänsterna är:

- **Pålitlig och mycket tillgänglig.** Redundans garanterar att dina data är säkra i händelse av tillfälliga maskinvarufel. Du kan också välja att replikera data i datacenter eller geografiska regioner för ytterligare skydd mot lokala allvarliga händelser eller naturkatastrofer. Data som replikeras på det här sättet förblir mycket tillgängliga vid ett oväntat avbrott.
- **Säker.** Alla data som skrivs till ett Azure-lagringskonto krypteras av tjänsten. Med Azure Storage får du detaljerad kontroll över vem som har tillgång till dina data.
- **Skalbar.** Azure Storage är utformat för att vara mycket skalbart för att uppfylla krav på datalagring och prestanda för dagens program.
- **Hanterad.** Azure hanterar maskinvaruunderhåll, uppdateringar och kritiska problem åt dig.
- **Åtkomlig.** Data i Azure Storage är åtkomlig från hela världen via HTTP eller HTTPS varifrån som helst. Microsoft tillhandahåller klientbibliotek för Azure Storage på en mängd olika språk, inklusive .NET, Java, Node.js, Python, PHP, Ruby, Go och andra, samt ett moget REST API. Azure Storage stöder skriptning i Azure PowerShell eller Azure CLI. Azure-portalen och Azure Storage Explorer erbjuder även enkla visuella lösningar för att arbeta med dina data.  

## <a name="core-storage-services"></a>Grundläggande lagringstjänster

Azure Storage-plattformen innehåller följande datatjänster:

- [Azure Blobs](../blobs/storage-blobs-introduction.md): En mycket skalbar objektlagring för textdata och binära data. Innehåller även stöd för stordataanalys via Data Lake Storage Gen2.
- [Azure Files](../files/storage-files-introduction.md): Hanterade filresurser för distributioner i molnet eller lokalt.
- [Azure Queues](../queues/storage-queues-introduction.md): Ett meddelandearkiv för tillförlitliga meddelandefunktioner mellan programkomponenter.
- [Azure Tables](../tables/table-storage-overview.md): En NoSQL-lagring av schemalös lagring av strukturerade data.
- [Azure-diskar: Lagringsvolymer](../../virtual-machines/windows/managed-disks-overview.md)på blocknivå för virtuella Azure-datorer.

Varje tjänst nås via ett lagringskonto. För att komma igång läser du [Skapa ett lagringskonto](storage-account-create.md).

## <a name="example-scenarios"></a>Exempelscenarier

I följande tabell jämförs Filer, Blobbar, Diskar, Köer och Tabeller och visar exempelscenarier för var och en.

| Funktion | Beskrivning | När du ska använda detta |
|--------------|-------------|-------------|
| **Azure-filer** |Erbjuder fullständigt hanterade molnfilresurser som du kan komma åt var som helst via SMB-protokollet (Industry Standard Message Block).<br><br>Du kan montera Azure-filresurser från moln- eller lokala distributioner av Windows, Linux och macOS. | Du vill "lyfta och flytta" ett program till molnet som redan använder de inbyggda api:erna för filsystem för att dela data mellan det och andra program som körs i Azure.<br/><br/>Du vill ersätta eller komplettera lokala filservrar eller NAS-enheter.<br><br> Du vill lagra utvecklings- och felsökningsverktyg som måste nås från många virtuella datorer. |
| **Azure-blobar** | Gör att ostrukturerade data kan lagras och nås i stor skala i blockblobar.<br/><br/>Stöder även [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) för lösningar för stordataanalys för företag. | Du vill att ditt program ska stödja strömmande och slumpmässiga åtkomstscenarier.<br/><br/>Du vill kunna komma åt programdata var som helst.<br/><br/>Du vill skapa en företagsdatasjö på Azure och utföra stordataanalys. |
| **Azure-diskar** | Gör att data kan lagras och nås från en ansluten virtuell hårddisk. | Du vill "lyfta och flytta" program som använder inbyggda api:er för filsystem för att läsa och skriva data till beständiga diskar.<br/><br/>Du vill lagra data som inte behöver nås från utanför den virtuella datorn som disken är ansluten till. |
| **Azure-köer** | Tillåter asynkron meddelandekö mellan programkomponenter. | Du vill frikoppla programkomponenter och använda asynkrona meddelanden för att kommunicera mellan dem.<br><br>Vägledning om när kölagring kontra servicebussköer ska användas finns i [Köer för lagring och Service Bus – jämfört och kontrasterade](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted). |
| **Azure-tabeller** | Gör att du kan lagra strukturerade NoSQL-data i molnet, vilket ger en nyckel/attributlagring med en schemalös design. | Du vill lagra flexibla datauppsättningar som användardata för webbprogram, adressböcker, enhetsinformation eller andra typer av metadata som tjänsten kräver. <br/><br/>Mer information om när du ska använda Tabelllagring jämfört med Azure Cosmos DB Table API finns i [Utveckla med Azure Cosmos DB Table API och Azure Table Storage](../../cosmos-db/table-support.md). |

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

Mer information om Azure Files finns i [Introduktion till Azure Files](../files/storage-files-introduction.md).

Vissa SMB-funktioner är inte tillämpliga på molnet. Mer information finns i [Funktioner som inte stöds av Azure File-tjänsten](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).

## <a name="queue-storage"></a>Queue Storage

Azure-kötjänsten används för att lagra och hämta meddelanden. Kömeddelanden kan vara upp till 64 kB och en kö kan innehålla miljontals meddelanden. Köer används vanligtvis för att lagra listor med meddelanden som ska bearbetas asynkront.

Anta exempelvis att du vill att kunderna ska kunna ladda upp bilder och du vill skapa miniatyrer för varje bild. Du kan låta kunden vänta på att du skapar miniatyrerna medan bilderna laddas upp. Ett alternativ är att använda en kö. När kunden är klar med sin uppladdning skriver du ett meddelande till kön. Sedan hämtar en Azure-funktion meddelandet från kön och skapar miniatyrerna. Alla delar av bearbetningen kan skalas separat, vilket ger dig större kontroll när du anpassar den för din användning.

Mer information om Azure Queues finns i [introduktionen till Queues](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Table Storage

Azure Table Storage är nu en del av Azure Cosmos DB. Du hittar dokumentationen till Azure Table Storage i [Översikt över Azure Table Storage](../tables/table-storage-overview.md). Utöver den befintliga Azure Table Storage-tjänsten finns ett nytt tabell-API för Azure Cosmos DB som tillhandahåller genomströmningsoptimerade tabeller, global distribution och automatiska sekundärindex. Mer information och prova den nya premiumupplevelsen finns i [Azure Cosmos DB Table API](https://aka.ms/premiumtables).

Mer information om Table Storage finns i [översikten över Azure Table Storage](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Disklagring

En Azure-hanterad disk är en virtuell hårddisk (VHD). Du kan se det som en fysisk disk i en lokal server men virtualiserad. Azure-hanterade diskar lagras som sidblobar, som är ett slumpmässigt IO-lagringsobjekt i Azure. Vi anropar en hanterad disk som "hanteras" eftersom det är en abstraktion över sidblobar, blob-behållare och Azure-lagringskonton. Med hanterade diskar behöver du bara etablera disken och Azure tar hand om resten.

Mer information om hanterade diskar finns i [Introduktion till Azure-hanterade diskar](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="types-of-storage-accounts"></a>Typer av lagringskonton

Azure Storage erbjuder flera typer av lagringskonton. Varje typ stöder olika funktioner och har sin egen prismodell. Mer information om typer av lagringskonton finns i [Översikt över Azure Storage-konton](storage-account-overview.md).

## <a name="secure-access-to-storage-accounts"></a>Säker åtkomst till lagringskonton

Varje begäran till Azure Storage måste godkännas. Azure Storage stöder följande auktoriseringsmetoder:

- **Azure Active Directory (Azure AD) integration för blob och ködata.** Azure Storage stöder autentisering och auktorisering med Azure AD för Blob- och Queue-tjänsterna via rollbaserad åtkomstkontroll (RBAC). Att godkänna begäranden med Azure AD rekommenderas för överlägsen säkerhet och användarvänlighet. Mer information finns i [Auktorisera åtkomst till Azure-blobbar och köer med Azure Active Directory](storage-auth-aad.md).
- **Azure AD-auktorisering över SMB för Azure-filer.** Azure Files stöder identitetsbaserad auktorisering via SMB (Server Message Block) via antingen Azure Active Directory Domain Services (Azure AD DS) eller lokala Active Directory Domain Services (förhandsversion). Dina domänanslutna Virtuella datorer med Windows kan komma åt Azure-filresurser med Azure AD-autentiseringsuppgifter. Mer information finns i [Översikt över Azure Files identitetsbaserade autentiseringsstöd för SMB-åtkomst](../files/storage-files-active-directory-overview.md) och planering för en Azure [Files-distribution](../files/storage-files-planning.md#identity).
- **Auktorisering med delad nyckel.** Azure Storage Blob, Files, Queue och Table services stöder auktorisering med delad nyckel. En klient som använder auktorisering för delad nyckel skickar ett huvud med varje begäran som signeras med åtkomstnyckeln för lagringskontot. Mer information finns i [Auktorisera med delad nyckel](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key).
- **Auktorisering med signaturer för delad åtkomst (SAS).** En SAS (Shared Access Signature) är en sträng som innehåller en säkerhetstoken som kan läggas till i URI för en lagringsresurs. Säkerhetstoken kapslar in begränsningar som behörigheter och åtkomstintervallet. Mer information finns i [Använda SIGNATURER för delad åtkomst (SAS)](storage-sas-overview.md).
- **Anonym åtkomst till behållare och blobbar.** En behållare och dess blobbar kan vara allmänt tillgängliga. När du anger att en behållare eller blob är offentlig kan vem som helst läsa den anonymt. ingen autentisering krävs. Mer information finns i [Hantera anonym läsåtkomst till behållare och blobbar](../blobs/storage-manage-access-to-resources.md).

## <a name="encryption"></a>Kryptering

Det finns två grundläggande typer av kryptering tillgängliga för kärnlagringstjänster. Mer information om säkerhet och kryptering finns i [Säkerhetsguiden för Azure Storage](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Vilande kryptering

Azure Storage-kryptering skyddar och skyddar dina data för att uppfylla dina organisatoriska säkerhets- och efterlevnadsåtaganden. Azure Storage krypterar automatiskt alla data innan de sparas till lagringskontot och dekrypterar dem före hämtning. Kryptering, dekryptering och nyckelhanteringsprocesser är transparenta för användarna. Kunder kan också välja att hantera sina egna nycklar med Hjälp av Azure Key Vault. Mer information finns i [Azure Storage-kryptering för data i vila](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Kryptering av klientsidan

Azure Storage-klientbiblioteken tillhandahåller metoder för att kryptera data från klientbiblioteket innan de skickar dem över kabeln och dekrypterar svaret. Data som krypteras via kryptering på klientsidan krypteras också i vila av Azure Storage. Mer information om kryptering på klientsidan finns i [Kryptering på klientsidan med .NET för Azure Storage](storage-client-side-encryption.md).

## <a name="redundancy"></a>Redundans

Azure Storage lagrar flera kopior av dina data för att säkerställa att dina data är varaktiga. När du konfigurerar ditt lagringskonto väljer du ett redundansalternativ. Läs mer i [Redundansalternativ för Azure Storage](/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json).

## <a name="transfer-data-to-and-from-azure-storage"></a>Överföra data till och från Azure Storage

Du kan flytta data till eller från Azure Storage på flera olika sätt. Vilket alternativ du väljer beror på storleken på datauppsättningen och din nätverksbandbredd. Mer information finns i [Välja en Azure-lösning för dataöverföring](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Prissättning

När du fattar beslut om hur dina data lagras och nås bör du också ta hänsyn till kostnaderna. Mer information finns i [Azure Storage-priser](https://azure.microsoft.com/pricing/details/storage/).

## <a name="storage-apis-libraries-and-tools"></a>API:er, bibliotek och verktyg för Azure Storage

Du kan komma åt resurser i ett lagringskonto på vilket språk som helst som kan göra HTTP/HTTPS-begäranden. Dessutom erbjuder de grundläggande Azure Storage-tjänsterna programmeringsbibliotek för flera populära språk. Dessa bibliotek förenklar många aspekter av arbetet med Azure Storage genom att hantera information som till exempel synkrona och asynkrona anrop, massbearbetning av åtgärder, undantagshantering, automatiska omförsök, funktionsbeteenden och så vidare. Bibliotek är för närvarande tillgängliga för följande språk och plattformar, med andra i pipeline:

### <a name="azure-storage-data-api-and-library-references"></a>Referenser till Azure Storage-data-API och -bibliotek

- [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/)
- [Azure Storage-klientbibliotek för .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [Azure Storage-klientbibliotek för Java/Android](https://docs.microsoft.com/java/api/overview/azure/storage)
- [Azure Storage-klientbibliotek för Node.js](https://docs.microsoft.com/javascript/api/overview/azure/storage-overview)
- [Azure Storage-klientbibliotek för Python](https://github.com/Azure/azure-storage-python)
- [Azure Storage-klientbibliotek för PHP](https://github.com/Azure/azure-storage-php)
- [Azure Storage-klientbibliotek för Ruby](https://github.com/Azure/azure-storage-ruby)
- [Azure Storage-klientbibliotek för C++](https://github.com/Azure/azure-storage-cpp)

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
- [Kommandoradsverktyg för AzCopy](https://aka.ms/downloadazcopy)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
- [Klientverktyg för Azure Storage](../storage-explorers.md)
- [Azure-utvecklarverktyg](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med grundläggande Azure Storage-tjänster finns i [Skapa ett lagringskonto](storage-account-create.md).
