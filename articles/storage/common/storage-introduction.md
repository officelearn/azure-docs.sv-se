---
title: Introduktion till Azure Storage | Microsoft Docs
description: Introduktion till Azure Storage, Microsofts datalagring i molnet.
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: a4a1bc58-ea14-4bf5-b040-f85114edc1f1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/10/2017
ms.author: tamram
ms.openlocfilehash: e0da76d1c99de94762a54f552e49f7ee75eba26f
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="introduction-to-microsoft-azure-storage"></a>Introduktion till Microsoft Azure Storage

Microsoft Azure Storage är en Microsoft-hanterad molntjänst som tillhandahåller lagring som är mycket tillgänglig, säker, beständig, skalbar och redundant. Microsoft tar hand om underhåll och hanterar kritiska problem åt dig.

Azure Storage består av tre datatjänster: Blob Storage, File Storage och Queue Storage. Blob Storage stöder både standard- och premiumlagring. För premiumlagring används endast SSD för snabbaste möjliga prestanda. En annan funktion är lågfrekvent lagring, som gör att du kan lagra stora mängder data som sällan används till en lägre kostnad.

I den här artikeln får du veta mer om följande:
* Azure Storage-tjänsterna
* typerna av lagringskonton
* åtkomst till dina blobar, köer och filer
* kryptering
* replikering
* överföring av data till och från lagring
* de många lagringsklientbiblioteken som finns tillgängliga.

Kom igång med Azure Storage genom att läsa [Skapa ett lagringskonto](storage-quickstart-create-account.md).

## <a name="introducing-the-azure-storage-services"></a>Introduktion till Azure Storage-tjänsterna

Om du vill använda någon av de tjänster som tillhandahålls av Azure Storage – Blob Storage, File Storage och Queue Storage – skapar du först ett lagringskonto och sedan kan du överföra data till/från en specifik tjänst i det lagringskontot.

## <a name="blob-storage"></a>Blob Storage

Blobbar är i princip filer som de du lagrar på en dator (eller surfplatta, mobil enhet och så vidare). De kan vara bilder, Microsoft Excel-filer, HTML-filer, virtuella hårddiskar (VHD), stordata, till exempel loggar, databassäkerhetskopior – nästan allt. Blobbar lagras i behållare som liknar mappar.

När du lagrar filer i Blob Storage kan du komma åt dem var som helst i världen med hjälp av URL:er, REST-gränssnittet eller något av Azure SDK-lagringsklientbiblioteken. Det finns lagringsklientbibliotek för flera språk, bland annat Node.js, Java, PHP, Ruby, Python och .NET.

Det finns tre typer av blobbar – blockblobbar, sidblobbar (används för VHD-filer) och tilläggsblobbar.

* Blockblobbar används för att lagra vanliga filer upp till ungefär 4,7 TB.
* Sidblobbar används för att lagra filer med slumpmässig åtkomst upp till 8 TB. De används för VHD-filerna som stöder virtuella datorer.
* Tilläggsblobbar består av block precis som blockblobbarna, men är optimerade för tilläggsåtgärder. De används till exempel för att logga information till samma blob från flera virtuella datorer.

För mycket stora datamängder där nätverksbegränsningar gör det orealistiskt att överföra eller hämta data till Blob Storage via kabel kan du skicka en uppsättning hårddiskar till Microsoft för att importera eller exportera data direkt från datacentret. Mer information finns i [Använda tjänsten Microsoft Azure Import/Export för att överföra data till Blob Storage](../storage-import-export-service.md).

## <a name="azure-files"></a>Azure Files
Med [Azure Files](../files/storage-files-introduction.md) kan du konfigurera nätverksfilresurser med hög tillgänglighet som kan nås via SMB-standardprotokollet (Server Message Block). Det innebär att flera virtuella datorer kan dela samma filer med både läs- och skrivbehörighet. Du kan också läsa filerna med hjälp av REST-gränssnittet eller klientbiblioteken för lagring.

En sak som skiljer Azure Files från filer på en företagsfilresurs är att du kan komma åt filerna var som helst i världen med en URL som pekar på filen och som innehåller en SAS-token (signatur för delad åtkomst). Du kan generera SAS-token, som ger specifik åtkomst till en privat resurs under en viss tidsperiod.

Filresurser kan användas för många vanliga scenarier:

* Många lokala program använder filresurser. Den här funktionen gör det enklare att migrera program som delar data till Azure. Om du monterar filresursen med samma enhetsbeteckning som det lokala programmet använder, bör den del av programmet som använder filresursen fungera med minimala, om några, ändringar.

* Konfigurationsfiler kan lagras på en filresurs och nås från flera virtuella datorer. Verktyg och hjälpmedel som används av flera utvecklare i ett team kan lagras på en filresurs, så att alla enkelt kan hitta dem och för att säkerställa att alla använder samma version.

* Diagnostikloggar, mätvärden och kraschdumpar är bara tre exempel på data som kan skrivas till en filresurs och bearbetas eller analyseras senare.

För närvarande stöds inte Active Directory-baserad autentisering och åtkomstkontrollistor (ACL:er). Detta kommer dock att implementeras i en framtida utgåva. Autentiseringsuppgifterna för lagringskontot används för att tillhandahålla autentisering för åtkomst till filresursen. Det betyder att alla som har resursen monterad har fullständig skriv- och läsåtkomst till resursen.

## <a name="queue-storage"></a>Queue Storage

Azure-kötjänsten används för att lagra och hämta meddelanden. Kömeddelanden kan vara upp till 64 kB och en kö kan innehålla miljontals meddelanden. Köer används vanligtvis för att lagra listor med meddelanden som ska bearbetas asynkront.

Anta exempelvis att du vill att kunderna ska kunna ladda upp bilder och du vill skapa miniatyrer för varje bild. Du kan låta kunden vänta på att du skapar miniatyrerna medan bilderna laddas upp. Ett alternativ är att använda en kö. När kunden är klar med uppladdningen skrivs ett meddelande till kön. Sedan hämtar en Azure-funktion meddelandet från kön och skapar miniatyrerna. Alla delar av bearbetningen kan skalas separat, vilket ger dig större kontroll när du anpassar den för din användning.

## <a name="table-storage"></a>Table Storage

Azure Table Storage är nu en del av Azure Cosmos DB. Du hittar dokumentationen till Azure Table Storage i [Översikt över Azure Table Storage](../../cosmos-db/table-storage-overview.md). Utöver den befintliga Azure Table Storage-tjänsten finns ett nytt tabell-API för Azure Cosmos DB som tillhandahåller genomströmningsoptimerade tabeller, global distribution och automatiska sekundärindex. Om du vill veta mer om och prova den nya premiumtjänsten går du till avsnittet [Tabell-API för Azure Cosmos DB](https://aka.ms/premiumtables).

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

Det finns några grundläggande typer av kryptering för Storage-tjänsterna.

### <a name="encryption-at-rest"></a>Vilande kryptering

Du kan aktivera SSE (Storage Service Encryption) för Files-tjänsten (förhandsversion) eller Blob Service för ett Azure-lagringskonto. Om det aktiveras krypteras alla data som skrivs till den specifika tjänsten innan de skrivs. När du läser dessa data dekrypteras de innan de returneras.

### <a name="client-side-encryption"></a>Kryptering av klientsidan

Lagringsklientbiblioteken har metoder som du kan anropa för att programmässigt kryptera data innan de skickas via kabeln från klienten till Azure. De lagras krypterade, vilket innebär att de också är krypterade i vila. När du läser data dekrypteras informationen när du har fått den.

### <a name="encryption-in-transit-with-azure-file-shares"></a>Kryptering under överföring med Azure-filresurser

Mer information om signaturer för delad åtkomst finns i [Använda signaturer för delad åtkomst (SAS)](../storage-dotnet-shared-access-signature-part-1.md). Mer information om säker åtkomst till ditt lagringskonto finns i [Hantera anonym läsbehörighet till behållare och blobbar](../blobs/storage-manage-access-to-resources.md) och [Autentisering för Azure Storage-tjänster](https://msdn.microsoft.com/library/azure/dd179428.aspx).

Mer information om hur du skyddar ditt lagringskonto och kryptering finns i [säkerhetsguiden för Azure Storage](storage-security-guide.md).

## <a name="replication"></a>Replikering

För att säkerställa att dina data är beständiga kan Azure Storage lagra (och hantera) flera kopior av dina data. Det kallas replikering, eller ibland redundans. När du konfigurerar ditt lagringskonto väljer du en replikeringstyp. I de flesta fall kan den här inställningen ändras efter att lagringskontot har konfigurerats.

Alla lagringskonton har **lokalt redundant lagring (LRS)** som är utformat för att tillhandahålla minst 99,999999999 % (elva nior) objektshållbarhet under ett givet år. Det innebär att flera kopior av dina data hanteras av Azure Storage i det datacenter som angavs när lagringskontot konfigurerades. När ändringar har genomförts uppdateras alla kopior innan information om att åtgärden lyckats returneras. Det innebär att alla kopior alltid är synkroniserade. Dessutom finns kopiorna i separata feldomäner och uppgraderingsdomäner, vilket innebär att dina data är tillgängliga även om en lagringsnod med dina data råkar ut för fel eller kopplas från för att uppdateras.

**Lokalt redundant lagring (LRS)**

Som redan nämnts ovan innebär LRS att du får flera kopior av dina data i ett enda datacenter. Det hanterar problemet med att data blir otillgängliga om en lagringsnod råkar ut för ett fel eller kopplas från för att uppdateras, men inte om ett helt datacenter skulle bli otillgängligt.

**Zonredundant lagring (ZRS)**

Zonredundant lagring (ZRS) är utformat för att ge minst 99,9999999999 % (tolv nior) objektshållbarhet under ett givet år genom att både lokala kopior och en separat uppsättning kopior av dina data sparas. Den andra uppsättningen med kopior replikeras asynkront mellan datacenter i en eller två regioner. Observera att ZRS endast är tillgängligt för blockblobbar i allmänna lagringskonton. Och när du har skapat ditt lagringskonto och valt ZRS kan du inte konvertera det för att använda en annan typ av replikering eller tvärtom.

ZRS-konton ger högre hållbarhet än LRS, men ZRS-konton har inte mått- eller loggningsfunktioner.

**Geo-redundant lagring (GRS)**

Geo-redundant lagring (GRS) är utformat för att ge 99,99999999999999 % (16 nior) objektshållbarhet under ett givet år genom att lokala kopior av dina data lagras i en primär region, samtidigt som en annan uppsättning kopior lagras i en sekundär region hundratals mil från den primära regionen. Om det uppstår ett fel i den primära regionen kommer Azure Storage att redundansväxla till den sekundära regionen.

**Geo-redundant lagring med läsbehörighet (RA-GRS)**

Geo-redundant lagring med läsbehörighet är precis som GRS förutom att du får läsbehörighet till data på den sekundära platsen. Om det primära datacentret tillfälligt blir otillgängligt kan du fortsätta att läsa data från den sekundära platsen. Det kan vara mycket användbart. Du kan till exempel ha en webbapp som ändrar till skrivskyddat läge och pekar på den sekundära kopian, vilket ger viss åtkomst även om uppdateringar inte är tillgängliga.

> [!IMPORTANT]
> Du kan ändra hur dina data replikeras när ditt lagringskonto har skapats, såvida du inte valde ZRS när du skapade kontot. Observera dock att det kan utgå ytterligare engångskostnader för dataöverföring om du växlar från LRS till GRS eller RA-GRS.
>

Mer information om replikering finns i [Azure Storage-replikering](storage-redundancy.md).

Information om haveriberedskap finns i [Vad du gör om ett avbrott i Azure Storage inträffar?](storage-disaster-recovery-guidance.md).

Ett exempel på hur du utnyttjar RA-GRS-lagring för att säkerställa hög tillgänglighet finns i [Utforma högtillgängliga program med hjälp av RA-GRS](storage-designing-ha-apps-with-ragrs.md).

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

### <a name="azure-storage-data-services"></a>Azure Storage Data Services
* [REST-API för Storage Services](/rest/api/storageservices/)
* [Storage-klientbibliotek för .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Storage-klientbibliotek för C++](https://github.com/Azure/azure-storage-cpp)
* [Storage-klientbibliotek för Java/Android](https://azure.microsoft.com/develop/java/)
* [Storage-klientbibliotek för Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Storage-klientbibliotek för PHP](https://azure.microsoft.com/develop/php/)
* [Storage-klientbibliotek för Python](https://azure.microsoft.com/develop/python/)
* [Storage-klientbibliotek för Ruby](https://azure.microsoft.com/develop/ruby/)
* [Storage-cmdletar för PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)
* [Storage-kommandon för CLI 2.0](/cli/azure/storage)

## <a name="next-steps"></a>Nästa steg

* [Mer information om Blob Storage](../blobs/storage-blobs-introduction.md)
* [Mer information om File Storage](../storage-files-introduction.md)
* [Mer information om Queue Storage](../queues/storage-queues-introduction.md)

Kom igång med Azure Storage genom att läsa [Skapa ett lagringskonto](storage-quickstart-create-account.md).

<!-- FIGURE OUT WHAT TO DO WITH ALL THESE LINKS.

Azure Storage resources can be accessed by any language that can make HTTP/HTTPS requests. Additionally, Azure Storage offers programming libraries for several popular languages. These libraries simplify many aspects of working with Azure Storage by handling details such as synchronous and asynchronous invocation, batching of operations, exception management, automatic retries, operational behavior and so forth. Libraries are currently available for the following languages and platforms, with others in the pipeline:

### Azure Storage data services
* [Storage Services REST API](https://docs.microsoft.com/rest/api/storageservices/)
* [Storage Client Library for .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp)
* [Storage Client Library for Java/Android](https://azure.microsoft.com/develop/java/)
* [Storage Client Library for Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Storage Client Library for PHP](https://azure.microsoft.com/develop/php/)
* [Storage Client Library for Python](https://azure.microsoft.com/develop/python/)
* [Storage Client Library for Ruby](https://azure.microsoft.com/develop/ruby/)
* [Storage Cmdlets for PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)

### Azure Storage management services
* [Storage Resource Provider REST API Reference](/rest/api/storagerp/)
* [Storage Resource Provider Client Library for .NET](/dotnet/api/microsoft.azure.management.storage)
* [Storage Resource Provider Cmdlets for PowerShell 1.0](/powershell/module/azure.storage)
* [Storage Service Management REST API (Classic)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### Azure Storage data movement services
* [Storage Import/Export Service REST API](../storage-import-export-service.md)
* [Storage Data Movement Client Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### Tools and utilities
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is a free, standalone app from Microsoft that enables you to work visually with Azure Storage data on Windows, macOS, and Linux.
* [Azure Storage Client Tools](../storage-explorers.md)
* [Azure SDKs and Tools](https://azure.microsoft.com/tools/)
* [Azure Storage Emulator](http://www.microsoft.com/download/details.aspx?id=43709)
* [Azure PowerShell](/powershell/azure/overview)
* [AzCopy Command-Line Utility](http://aka.ms/downloadazcopy)

## Next steps
To learn more about Azure Storage, explore these resources:

### Documentation
* [Azure Storage Documentation](https://azure.microsoft.com/documentation/services/storage/)
* [Create a storage account](../storage-create-storage-account.md)

-->

### <a name="for-administrators"></a>För administratörer
* [Använd Azure PowerShell med Azure Storage](storage-powershell-guide-full.md)
* [Använda Azure CLI med Azure Storage](../storage-azure-cli.md)

### <a name="for-net-developers"></a>För .NET-utvecklare
* [Komma igång med Azure Blob Storage med hjälp av .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Utveckla för Azure Files med .NET](../files/storage-dotnet-how-to-use-files.md)
* [Komma igång med Azure Table Storage med hjälp av .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Komma igång med Azure Queue Storage med hjälp av .NET](../storage-dotnet-how-to-use-queues.md)

### <a name="for-javaandroid-developers"></a>För Java-/Android-utvecklare
* [Använda Blob Storage från Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Utveckla för Azure Files med Java](../files/storage-java-how-to-use-file-storage.md)
* [Använda Table Storage från Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Använda Queue Storage från Java](../storage-java-how-to-use-queue-storage.md)

### <a name="for-nodejs-developers"></a>För Node.js-utvecklare
* [Använda Blob Storage från Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Använda Table Storage från Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Använda Queue Storage från Node.js](../storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>För PHP-utvecklare
* [Använda Blob Storage från PHP](../blobs/storage-php-how-to-use-blobs.md)
* [Använda Table Storage från PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Använda Queue Storage från PHP](../storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>För Ruby-utvecklare
* [Använda Blob Storage från Ruby](../blobs/storage-ruby-how-to-use-blob-storage.md)
* [Använda Table Storage från Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)
* [Använda Queue Storage från Ruby](../storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>För Python-utvecklare
* [Använda Blob Storage från Python](../blobs/storage-python-how-to-use-blob-storage.md)
* [Utveckla för Azure Files med Python](../files/storage-python-how-to-use-file-storage.md)
* [Använda Table Storage från Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Använda Queue Storage från Python](../storage-python-how-to-use-queue-storage.md)
