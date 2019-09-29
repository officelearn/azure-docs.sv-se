---
title: Översikt över Azure Storage-konto | Microsoft Docs
description: Förstå alternativen för att skapa och använda ett Azure Storage-konto.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 67022c6bd9e237ce24d8e63285f7ebabadca87c6
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671317"
---
# <a name="azure-storage-account-overview"></a>Översikt över Azure Storage-kontot

Ett Azure Storage-konto innehåller alla dina Azure Storage data objekt: blobbar, filer, köer, tabeller och diskar. Lagrings kontot tillhandahåller ett unikt namn område för dina Azure Storage data som är tillgängliga från var som helst i världen via HTTP eller HTTPS. Data i ditt Azure Storage-konto är tåliga och mycket tillgängliga, säkra och enorma skalbara.

Information om hur du skapar ett Azure Storage-konto finns i [skapa ett lagrings konto](storage-quickstart-create-account.md).

## <a name="types-of-storage-accounts"></a>Typer av lagringskonton

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>General-purpose v2-konton (GPv2)

Allmänna-Purpose v2-lagrings konton har stöd för de senaste Azure Storage funktionerna och inkluderar alla funktioner i generella v1-och Blob Storage-konton. Allmänna-Purpose v2-konton ger de lägsta priserna per Gigabyte för Azure Storage, samt priser för priser som är konkurrerande för branschen. Allmänna-Purpose v2-lagrings konton har stöd för dessa Azure Storage tjänster:

- Blobbar (alla typer: Blockera, lägga till, sida)
- Filer
- Diskar
- Köer
- Tabeller

> [!NOTE]
> Microsoft rekommenderar att du använder ett allmänt-syfte v2-lagrings konto för de flesta scenarier. Du kan enkelt uppgradera ett allmänt v1-eller Blob Storage-konto till ett allmänt-syfte v2-konto utan avbrott och utan att behöva kopiera data.
>
> Mer information om hur du uppgraderar till ett allmänt-syfte v2-konto finns i [Uppgradera till ett allmänt-syfte v2-lagrings konto](storage-account-upgrade.md).

Allmänna-syfte v2-lagrings konton ger flera åtkomst nivåer för lagring av data baserat på dina användnings mönster. Mer information finns i [åtkomst nivåer för block BLOB-data](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>General-purpose v1-konton (GPv1)

Allmänna v1-konton ger till gång till alla Azure Storage-tjänster, men de har inte de senaste funktionerna eller de lägsta priset per Gigabyte. Generella v1-lagrings konton har stöd för dessa Azure Storage tjänster:

- Blobbar (alla typer)
- Filer
- Diskar
- Köer
- Tabeller

Även om allmänna-Purpose v2-konton rekommenderas i de flesta fall är allmänna v1-konton bäst lämpade för följande scenarier:

* Dina program kräver den klassiska Azure-distributions modellen. Allmänna-Purpose v2-konton och Blob Storage-konton stöder bara Azure Resource Manager distributions modellen.

* Dina program är transaktions intensiva eller använder avsevärd bandbredd för geo-replikering, men kräver inte stor kapacitet. I det här fallet kan generell användning v1 vara det mest ekonomiska valet.

* Du använder en version av [Storage Services REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) som är äldre än 2014-02-14 eller ett klientbiblioteket med en tidigare version än 4.x och det inte går att uppgradera ditt program.

### <a name="block-blob-storage-accounts"></a>Blockera Blob Storage-konton

Ett Block Blob Storage-konto är ett specialiserat lagrings konto för lagring av ostrukturerade objekt data som block-blobbar. Den här lagrings konto typen stöder block-blobbar och tillägg av blobar, men inte sid-blobar, tabeller eller köer.

I jämförelse med General-Purpose v2-och Blob Storage-konton kan du blockera Blob Storage-konton med låg och konsekvent svars tid och högre transaktions kostnader.

Block för Blob Storage-konton stöder för närvarande inte lagrings nivåer för frekvent åtkomst, låg frekvent åtkomst eller Arkiv lag rings nivåer.

### <a name="filestorage-storage-accounts"></a>FileStorage lagrings konton

Ett FileStorage lagrings konto är ett specialiserat lagrings konto som används för att lagra och skapa Premium-filresurser. FileStorage lagrings konton ger unika prestanda dedikerade egenskaper som IOPS-burst. Mer information om dessa egenskaper finns i avsnittet om [prestanda nivåer för fil resurser](../files/storage-files-planning.md#file-share-performance-tiers) i planerings guiden för filer.

## <a name="naming-storage-accounts"></a>Namnge lagrings konton

Tänk på dessa regler när du namnger lagringskontot:

- Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.
- Namnet på ditt lagringskonto måste vara unikt i Azure. Det får inte finnas två lagringskonton med samma namn.

## <a name="performance-tiers"></a>Prestandanivåer

Allmänna lagrings konton kan konfigureras för någon av följande prestanda nivåer:

* En standard prestanda nivå för att lagra blobbar, filer, tabeller, köer och virtuella Azure-datorer.
* En Premium prestanda nivå för lagring av ohanterade virtuella dator diskar.

Blockera Blob Storage-konton ger en förstklassig prestanda nivå för lagring av block-blobbar och tillägg av blobbar.

FileStorage lagrings konton ger en förstklassig prestanda nivå för Azure-filresurser.

## <a name="access-tiers-for-block-blob-data"></a>Åtkomst nivåer för block BLOB-data

Azure Storage tillhandahåller olika alternativ för åtkomst till block BLOB-data baserat på användnings mönster. Varje åtkomst nivå i Azure Storage är optimerad för ett visst mönster för data användning. Genom att välja rätt åtkomst nivå för dina behov kan du lagra block BLOB-data på det mest kostnads effektiva sättet.

Tillgängliga åtkomst nivåer är:

* Frekvent **åtkomst nivå** , som är optimerad för frekvent åtkomst av objekt i lagrings kontot. Att komma åt data på frekvent nivå är mest kostnads effektivt, medan lagrings kostnaderna är högre. Nya lagrings konton skapas som standard på frekvent nivå.
* Den **låg** frekventa åtkomst nivån, som är optimerad för att lagra stora mängder data som används sällan och som lagras i minst 30 dagar. Att lagra data i den låg frekventa nivån är mer kostnads effektivt, men att komma åt dessa data kan vara dyrare än att komma åt data på frekvent nivå.
* **Arkiv** nivån, som endast är tillgänglig för enskilda block blobbar. Arkiv nivån är optimerad för data som kan tolerera flera timmars svars tid för hämtning och kommer att finnas kvar på Arkiv nivån i minst 180 dagar. Arkiv lag rings nivån är det mest kostnads effektiva alternativet för att lagra data, men att komma åt dessa data är dyrare än att komma åt data på frekventa eller låg frekventa nivåer.

Om det finns en ändring i användnings mönstret för dina data kan du när som helst växla mellan dessa åtkomst nivåer. Mer information om åtkomst nivåer finns i [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](../blobs/storage-blob-storage-tiers.md)lag rings nivåer.

> [!IMPORTANT]
> Om du ändrar åtkomst nivån för ett befintligt lagrings konto eller BLOB kan ytterligare kostnader uppstå. Mer information finns i [avsnittet fakturering av lagrings konto](#storage-account-billing).

## <a name="replication"></a>Replikering

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Mer information om Storage Replication finns [Azure Storage replikering](storage-redundancy.md).

## <a name="encryption"></a>Kryptering

Alla data i ditt lagrings konto krypteras på tjänst sidan. Mer information om kryptering finns i [Azure Storage tjänst kryptering för vilande data](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Lagringskontoslutpunkter

På ett lagringskonto finns ett unikt namnområde i Azure för dina data. Alla objekt som du lagrar i Azure Storage har en adress som innehåller ditt unika kontonamn. Kombinationen av kontonamnet och Azure Storage-tjänstens slutpunkt bildar slutpunkterna för ditt lagringskonto.

Om ditt allmänna lagrings konto till exempel heter *mystorageaccount*, är standard slut punkterna för det kontot:

* Blob Storage: http://*mystorageaccount*. blob.Core.Windows.net
* Table Storage: http://*mystorageaccount*. Table.Core.Windows.net
* Queue Storage: http://*mystorageaccount*. Queue.Core.Windows.net
* Azure Files: http://*mystorageaccount*. File.Core.Windows.net

> [!NOTE]
> Block-Blob-och Blob Storage-konton exponerar endast BLOB service-slutpunkten.

URL: en för att komma åt ett objekt i ett lagrings konto skapas genom att objektets plats i lagrings kontot läggs till i slut punkten. En blobbadress kan till exempel ha följande format: http://*mittlagringskonto*.blob.core.windows.net/*minbehållare*/*minblobb*.

Du kan också konfigurera ditt lagrings konto så att det använder en anpassad domän för blobbar. Mer information finns i [Konfigurera ett anpassat domän namn för ditt Azure Storage-konto](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Kontrol lera åtkomst till konto data

Som standard är data i ditt konto endast tillgängliga för dig, kontoägaren. Du har kontroll över vem som kan komma åt dina data och vilka behörigheter de har.

Varje begäran som görs mot ditt lagrings konto måste vara auktoriserad. På nivån för tjänsten måste begäran innehålla ett giltigt *Authorization* -huvud, som innehåller all information som behövs för tjänsten för att verifiera begäran innan den körs.

Du kan bevilja åtkomst till data i ditt lagrings konto med någon av följande metoder:

- **Azure Active Directory:** Använd Azure Active Directory (Azure AD) autentiseringsuppgifter för att autentisera en användare, grupp eller annan identitet för åtkomst till blob-och Queue-data. Om autentiseringen av en identitet lyckas returnerar Azure AD en token som ska användas för att auktorisera begäran till Azure Blob Storage eller Queue Storage. Mer information finns i [autentisera åtkomst till Azure Storage med hjälp av Azure Active Directory](storage-auth-aad.md).
- **Auktorisering av delad nyckel:** Använd din åtkomst nyckel för lagrings kontot för att skapa en anslutnings sträng som programmet använder vid körning för att få åtkomst till Azure Storage. Värdena i anslutnings strängen används för att skapa ett *Authorization* -huvud som skickas till Azure Storage. Mer information finns i [Konfigurera anslutnings strängar för Azure Storage](storage-configure-connection-string.md).
- **Signatur för delad åtkomst:** Använd en signatur för delad åtkomst för att delegera åtkomst till resurser i ditt lagrings konto, om du inte använder Azure AD-auktorisering. En signatur för delad åtkomst är en token som kapslar in all information som behövs för att auktorisera en begäran till Azure Storage på URL: en. Du kan ange lagrings resursen, de behörigheter som beviljats och det intervall med vilket behörigheterna är giltiga som en del av signaturen för delad åtkomst. Mer information finns i [använda signaturer för delad åtkomst (SAS)](storage-sas-overview.md).

> [!NOTE]
> Autentisering av användare eller program som använder Azure AD-autentiseringsuppgifter ger överlägsen säkerhet och lätt att använda för andra auktoriserings metoder. Även om du kan fortsätta att använda autentisering med delad nyckel med dina program kan du använda Azure AD för att kringgå behovet av att lagra din konto åtkomst nyckel med din kod. Du kan även fortsätta att använda signaturer för delad åtkomst (SAS) för att ge detaljerad åtkomst till resurser i ditt lagrings konto, men Azure AD erbjuder liknande funktioner utan att behöva hantera SAS-token eller oroa dig för att återkalla en komprometterad SAS. 
>
> Microsoft rekommenderar att du använder Azure AD-auktorisering för Azure Storage blob-och Queue-program när det är möjligt.

## <a name="copying-data-into-a-storage-account"></a>Kopiera data till ett lagrings konto

Microsoft tillhandahåller verktyg och bibliotek för att importera data från lokala lagrings enheter eller moln lagrings leverantörer från tredje part. Vilken lösning du använder beror på mängden data som du överför. 

När du uppgraderar till ett allmänt-syfte v2-konto från ett allmänt v1-eller Blob Storage-konto migreras dina data automatiskt. Microsoft rekommenderar den här väg för att uppgradera ditt konto. Men om du bestämmer dig för att flytta data från ett allmänt v1-konto till ett Blob Storage-konto måste du migrera dina data manuellt med hjälp av de verktyg och bibliotek som beskrivs nedan. 

### <a name="azcopy"></a>AzCopy

AzCopy är ett Windows-kommandoradsverktyg för högpresterande kopiering av data till och från Azure Storage. Du kan använda AzCopy för att kopiera data till ett Blob Storage-konto från ett befintligt allmänt lagrings konto, eller för att ladda upp data från lokala lagrings enheter. Mer information finns i [Överföra data med kommandoradsverktyget AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Bibliotek för flytt av data

Azure Storage-biblioteket för flytt av data för .NET är baserat på det ramverk för flytt av data som används för AzCopy. Biblioteket är utformat för högpresterande, tillförlitliga och enkla åtgärder för dataöverföring och liknar de som används i AzCopy. Du kan använda det för att utnyttja fördelarna med funktionerna som tillhandahålls av AzCopy direkt i din app utan att du behöver köra och övervaka externa instanser av AzCopy. Mer information finns i [Azure Storage Data Movement Library for .Net](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>REST-API eller klientbibliotek

Du kan skapa en anpassad app för att migrera dina data till ett Blob Storage-konto med Azures klientbibliotek eller REST-API:t för Azure Storage-tjänster. Azure Storage innehåller omfattande klientbibliotek för flera språk och plattformar som .NET, Java, C++, Node.JS, PHP, Ruby och Python. Klientbiblioteken har avancerade funktioner, t.ex. logik för omprövning, loggning och parallell överföring. Du kan också utveckla direkt mot REST-API:t, som kan anropas med valfritt språk som kan skicka HTTP/HTTPS-begäranden.

Mer information om Azure Storage REST API finns i [Azure Storage Services REST API Reference](https://docs.microsoft.com/rest/api/storageservices/). 

> [!IMPORTANT]
> Blobar som krypteras med kryptering på klientsidan lagrar krypteringsrelaterade metadata tillsammans med bloben. Om du kopierar en blob som är krypterad med kryptering på klientsidan bör du se till att kopieringen bevarar blobmetadata och framför allt krypteringsrelaterade metadata. Om du kopierar en blob utan krypteringsmetadata kan blobinnehållet inte hämtas igen. Mer information om krypteringsrelaterade metadata finns i [Azure Storage Client Side Encryption](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="azure-importexport-service"></a>Azure import/export-tjänsten

Om du har en stor mängd data som ska importeras till ditt lagrings konto bör du ta hänsyn till Azure import/export-tjänsten. Import/export-tjänsten används för att på ett säkert sätt importera stora mängder data till Azure Blob Storage och Azure Files genom att leverera disk enheter till ett Azure-datacenter. 

Import/export-tjänsten kan också användas för att överföra data från Azure Blob Storage till disk enheter och leverera till dina lokala platser. Data från en eller flera disk enheter kan importeras antingen till Azure Blob Storage eller Azure Files. Mer information finns i [Vad är Azure import/export-tjänsten?](https://docs.microsoft.com/azure/storage/common/storage-import-export-service).

## <a name="storage-account-billing"></a>Fakturering för lagringskonto

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar ett allmänt Azure Storage-konto finns i [skapa ett lagrings konto](storage-quickstart-create-account.md).
* Information om hur du skapar ett Block Blob Storage-konto finns i [skapa ett Block Blob Storage-konto](../blobs/storage-blob-create-account-block-blob.md).
* Information om hur du hanterar eller tar bort ett befintligt lagrings konto finns i [Hantera Azure Storage-konton](storage-account-manage.md).
