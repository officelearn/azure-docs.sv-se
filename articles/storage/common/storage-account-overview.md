---
title: Översikt över lagringskonto
titleSuffix: Azure Storage
description: Förstå alternativ för att skapa och använda ett Azure Storage-konto.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3694a1d04ec25f44cfcf9395bdd148e2fd3c0d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371569"
---
# <a name="storage-account-overview"></a>Översikt över lagringskonto

Ett Azure-lagringskonto innehåller alla dina Azure Storage-dataobjekt: blobbar, filer, köer, tabeller och diskar. Lagringskontot tillhandahåller ett unikt namnområde för dina Azure Storage-data som är tillgängliga var som helst i världen via HTTP eller HTTPS. Data i ditt Azure-lagringskonto är hållbara och högtillgängliga, säkra och enormt skalbara.

Mer information om hur du skapar ett Azure-lagringskonto finns i [Skapa ett lagringskonto](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Typer av lagringskonton

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>General-purpose v2-konton (GPv2)

Generella v2-lagringskonton stöder de senaste Azure Storage-funktionerna och innehåller alla funktioner för generella v1- och Blob-lagringskonton. Generella v2-konton ger de lägsta kapacitetspriserna per gigabyte för Azure Storage samt branschkonkurrella transaktionspriser. Generell v2-lagringskonton stöder dessa Azure Storage-tjänster:

- Blobbar (alla typer: Blockera, Lägg till, Sida)
- Data Lake Gen2
- Filer
- Diskar
- Köer
- Tabeller

> [!NOTE]
> Microsoft rekommenderar att du använder ett allmänt v2-lagringskonto för de flesta scenarier. Du kan enkelt uppgradera ett allmänt v1- eller Blob-lagringskonto till ett allmänt v2-konto utan driftstopp och utan att behöva kopiera data.
>
> Mer information om hur du uppgraderar till ett v2-konto för allmänt ändamål finns i [Uppgradera till ett allmänt v2-lagringskonto](storage-account-upgrade.md).

Generella v2-lagringskonton erbjuder flera åtkomstnivåer för lagring av data baserat på dina användningsmönster. Mer information finns i [Åtkomstnivåer för blockblobbdata](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>General-purpose v1-konton (GPv1)

Generella v1-lagringskonton ger åtkomst till alla Azure Storage-tjänster, men kanske inte har de senaste funktionerna eller den lägsta nivån per gigabyte-prissättning. Generell v1-lagringskonton stöder dessa Azure Storage-tjänster:

- Blobbar (alla typer)
- Filer
- Diskar
- Köer
- Tabeller

Du bör använda allmänna v2-konton i de flesta fall. Du kan använda virtuella v1-konton för allmänt ändamål för följande scenarier:

- Dina program kräver den klassiska Azure-distributionsmodellen. Generella v2-konton och Blob-lagringskonton stöder endast distributionsmodellen för Azure Resource Manager.

- Dina program är transaktionsintensiva eller använder betydande geo-replikeringsbandbredd, men kräver inte stor kapacitet. I detta fall kan generell v1 vara det mest ekonomiska valet.

- Du använder en version av [REST-API:et](https://msdn.microsoft.com/library/azure/dd894041.aspx) för Lagringstjänster som är tidigare än 2014-02-14 eller ett klientbibliotek med en version som är lägre än 4.x. Du kan inte uppgradera ditt program.

### <a name="blockblobstorage-accounts"></a>BlockBlobStorage-konton

Ett BlockBlobStorage-konto är ett specialiserat lagringskonto på premiumprestandanivån för lagring av ostrukturerade objektdata som blockblobbar eller tilläggsblobbar. Jämfört med konton för v2- och BlobStorage-konton i allmänna ändamål ger BlockBlobStorage-konton låg, konsekvent latens och högre transaktionsfrekvens.

BlockBlobStorage-konton stöder för närvarande inte nivåindelning till frekventa, svala eller arkivåtkomstnivåer. Den här typen av lagringskonto stöder inte sidblobar, tabeller eller köer.

### <a name="filestorage-accounts"></a>FileStorage-konton

Ett FileStorage-konto är ett specialiserat lagringskonto som används för att lagra och skapa premiumfilresurser. Den här lagringskontosden stöder filer men blockerar inte blobbar, tilläggsblobar, sidblobar, tabeller eller köer.

FileStorage-konton erbjuder unika prestandadedikerade egenskaper som IOPS-sprängning. Mer information om dessa egenskaper finns i avsnittet [Fildelningslagringsnivåer](../files/storage-files-planning.md#storage-tiers) i guiden Filplanering.

## <a name="naming-storage-accounts"></a>Namnge lagringskonton

Tänk på dessa regler när du namnger lagringskontot:

- Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.
- Namnet på ditt lagringskonto måste vara unikt i Azure. Det får inte finnas två lagringskonton med samma namn.

## <a name="performance-tiers"></a>Prestandanivåer

Beroende på vilken typ av lagringskonto du skapar kan du välja mellan standard- och premiumprestandanivåer.

### <a name="general-purpose-storage-accounts"></a>Allmänna lagringskonton

Lagringskonton för allmänt ändamål kan konfigureras för någon av följande prestandanivåer:

- En standardprestandanivå för lagring av blobbar, filer, tabeller, köer och virtuella Azure-diskar. Mer information om skalbarhetsmål för standardlagringskonton finns i [Skalbarhetsmål för standardlagringskonton](scalability-targets-standard-account.md).
- En premiumprestandanivå för lagring av ohanterat virtuella datordiskar. Microsoft rekommenderar att du använder hanterade diskar med virtuella Azure-datorer i stället för ohanterade diskar. Mer information om skalbarhetsmål för premiumprestandanivån finns i [Skalbarhetsmål för premiumsida blob storage-konton](../blobs/scalability-targets-premium-page-blobs.md).

### <a name="blockblobstorage-storage-accounts"></a>Lagringskonton för BlockBlobStorage

Storage-konton för BlockBlobStorage tillhandahåller en premiumprestandanivå för lagring av blockblobar och tilläggsblobbar. Mer information finns i [Skalbarhetsmål för premiumblockbloloblagringskonton](../blobs/scalability-targets-premium-block-blobs.md).

### <a name="filestorage-storage-accounts"></a>Lagringskonton för FileStorage

FileStorage lagringskonton ger en premium prestandanivå för Azure-filresurser. Mer information finns i [Azure Files skalbarhet och prestandamål](../files/storage-files-scale-targets.md).

## <a name="access-tiers-for-block-blob-data"></a>Åtkomstnivåer för blockblobdata

Azure Storage innehåller olika alternativ för åtkomst till blockblobbdata baserat på användningsmönster. Varje åtkomstnivå i Azure Storage är optimerad för ett visst mönster av dataanvändning. Genom att välja rätt åtkomstnivå för dina behov kan du lagra dina blockblobbdata på det mest kostnadseffektiva sättet.

De tillgängliga åtkomstnivåerna är:

- Nivån **För frekvent** åtkomst. Den här nivån är optimerad för frekvent åtkomst av objekt i lagringskontot. Att komma åt data på den heta nivån är mest kostnadseffektivt, medan lagringskostnaderna är högre. Nya lagringskonton skapas som standard på den heta nivån.
- **Den coola** åtkomstnivån. Den här nivån är optimerad för lagring av stora mängder data som sällan används och lagras i minst 30 dagar. Att lagra data på den häftiga nivån är mer kostnadseffektivt, men det kan vara dyrare att komma åt dessa data än att komma åt data på den aktiva nivån.
- **Arkivnivån.** Den här nivån är endast tillgänglig för enskilda blockblobar. Arkivnivån är optimerad för data som tål flera timmars svarstid för hämtning och som kommer att finnas kvar på arkivnivån i minst 180 dagar. Arkivnivån är det mest kostnadseffektiva alternativet för lagring av data. Det är dock dyrare att komma åt dessa data än att komma åt data på de aktiva eller kalla nivåerna.

Om det finns en förändring i användningsmönstret för dina data kan du växla mellan dessa åtkomstnivåer när som helst. Mer information om åtkomstnivåer finns i [Azure Blob storage: hot, cool och archive access tiers](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> Om du ändrar åtkomstnivån för ett befintligt lagringskonto eller en befintlig blob kan det leda till ytterligare avgifter. Mer information finns i [avsnittet Fakturering för lagringskonto](#storage-account-billing).

## <a name="redundancy"></a>Redundans

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="encryption"></a>Kryptering

Alla data i ditt lagringskonto krypteras på tjänstsidan. Mer information om kryptering finns i [Azure Storage Service Encryption för data i vila](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Slutpunkter för lagringskonto

På ett lagringskonto finns ett unikt namnområde i Azure för dina data. Alla objekt som du lagrar i Azure Storage har en adress som innehåller ditt unika kontonamn. Kombinationen av kontonamnet och Azure Storage-tjänstens slutpunkt bildar slutpunkterna för ditt lagringskonto.

Om ditt allmänt lagringskonto till exempel heter *mystorageaccount*är standardslutpunkterna för det kontot:

- Blob lagring:`https://*mystorageaccount*.blob.core.windows.net`
- Tabell lagring:`https://*mystorageaccount*.table.core.windows.net`
- Kölagring:`https://*mystorageaccount*.queue.core.windows.net`
- Azure-filer:`https://*mystorageaccount*.file.core.windows.net`

> [!NOTE]
> Block blob och blob lagringskonton exponera endast Blob-tjänsten slutpunkten.

Skapa URL:en för åtkomst till ett objekt i ett lagringskonto genom att lägga till objektets plats i lagringskontot till slutpunkten. En blobbadress kan till exempel ha följande format: http://*mittlagringskonto*.blob.core.windows.net/*mincontainer*/*minblobb*.

Du kan också konfigurera ditt lagringskonto så att det använder en anpassad domän för blobbar. Mer information finns i [Konfigurera ett anpassat domännamn för ditt Azure Storage-konto](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Kontrollera åtkomsten till kontodata

Som standard är data i ditt konto endast tillgängliga för dig, kontoägaren. Du har kontroll över vem som kan komma åt dina data och vilka behörigheter de har.

Varje begäran som görs mot ditt lagringskonto måste godkännas. På servicenivå måste begäran innehålla ett giltigt *auktoriseringshuvud.* I det här huvudet ingår all information som krävs för att tjänsten ska validera begäran innan den körs.

Du kan bevilja åtkomst till data i ditt lagringskonto med någon av följande metoder:

- **Azure Active Directory:** Använd Azure Active Directory(Azure AD) autentiseringsuppgifter för att autentisera en användare, grupp eller annan identitet för åtkomst till blob- och ködata. Om autentisering av en identitet lyckas returnerar Azure AD en token som ska användas för att godkänna begäran till Azure Blob-lagring eller Kölagring. Mer information finns [i Autentisera åtkomst till Azure Storage med Azure Active Directory](storage-auth-aad.md).
- **Auktorisering för delad nyckel:** Använd åtkomstnyckeln för lagringskonto för att skapa en anslutningssträng som ditt program använder vid körning för att komma åt Azure Storage. Värdena i anslutningssträngen används för att konstruera *auktoriseringshuvudet* som skickas till Azure Storage. Mer information finns i [Konfigurera Azure Storage-anslutningssträngar](storage-configure-connection-string.md).
- **Signatur för delad åtkomst:** Använd en signatur för delad åtkomst för att delegera åtkomst till resurser i ditt lagringskonto, om du inte använder Azure AD-auktorisering. En signatur för delad åtkomst är en token som kapslar in all information som behövs för att auktorisera en begäran till Azure Storage på URL:en. Du kan ange lagringsresursen, de beviljade behörigheterna och det intervall som behörigheterna är giltiga för som en del av signaturen för delad åtkomst. Mer information finns i [Använda SIGNATURER för delad åtkomst (SAS)](storage-sas-overview.md).

> [!NOTE]
> Autentisera användare eller program med Azure AD-autentiseringsuppgifter ger överlägsen säkerhet och användarvänlighet över andra medel för auktorisering. Du kan fortsätta att använda auktorisering av delad nyckel med dina program, men med hjälp av Azure AD kringgås behovet av att lagra din kontoåtkomstnyckel med din kod. Du kan också fortsätta att använda SIGNATURER för delad åtkomst (SAS) för att bevilja finkornig åtkomst till resurser i ditt lagringskonto, men Azure AD erbjuder liknande funktioner utan att behöva hantera SAS-token eller oroa dig för att återkalla en komprometterad SAS.
>
> Microsoft rekommenderar att du använder Azure AD-auktorisering för dina Azure Storage-blob- och köprogram när det är möjligt.

## <a name="copying-data-into-a-storage-account"></a>Kopiera data till ett lagringskonto

Microsoft tillhandahåller verktyg och bibliotek för att importera data från lokala lagringsenheter eller molnlagringsleverantörer från tredje part. Vilken lösning du använder beror på hur många data du överför.

När du uppgraderar till ett allmänt v2-konto från ett allmänt v1- eller Blob-lagringskonto migreras dina data automatiskt. Microsoft rekommenderar den här vägen för uppgradering av ditt konto. Men om du bestämmer dig för att flytta data från ett allmänt v1-konto till ett Blob-lagringskonto migrerar du dina data manuellt med hjälp av de verktyg och bibliotek som beskrivs nedan.

### <a name="azcopy"></a>AzCopy

AzCopy är ett Windows-kommandoradsverktyg för högpresterande kopiering av data till och från Azure Storage. Du kan använda AzCopy för att kopiera data till ett Blob-lagringskonto från ett befintligt allmänt lagringskonto eller för att överföra data från lokala lagringsenheter. Mer information finns i [Överföra data med kommandoradsverktyget AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Bibliotek för flytt av data

Azure Storage-biblioteket för flytt av data för .NET är baserat på det ramverk för flytt av data som används för AzCopy. Biblioteket är utformat för högpresterande, tillförlitliga och enkla åtgärder för dataöverföring och liknar de som används i AzCopy. Du kan använda dataförflyttningsbiblioteket för att dra nytta av AzCopy-funktioner internt. Mer information finns i [Azure Storage Data Movement Library för .NET](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>REST-API eller klientbibliotek

Du kan skapa ett anpassat program för att migrera dina data från ett allmänt v1-lagringskonto till ett Blob-lagringskonto. Använd ett av Azure-klientbiblioteken eller AZURE storage services REST API. Azure Storage innehåller omfattande klientbibliotek för flera språk och plattformar som .NET, Java, C++, Node.JS, PHP, Ruby och Python. Klientbiblioteken har avancerade funktioner, t.ex. logik för omprövning, loggning och parallell överföring. Du kan också utveckla direkt mot REST-API:t, som kan anropas med valfritt språk som kan skicka HTTP/HTTPS-begäranden.

Mer information om AZURE Storage REST API finns i [Azure Storage Services REST API Reference](https://docs.microsoft.com/rest/api/storageservices/).

> [!IMPORTANT]
> Blobar som krypteras med kryptering på klientsidan lagrar krypteringsrelaterade metadata tillsammans med bloben. Om du kopierar en blob som är krypterad med kryptering på klientsidan bör du se till att kopieringen bevarar blobmetadata och framför allt krypteringsrelaterade metadata. Om du kopierar en blob utan krypteringsmetadata kan blobinnehållet inte hämtas igen. Mer information om krypteringsrelaterade metadata finns i [Azure Storage Client Side Encryption](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="storage-account-billing"></a>Fakturering för lagringskonto

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>Nästa steg

- [skapar ett lagringskonto](storage-account-create.md)
- [Skapa ett blockblobslagringskonto](../blobs/storage-blob-create-account-block-blob.md)
