---
title: Introduktion till Storage | Microsoft Docs
description: "En översikt över Azure Storage, Microsofts onlinelagring i molnet. Lär dig hur du använder den bästa tillgängliga lösningen för molnlagring i dina program."
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: a4a1bc58-ea14-4bf5-b040-f85114edc1f1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 931503f56b32ce9d1b11283dff7224d7e2f015ae
ms.openlocfilehash: 40ab7632f47de4d4eef277f4c4071ce2d4de1eed


---
# <a name="introduction-to-microsoft-azure-storage"></a>Introduktion till Microsoft Azure Storage
## <a name="overview"></a>Översikt
Azure Storage är molnlagringslösningen för moderna program som kräver hållbarhet, tillgänglighet och skalbarhet för att uppfylla kundernas behov. I den här artikeln kan utvecklare, IT-proffs och beslutsfattare lära sig mer om:

* Vad Azure Storage är och hur du kan dra nytta av det i dina moln-, mobil-, server- och skrivbordsprogram.
* Vilka typer av data som du kan lagra med Azure Storage-tjänsterna: blobbdata (objekt), NoSQL-tabelldata, kömeddelanden och filresurser.
* Hur åtkomsten till dina data i Azure Storage hanteras.
* Hur dina Azure Storage-data blir hållbara genom redundans och replikering.
* Vart du går för att skapa ditt första Azure Storage-program.

Information om hur du snabbt kommer igång med Azure Storage finns i [Komma igång med Azure Storage på fem minuter](storage-getting-started-guide.md).

Mer information om verktyg, bibliotek och andra resurser för att arbeta med Azure Storage finns i [Nästa steg](#next-steps) nedan.

## <a name="what-is-azure-storage"></a>Vad är Azure Storage?
Molntjänster möjliggör nya scenarier för program som kräver datalagring med hög tillgänglighet, skalbarhet och hållbarhet – vilket är exakt varför Microsoft utvecklade Azure Storage. Förutom att göra det möjligt för utvecklare att bygga storskaliga program som ger stöd för nya scenarier fungerar Azure Storage som lagringsgrunden för Azure Virtual Machines, ytterligare ett bevis på dess stabilitet.

Azure Storage är extremt skalbart. Du kan lagra och bearbeta många hundra terabyte data för att ge stöd för stordatascenarier som krävs av vetenskapsprogram, medieprogram och program för ekonomisk analys. Men du kan också lagra små mängder data som behövs för en liten företagswebbplats. Oavsett dina behov så betalar du bara för de data du lagrar. Azure Storage lagrar för närvarande flera biljoner unika kundobjekt och hanterar många miljoner förfrågningar per sekund i genomsnitt.

Azure Storage är elastiskt. Det betyder att du kan skapa program för en stor global målgrupp och skala programmen efter behov – både vad gäller mängden lagrade data och antalet förfrågningar som görs mot dem. Du betalar endast för det du använder, och bara när du använder det.

Azure Storage använder ett system för automatisk partitionering som automatiskt belastningsutjämnar dina data baserat på trafiken. Det innebär att när kraven på ditt program ökar så tilldelar Azure Storage automatiskt lämpliga resurser för att uppfylla dem.

Azure Storage kan nås överallt i världen, från alla slags program, oavsett om de körs i molnet, på skrivbordet, på en lokal server eller på en mobilenhet eller surfplatta. Du kan använda Azure Storage i mobila scenarier där programmet lagrar en delmängd data på enheten och synkroniserar dem med en fullständig uppsättning data som lagras i molnet.

Azure Storage har stöd för klienter med en rad olika operativsystem (inklusive Windows och Linux) och många olika programmeringsspråk (inklusive .NET, Java, Node.js, Python, Ruby, PHP och C++ samt programmeringsspråk för mobilappar) för en smidig utveckling. Azure Storage exponerar även dataresurser via enkla REST-API:er, som är tillgängliga för alla klienter som kan skicka och ta emot data via HTTP/HTTPS.

Azure Premium Storage ger stöd för diskar med låg fördröjning och höga prestanda i I/O-intensiva arbetsbelastningar som körs på Azure Virtual Machines. Med Azure Premium Storage kan du lägga till flera beständiga datadiskar till en virtuell dator och konfigurera dem så att de uppfyller dina prestandakrav. Varje datadisk backas upp av en SSD-disk i Premium Storage för maximala I/O-prestanda. Mer information finns i [Premium Storage: Lagring med höga prestanda för arbetsbelastningar på virtuella datorer i Azure](storage-premium-storage.md).

## <a name="introducing-the-azure-storage-services"></a>Introduktion till Azure Storage-tjänsterna
Azure Storage tillhandahåller följande fyra tjänster: Blob Storage, Table Storage, Queue Storage och File Storage.

* Blob Storage lagrar ostrukturerade objektdata. En blobb kan bestå av vilka slags textdata eller binära data som helst, till exempel ett dokument, en mediefil eller ett installationsprogram. Blob Storage kallas även för objektlagring.
* Table Storage lagrar strukturerade datauppsättningar. Table Storage är en nyckel- och attributdatabas för NoSQL som möjliggör snabb utveckling och snabb tillgång till stora mängder data.
* Queue Storage erbjuder tillförlitlig meddelandehantering för arbetsflödesbearbetning och för kommunikation mellan komponenter i molntjänster.
* File Storage erbjuder delad lagring för äldre program som använder SMB-standardprotokollet. Virtuella Azure-datorer och molntjänster kan dela fildata mellan programkomponenter via monterade resurser, och lokala program kan komma åt fildata på en resurs via REST-API:et för filtjänsten.

Ett Azure Storage-konto är ett säkert konto som ger dig tillgång till tjänster i Azure Storage. Ditt lagringskonto tillhandahåller den unika namnrymden för dina lagringsresurser. Bilden nedan visar relationerna mellan Azure Storage-resurser i ett lagringskonto:

![Azure Storage-resurser](./media/storage-introduction/storage-concepts.png)

[!INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

[!INCLUDE [storage-versions-include](../../includes/storage-versions-include.md)]

## <a name="blob-storage"></a>Blob Storage
För användare som behöver lagra stora mängder ostrukturerade objektdata i molnet erbjuder Blob Storage en kostnadseffektiv och skalbar lösning. Du kan använda Blob Storage för att lagra innehåll som:

* Dokument
* Sociala data, till exempel foton, videor, musik och bloggar
* Säkerhetskopior av filer, datorer, databaser och enheter
* Bilder och text för webbprogram
* Konfigurationsdata för molnprogram
* Stordata, till exempel loggar och andra stora datauppsättningar

Blobbar ordnas i behållare. Behållare är också ett praktiskt sätt att tilldela säkerhetsprinciper till grupper med objekt. Ett lagringskonto kan innehålla ett obestämt antal behållare och en behållare kan innehålla ett obestämt antal blobbar, upp till lagringskontots kapacitetsgräns på 500 TB.

Blob Storage erbjuder tre typer av blobbar: blockblobbar, tilläggsblobbar och sidblobbar (diskar).

* Blockblobbar är optimerade för direktuppspelning och lagring av molnobjekt och är ett bra alternativ för att lagra dokument, mediefiler, säkerhetskopior osv.
* Tilläggsblobbar påminner om blockblobbar, men är optimerade för tilläggsåtgärder. En tilläggsblobb kan bara uppdateras genom att ett nytt block läggs till i slutet. Tilläggsblobbar är praktiska i scenarier som loggning, där nya data bara behöver skrivas till slutet av blobben.
* Sidblobbar är optimerade för att representera IaaS-diskar och har bra stöd för slumpmässiga skrivningar. De kan vara upp till 1 TB stora. En nätverksansluten IaaS-disk för en virtuell Azure-dator är en VHD som lagras som en sidblobb.

För mycket stora datamängder där nätverksbegränsningar gör det orealistiskt att överföra eller hämta data till Blob Storage via kabel kan du skicka en hårdisk till Microsoft för att importera eller exportera data direkt från datacentret. Mer information finns i [Använda tjänsten Microsoft Azure Import/Export för att överföra data till Blob Storage](storage-import-export-service.md).

## <a name="table-storage"></a>Table Storage
Moderna program behöver ofta datalager med bättre skalbarhet och flexibilitet än tidigare versioner av den programvara som krävs. Table Storage erbjuder mycket skalbar lagring med hög tillgänglighet, så att ditt program kan anpassas automatiskt efter användarens behov. Table Storage är Microsofts nyckel- och attributdatabas för NoSQL. Tjänsten har en schemalös design, vilket skiljer den från traditionella relationsdatabaser. Ett schemalöst datalager gör det enkelt att anpassa data i takt med att programmets behov förändras. Table Storage är enkelt att använda, vilket gör det lätt för utvecklare att snabbt skapa program. Åtkomsten till data är snabb och kostnadseffektiv för alla typer av program.  Kostnaden för Table Storage är normalt sett betydligt lägre än för motsvarande volymer med traditionell SQL.

Table Storage är en nyckel- och attributdatabas, vilket innebär att varje värde i en tabell lagras med ett typbestämt egenskapsnamn. Egenskapsnamnet kan användas för att filtrera och ange urvalskriterier. En uppsättning egenskaper och deras värden utgör en entitet. Eftersom Table Storage är schemalöst kan två entiteter i samma tabell innehålla olika samlingar egenskaper, och dessa egenskaper kan höra till olika typer.

Du kan använda Table Storage för att lagra flexibla datauppsättningar, till exempel användardata för webbprogram, adressböcker, enhetsinformation och andra typer av metadata som din tjänst kräver.  Du kan lagra valfritt antal enheter i en tabell, och ett lagringskonto kan innehålla valfritt antal tabeller, upp till lagringskontots kapacitetsgräns.

Precis som med blobbar och köer kan utvecklare hantera och komma åt Table Storage med hjälp av REST-standardprotokoll, men Table Storage stöder också en delmängd av OData-protokollet, vilket förenklar avancerade frågefunktioner och ger stöd för JSON- och AtomPub-format (XML-baserade).

NoSQL-databaser som Table Storage erbjuder ett populärt alternativ till traditionella relationsdatabaser för dagens Internetbaserade program.

## <a name="queue-storage"></a>Queue Storage
När program utformas för skalning är programkomponenterna ofta fristående, så att de kan skalas oberoende av varandra. Queue Storage är en tillförlitlig meddelandelösning för asynkron kommunikation mellan programkomponenter, oavsett om de körs i molnet, på skrivbordet, på en lokal server eller på en mobil enhet. Queue Storage har också stöd för hantering av asynkrona åtgärder och utveckling av processarbetsflöden.

Ett lagringskonto kan innehålla valfritt antal köer. En kö kan innehålla valfritt antal meddelanden, upp till lagringskontots kapacitetsgräns. Enskilda meddelanden kan vara upp till 64 kB stora.

## <a name="file-storage"></a>File Storage
Azure File Storage tillhandahåller molnbaserade SMB-filresurser så att du snabbt och utan kostsamma omskrivningar kan migrera äldre program som är beroende av filresurser till Azure. Med Azure File Storage kan program som körs på virtuella Azure-datorer eller molntjänster montera en filresurs i molnet, precis som ett skrivbordsprogram monterar en typisk SMB-resurs. Ett obegränsat antal programkomponenter kan sedan montera och komma åt fillagringsresursen samtidigt.

Eftersom en fillagringsresurs är en vanlig SMB-filresurs kan program som körs i Azure komma åt data i resursen via filsystemets I/O-API:er. Utvecklare kan därför utnyttja befintlig kod och erfarenhet för att migrera befintliga program. IT-proffs kan använda PowerShell-cmdlets för att skapa, montera och hantera fillagringsresurser som en del av administrationen av Azure-program.

Precis som de andra Azure-lagringstjänsterna exponerar File Storage ett REST-API för åtkomst till data i en resurs. Lokala program kan anropa REST-API:et för File Storage för att komma åt data i en filresurs. På så sätt kan ett företag välja att migrera vissa äldre program till Azure och fortsätta att köra andra inifrån den egna organisationen. Observera att det bara går att montera en filresurs för program som körs i Azure. Ett lokalt program kan bara komma åt filresursen via REST-API:et.

Distribuerade program kan också använda File Storage för att lagra och dela användbara programdata och utvecklings- och testningsverktyg. Ett program kan till exempel lagra konfigurationsfiler och diagnostikdata som loggar, mätvärden och kraschdumpar på en File Storage-resurs så att de blir tillgängliga för flera virtuella datorer eller roller. Utvecklare och administratörer kan lagra verktyg som de behöver för att skapa och hantera ett program på en File Storage-resurs som är tillgänglig för alla komponenter i stället för att installera dem på varje virtuell dator eller rollinstans.

## <a name="access-to-blob-table-queue-and-file-resources"></a>Åtkomst till blobb-, tabell-, kö- och filresurser
Som standard kan endast lagringskontoägaren komma åt resurser i lagringskontot. För att skydda dina data måste varje begäran som görs mot resurser på ditt konto autentiseras. Autentiseringen använder en modell med delade nycklar. Blobbar kan också konfigureras att stödja anonym autentisering.

Ditt lagringskonto tilldelas två privata åtkomstnycklar när det skapas som används för autentisering. Eftersom det finns två nycklar förblir programmet tillgängligt när du regelbundet återskapar nycklarna, vilket är en vanlig säkerhetspraxis i samband med nyckelhantering.

Om du behöver ge användare kontrollerad åtkomst till dina lagringsresurser kan du skapa en signatur för delad åtkomst. En signatur för delad åtkomst (SAS) är en token som kan läggas till i en URL och som ger delegerad åtkomst till en lagringsresurs. Alla som har tillgång till denna token kan komma åt resursen som den pekar på med de behörigheter som den beviljar, under den tidsperiod som den är giltig. Från och med version 2015-04-05 stöder Azure Storage två typer av signaturer för delad åtkomst: tjänst-SAS och konto-SAS.

En tjänst-SAS delegerar åtkomst till en resurs i en av lagringstjänsterna: blobb-, kö-, tabell- eller filtjänsten.

En konto-SAS delegerar åtkomst till resurser i en eller flera av lagringstjänsterna. Du kan delegera åtkomst till åtgärder på tjänstnivå som inte är tillgängliga med en tjänst-SAS. Du kan också delegera åtkomst till läs-, skriv- och borttagningsåtgärder i blobbbehållare, tabeller, köer och filresurser som inte tillåts med en tjänst-SAS.

Slutligen kan du även ange att en behållare och dess blobbar, eller en specifik blobb, ska vara offentligt tillgängliga. När du anger att en behållare eller blobb är offentlig kan alla läsa den anonymt; ingen autentisering krävs.  Offentliga behållare och blobbar är användbara för att exponera resurser, till exempel media och dokument som finns på webbplatser.  Du kan minska nätverksfördröjningen för en global publik genom att cachelagra blobbdata som används av webbplatser med hjälp av Azure CDN.

Mer information om signaturer för delad åtkomst finns i [Använda signaturer för delad åtkomst (SAS)](storage-dotnet-shared-access-signature-part-1.md). Mer information om säker åtkomst till ditt lagringskonto finns i [Hantera anonym läsbehörighet till behållare och blobbar](storage-manage-access-to-resources.md) och [Autentisering för Azure Storage-tjänster](https://msdn.microsoft.com/library/azure/dd179428.aspx).

## <a name="replication-for-durability-and-high-availability"></a>Replikering för hållbarhet och hög tillgänglighet
Data i ditt Microsoft Azure-lagringskonto replikeras alltid för att säkerställa hållbarhet och hög tillgänglighet. Replikeringen kopierar dina data inom samma datacenter eller till en andra datacenter, beroende på vilket replikeringsalternativ du väljer. Replikering skyddar dina data och bevarar dina programs drifttid vid tillfälliga maskinvarufel. Om dina data replikeras till ett andra datacenter, skyddar detta även dina data mot ett oåterkalleligt fel på den primära platsen.

Replikeringen garanterar att ditt lagringskonto uppfyller [Servicenivåavtal (SLA) för lagring](https://azure.microsoft.com/support/legal/sla/storage/) även vid fel. Visa SLA för information om Azure Storage-garantier för hållbarhet och tillgänglighet.

När du skapar ett lagringskonto kan du välja något av följande replikeringsalternativ:

* **Lokalt redundant lagring (LRS).** Med lokalt redundant lagring underhålls tre kopior av dina data. LRS replikeras tre gånger på ett datacenter i en region. LRS skyddar dina data mot normala maskinvarufel, men inte mot fel på ett enskilt datacenter.

    LRS erbjuds med rabatt. För maximal hållbarhet rekommenderar vi att du använder geo-redundant lagring, som beskrivs nedan.
* **Zonredundant lagring (ZRS).** Med zonredundant lagring underhålls tre kopior av dina data. ZRS replikeras tre gånger mellan två eller tre anläggningar, antingen inom en enda region eller mellan två regioner, vilket ger högre hållbarhet än LRS. ZRS garanterar att dina data skyddas inom en enskild region.

    ZRS ger en högre nivå av hållbarhet än LRS. För maximal hållbarhet rekommenderar vi dock att du använder geo-redundant lagring, som beskrivs nedan.

  > [!NOTE]
  > ZRS är för närvarande endast tillgängligt för blockblobbar, och stöds endast för version 2014-02-14 och senare.
  >
  > När du har skapat ditt lagringskonto och valt ZRS kan du inte konvertera det för att använda en annan typ av replikering eller tvärtom.
  >
  >
* **Geo-redundant lagring (GRS)**. Med GRS underhålls sex kopior av dina data. Med GRS replikeras dina data tre gånger i den primära regionen och dessutom tre gånger i en sekundär region hundratals mil bort från den primära regionen, vilket ger den högsta nivån av hållbarhet. Om det uppstår ett fel i den primära regionen kommer Azure Storage att redundansväxla till den sekundära regionen. GRS garanterar att dina data skyddas i två olika områden.

    Information om primära och sekundära kopplingar efter region finns i [Azure-regioner](https://azure.microsoft.com/regions/).
* **Geo-redundant lagring med läsbehörighet (RA-GRS)**. Geo-redundant lagring med läsbehörighet replikerar data till en sekundär geografisk plats och ger även läsåtkomst till dina data på den sekundära platsen. Med geo-redundant lagring med läsbehörighet kan du komma åt dina data från antingen den primära eller sekundära platsen om en av platserna skulle bli otillgänglig. Geo-redundant lagring med läsbehörighet är standardalternativet för ditt lagringskonto som standard när du skapar det.

  > [!IMPORTANT]
  > Du kan ändra hur dina data replikeras när ditt lagringskonto har skapats, såvida du inte valde ZRS när du skapade kontot. Observera dock att det kan utgå ytterligare engångskostnader för dataöverföring om du växlar från LRS till GRS eller RA-GRS.
  >
  >

Mer information om lagringsreplikeringsalternativ finns i [Azure Storage-replikering](storage-redundancy.md).

Information om priser för replikering av lagringskonton finns i [Priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Mer information om vilka tjänster som är tillgängliga i varje region finns i [Azure-regioner](https://azure.microsoft.com/regions/#services).

Arkitekturinformation om hållbarhet med Azure Storage finns i dokumentet [SOSP Paper - Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

## <a name="transferring-data-to-and-from-azure-storage"></a>Överföra data till och från Azure Storage
Du kan använda kommandoradsverktyget AzCopy för att kopiera blobb-, fil- och tabelldata inom ditt lagringskonto eller mellan lagringskonton. Mer information finns i [Överföra data med kommandoradsverktyget AzCopy](storage-use-azcopy.md).

AzCopy är byggt ovanpå [biblioteket för Azure-dataflyttningar](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), som för närvarande är tillgängligt som en förhandsversion.

Med tjänsten Azure Import/Export kan du importera och exportera blobbdata till eller från ditt lagringskonto via en hårddisk som du skickar till Azure-datacentret. Mer information om tjänsten Import/Export finns i [Använda tjänsten Microsoft Azure Import/Export för att överföra data till Blob Storage](storage-import-export-service.md).

## <a name="pricing"></a>Priser
[!INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

## <a name="storage-apis-libraries-and-tools"></a>API:er, bibliotek och verktyg för Azure Storage
Azure Storage-resurser kan nås med alla språk som kan skicka HTTP/HTTPS-förfrågningar. Dessutom erbjuder Azure Storage programmeringsbibliotek för flera populära språk. Dessa bibliotek förenklar många aspekter av arbetet med Azure Storage genom att hantera information som till exempel synkrona och asynkrona anrop, massbearbetning av åtgärder, undantagshantering, automatiska omförsök, funktionsbeteenden och så vidare. Bibliotek är för närvarande tillgängliga för följande språk och plattformar, med andra i pipeline:

### <a name="azure-storage-data-services"></a>Azure Storage Data Services
* [REST-API för Storage Services](http://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Storage-klientbibliotek för .NET, Windows Phone och Windows Runtime](https://www.nuget.org/packages/WindowsAzure.Storage/)
* [Storage-klientbibliotek för C++](https://github.com/Azure/azure-storage-cpp)
* [Storage-klientbibliotek för Java/Android](/develop/java/)
* [Storage-klientbibliotek för Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Storage-klientbibliotek för PHP](/develop/php/)
* [Storage-klientbibliotek för Ruby](/develop/ruby/)
* [Storage-klientbibliotek för Python](/develop/python/)
* [Storage-cmdletar för PowerShell 1.0](https://msdn.microsoft.com/library/azure/mt269418.aspx)

### <a name="azure-storage-management-services"></a>Azure Storage Management Services
* [Referens för REST-API för Storage Resource Provider](https://msdn.microsoft.com/library/azure/mt163683.aspx)
* [Klientbibliotek för Storage Resource Provider för .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx)
* [Cmdlets för Storage Resource Provider för PowerShell 1.0](https://msdn.microsoft.com/library/azure/mt607151.aspx)
* [REST-API för Service Management för Storage (klassisk)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-services"></a>Azure Storage Data Movement Services
* [REST-API för Storage Import/Export Service](https://msdn.microsoft.com/library/azure/dn529096.aspx)
* [Klientbibliotek för Storage Data Movement för .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### <a name="tools-and-utilities"></a>Verktyg och hjälpmedel
* [Azure Storage Explorer](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Azure Storage-klientverktyg](storage-explorers.md)
* [SDK:er och verktyg för Azure](https://azure.microsoft.com/tools/)
* [Azure Storage-emulator](http://www.microsoft.com/download/details.aspx?id=43709)
* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Kommandoradsverktyget AzCopy](http://aka.ms/downloadazcopy)

## <a name="next-steps"></a>Nästa steg
Utforska gärna dessa resurser om du vill veta mer om Azure Storage:

### <a name="documentation"></a>Dokumentation
* [Azure Storage-dokumentation](https://azure.microsoft.com/documentation/services/storage/)

### <a name="for-administrators"></a>För administratörer
* [Använd Azure PowerShell med Azure Storage](storage-powershell-guide-full.md)
* [Använda Azure CLI med Azure Storage](storage-azure-cli.md)

### <a name="for-net-developers"></a>För .NET-utvecklare
* [Komma igång med Azure Blob Storage med hjälp av .NET](storage-dotnet-how-to-use-blobs.md)
* [Komma igång med Azure Table Storage med hjälp av .NET](storage-dotnet-how-to-use-tables.md)
* [Komma igång med Azure Queue Storage med hjälp av .NET](storage-dotnet-how-to-use-queues.md)
* [Komma igång med Azure File Storage i Windows](storage-dotnet-how-to-use-files.md)

### <a name="for-javaandroid-developers"></a>För Java/Android-utvecklare
* [Använda Blob Storage från Java](storage-java-how-to-use-blob-storage.md)
* [Använda Table Storage från Java](storage-java-how-to-use-table-storage.md)
* [Använda Queue Storage från Java](storage-java-how-to-use-queue-storage.md)
* [Använda File Storage från Java](storage-java-how-to-use-file-storage.md)

### <a name="for-nodejs-developers"></a>För Node.js-utvecklare
* [Använda Blob Storage från Node.js](storage-nodejs-how-to-use-blob-storage.md)
* [Använda Table Storage från Node.js](storage-nodejs-how-to-use-table-storage.md)
* [Använda Queue Storage från Node.js](storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>För PHP-utvecklare
* [Använda Blob Storage från PHP](storage-php-how-to-use-blobs.md)
* [Använda Table Storage från PHP](storage-php-how-to-use-table-storage.md)
* [Använda Queue Storage från PHP](storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>För Ruby-utvecklare
* [Använda Blob Storage från Ruby](storage-ruby-how-to-use-blob-storage.md)
* [Använda Table Storage från Ruby](storage-ruby-how-to-use-table-storage.md)
* [Använda Queue Storage från Ruby](storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>För Python-utvecklare
* [Använda Blob Storage från Python](storage-python-how-to-use-blob-storage.md)
* [Använda Table Storage från Python](storage-python-how-to-use-table-storage.md)
* [Använda Queue Storage från Python](storage-python-how-to-use-queue-storage.md)
* [Använda File Storage från Python](storage-python-how-to-use-file-storage.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa ett lagringskonto](storage-create-storage-account.md)
* [Komma igång med Azure Storage på fem minuter](storage-getting-started-guide.md)


<!--HONumber=Dec16_HO2-->


