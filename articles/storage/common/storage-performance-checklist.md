---
title: Check lista för Azure Storage prestanda och skalbarhet | Microsoft Docs
description: En check lista över beprövade metoder för användning med Azure Storage för att utveckla program som utförs.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e700cf04123bf02c1014aa418189221fbbb0b812
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2019
ms.locfileid: "71670917"
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>Check lista för Microsoft Azure Storage prestanda och skalbarhet

Sedan lanseringen av Microsoft Azure Storage Services har Microsoft utvecklat ett antal beprövade metoder för att använda dessa tjänster på ett effektivt sätt, och den här artikeln beskriver hur de är viktigast för dem i en check lista. Avsikten med den här artikeln är att hjälpa utvecklare att verifiera att de använder beprövade metoder med Azure Storage och för att hjälpa dem att identifiera andra beprövade metoder som de bör tänka på. Den här artikeln försöker inte ta upp alla möjliga optimeringar av prestanda och skalbarhet – den utesluter de som är små i deras inverkan eller som inte är allmänt tillämpliga. I den utsträckning som program beteendet kan förutsägas under designen, är det bra att komma ihåg detta för att undvika att modeller som kan köras i prestanda problem.  

Varje program utvecklare som använder Azure Storage bör ta tid att läsa den här artikeln och kontrol lera att deras program följer de metoder som anges nedan.  

## <a name="checklist"></a>Checklista

Den här artikeln ordnar beprövade metoder i följande grupper. Beprövade metoder som är tillämpliga för:  

* Alla Azure Storage-tjänster (blobbar, tabeller, köer och filer)
* Blobar
* Tabeller
* Köer  

| Klart | Område | Category | Fråga |
| --- | --- | --- | --- |
| &nbsp; | Alla tjänster |Skalbarhets mål |[Är ditt program utformat för att undvika att nå skalbarhets målen?](#subheading1) |
| &nbsp; | Alla tjänster |Skalbarhets mål |[Är namngivnings konventionen utformad för att möjliggöra bättre belastnings utjämning?](#subheading47) |
| &nbsp; | Alla tjänster |Nätverk |[Har klient sidan enheter tillräckligt med hög bandbredd och låg latens för att uppnå den prestanda som krävs?](#subheading2) |
| &nbsp; | Alla tjänster |Nätverk |[Har klient sidan enheter en hög tillräckligt stor kvalitets länk?](#subheading3) |
| &nbsp; | Alla tjänster |Nätverk |[Finns klient programmet "nära" lagrings kontot?](#subheading4) |
| &nbsp; | Alla tjänster |Innehållsdistribution |[Använder du ett CDN för innehålls distribution?](#subheading5) |
| &nbsp; | Alla tjänster |Direkt klient åtkomst |[Använder du SAS och CORS för att tillåta direkt åtkomst till lagring istället för proxy?](#subheading6) |
| &nbsp; | Alla tjänster |Cachelagring |[Är dina program cachelagrade data som används upprepade gånger och ändras sällan?](#subheading7) |
| &nbsp; | Alla tjänster |Cachelagring |[Är ditt program batch-uppdateringar (cachelagrar dem på klient sidan och laddar sedan upp i större mängder)?](#subheading8) |
| &nbsp; | Alla tjänster |.NET-konfiguration |[Har du konfigurerat att klienten ska använda ett tillräckligt antal samtidiga anslutningar?](#subheading9) |
| &nbsp; | Alla tjänster |.NET-konfiguration |[Har du konfigurerat .NET att använda ett tillräckligt antal trådar?](#subheading10) |
| &nbsp; | Alla tjänster |.NET-konfiguration |[Använder du .NET 4,5 eller senare, som har förbättrad skräp insamling?](#subheading11) |
| &nbsp; | Alla tjänster |Parallellitet |[Har du säkerställt att parallellitet är lämpligt så att du inte överbelastar dina klient funktioner eller skalbarhets målen?](#subheading12) |
| &nbsp; | Alla tjänster |Verktyg |[Använder du den senaste versionen av klient bibliotek och verktyg från Microsoft?](#subheading13) |
| &nbsp; | Alla tjänster |Antal försök |[Använder du en princip för en exponentiell backoff-återförsök för begränsning av fel och tids gränser?](#subheading14) |
| &nbsp; | Alla tjänster |Antal försök |[Kan programmet undvika nya försök för fel som inte kan återförsökas?](#subheading15) |
| &nbsp; | Blobar |Skalbarhets mål |[Har du ett stort antal klienter som har åtkomst till ett enda objekt samtidigt?](#subheading46) |
| &nbsp; | Blobar |Skalbarhets mål |[Ligger ditt program i bandbredds-eller drifts skalbarhets målet för en enskild BLOB?](#subheading16) |
| &nbsp; | Blobar |Kopiera blobbar |[Kopierar du blobbar på ett effektivt sätt?](#subheading17) |
| &nbsp; | Blobar |Kopiera blobbar |[Använder du AzCopy för Mass kopior av blobbar?](#subheading18) |
| &nbsp; | Blobar |Kopiera blobbar |[Använder du Azure import/export för att överföra stora mängder data?](#subheading19) |
| &nbsp; | Blobar |Använd metadata |[Lagrar du ofta använda metadata om blobbar i deras metadata?](#subheading20) |
| &nbsp; | Blobar |Laddar upp snabbt |[Kommer du att ladda upp block parallellt när du försöker ladda upp en BLOB snabbt?](#subheading21) |
| &nbsp; | Blobar |Laddar upp snabbt |[Kommer du att ladda upp blobar parallellt när du snabbt försöker ladda upp många blobbar?](#subheading22) |
| &nbsp; | Blobar |Rätt Blob-typ |[Använder du Page blobbar eller block-blobar när det är lämpligt?](#subheading23) |
| &nbsp; | Tabeller |Skalbarhets mål |[Närmar du dig skalbarhets målen för entiteter per sekund?](#subheading24) |
| &nbsp; | Tabeller |Konfiguration |[Använder du JSON för dina tabell förfrågningar?](#subheading25) |
| &nbsp; | Tabeller |Konfiguration |[Har du stängt av Nagle för att förbättra prestanda för små begär Anden?](#subheading26) |
| &nbsp; | Tabeller |Tabeller och partitioner |[Har du partitionerat dina data korrekt?](#subheading27) |
| &nbsp; | Tabeller |Varma partitioner |[Undviker du tilläggs mönster för tillägg och lägga?](#subheading28) |
| &nbsp; | Tabeller |Varma partitioner |[Är dina infogningar/uppdateringar spridda över flera partitioner?](#subheading29) |
| &nbsp; | Tabeller |Fråge omfång |[Har du utformat ditt schema för att tillåta att punkt frågor används i de flesta fall och tabell frågor som ska användas sparsamt?](#subheading30) |
| &nbsp; | Tabeller |Frågans densitet |[Söker dina frågor normalt bara igenom och returnerar rader som programmet kommer att använda?](#subheading31) |
| &nbsp; | Tabeller |Begränsa returnerade data |[Använder du filtrering för att undvika att returnera entiteter som inte behövs?](#subheading32) |
| &nbsp; | Tabeller |Begränsa returnerade data |[Använder du projektion för att undvika att returnera egenskaper som inte behövs?](#subheading33) |
| &nbsp; | Tabeller |Denormalisering |[Har du avnormaliserat dina data så att du undviker ineffektiva frågor eller flera Läs begär anden när du försöker hämta data?](#subheading34) |
| &nbsp; | Tabeller |Insert/Update/Delete |[Är du batch-begäranden som måste vara transaktionella eller som kan utföras samtidigt för att minska antalet turer?](#subheading35) |
| &nbsp; | Tabeller |Insert/Update/Delete |[Undviker du att hämta en entitet bara för att avgöra om du vill anropa INSERT eller Update?](#subheading36) |
| &nbsp; | Tabeller |Insert/Update/Delete |[Har du funderat på att lagra data serier som ofta hämtas tillsammans i en enskild entitet som egenskaper i stället för flera entiteter?](#subheading37) |
| &nbsp; | Tabeller |Insert/Update/Delete |[För entiteter som alltid ska hämtas tillsammans och som kan skrivas i batchar (till exempel Time Series-data), har du funderat på att använda blobbar i stället för tabeller?](#subheading38) |
| &nbsp; | Köer |Skalbarhets mål |[Närmar du dig skalbarhets målen för meddelanden per sekund?](#subheading39) |
| &nbsp; | Köer |Konfiguration |[Har du stängt av Nagle för att förbättra prestanda för små begär Anden?](#subheading40) |
| &nbsp; | Köer |Meddelandestorlek |[Är dina meddelanden komprimerade för att förbättra köns prestanda?](#subheading41) |
| &nbsp; | Köer |Hämta Mass hämtning |[Hämtar du flera meddelanden i en enda "Get"-åtgärd?](#subheading42) |
| &nbsp; | Köer |Avsöknings frekvens |[Avsöker du tillräckligt ofta för att minska den uppskattade svars tiden för ditt program?](#subheading43) |
| &nbsp; | Köer |Uppdatera meddelande |[Använder du UpdateMessage för att lagra förloppet för bearbetning av meddelanden, vilket undviker att behöva bearbeta om hela meddelandet om ett fel uppstår?](#subheading44) |
| &nbsp; | Köer |Arkitektur |[Använder du köer för att göra hela programmet skalbart genom att hålla långvariga arbets belastningar från den kritiska vägen och skala sedan oberoende av varandra?](#subheading45) |

## <a name="allservices"></a>Alla tjänster

I det här avsnittet listas beprövade metoder som kan användas för att använda någon av de Azure Storage tjänsterna (blobbar, tabeller, köer eller filer).  

### <a name="subheading1"></a>Skalbarhets mål

Azure Storage har en gräns på 250 lagrings konton per region per prenumeration. Om du når gränsen kan du inte längre skapa fler lagringskonton i den kombinationen av prenumeration/region.

Var och en av de Azure Storage tjänsterna har skalbarhets mål för kapacitet (GB), transaktions hastighet och bandbredd. Om ditt program närmar sig eller överskrider något av skalbarhets målen kan det uppstå ökad transaktions fördröjning eller begränsning. När en lagrings tjänst begränsar ditt program börjar tjänsten returnera felkoderna "503-servern är upptagen" eller "500 åtgärds tids gräns" för vissa lagrings transaktioner. I det här avsnittet beskrivs både den allmänna metoden för att hantera skalbarhets mål och skalbarhet för bandbredd i synnerhet. I senare avsnitt som behandlar enskilda lagrings tjänster diskuterar du skalbarhets mål i samband med den specifika tjänsten:  

* [BLOB-bandbredd och begär Anden per sekund](#subheading16)
* [Tabell enheter per sekund](#subheading24)
* [Köa meddelanden per sekund](#subheading39)  

#### <a name="sub1bandwidth"></a>Bandbredds skalbarhets mål för alla tjänster

Vid tidpunkten för skrivning är bandbredds målen i USA för ett Geo-redundant lagrings konto (GRS) 10 gigabit per sekund (Gbps) för ingress (data som skickas till lagrings kontot) och 20 Gbit/s för utgående data från lagrings kontot. För ett LRS-konto (lokalt redundant lagring) är gränserna högre – 20 Gbit/s för inkommande och 30 Gbit/s för utgående trafik.  De internationella bandbredds gränserna kan vara lägre och finns på [sidan för skalbarhets mål](https://msdn.microsoft.com/library/azure/dn249410.aspx).  Mer information om alternativ för lagrings redundans finns i länkarna i användbara resurser nedan.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>Vad du kan göra när du närmar dig ett skalbarhets mål

Om du närmar dig gränsen för lagrings konton som du kan ha i en viss kombination av prenumerationer och regioner, utvärderar du ditt program och användningen av lagrings konton och avgör om något av dessa villkor gäller.

* Använd lagrings konton som ohanterade diskar och lägga till diskarna till dina virtuella datorer. I det här scenariot rekommenderar vi att du använder [Managed disks](../../virtual-machines/windows/managed-disks-overview.md), eftersom de hanterar skalbarhet för lagrings disken utan att du behöver skapa och hantera enskilda lagrings konton.
* Använd ett lagrings konto per kund för att isolera data. I det här scenariot rekommenderar vi att du använder lagrings behållare för varje kund i stället för ett helt lagrings konto. Med Azure Storage kan du nu ange rollbaserad åtkomst kontroll per [behållare](storage-auth-aad-rbac-portal.md).
* Använd flera lagrings konton till Shard för att öka skalbarheten för ingress/utgående/IOPS/kapacitet. I det här scenariot rekommenderar vi om möjligt att du utnyttjar de [ökade gränserna](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) för standard lagrings konton för att minska antalet lagrings konton som krävs för din arbets belastning.

Om ditt program närmar sig skalbarhets målen för ett enda lagrings konto bör du överväga att använda någon av följande metoder:  

* Ta hänsyn till arbets belastningen som gör att ditt program närmar sig eller överskrider skalbarhets målet. Kan du utforma det på ett annat sätt att använda mindre bandbredd eller kapacitet eller färre transaktioner?
* Om ett program måste överskrida ett av skalbarhets målen bör du skapa flera lagrings konton och partitionera dina program data på flera lagrings konton. Om du använder det här mönstret ser du till att utforma ditt program så att du kan lägga till fler lagrings konton i framtiden för belastnings utjämning. Vid tidpunkten för skrivning kan varje Azure-prenumeration ha upp till 250 lagrings konton per region (när de distribueras med Azure Resource Manager-modellen).  Lagrings konton har inte heller någon annan kostnad än din användning i termer av lagrade data, transaktioner som har gjorts eller överförda data.
* Om ditt program träffar bandbredds målen bör du överväga att komprimera data i klienten för att minska den bandbredd som krävs för att skicka data till lagrings tjänsten.  Även om det kan spara bandbredd och förbättra nätverks prestanda, kan det också vara negativ påverkan.  Du bör utvärdera prestanda effekten av detta på grund av de ytterligare bearbetnings kraven för komprimering och expandering av data i klienten. Dessutom kan lagring av komprimerade data göra det svårare att felsöka problem eftersom det kan vara svårare att visa lagrade data med hjälp av standard verktyg.
* Om ditt program träffar skalbarhets målen kontrollerar du att du använder en exponentiell backoff för återförsök (se återförsök [](#subheading14)).  Det är bättre att se till att du aldrig närmar dig skalbarhets målen (genom att använda någon av metoderna ovan), men det garanterar att programmet inte bara fortsätter att försöka snabbt, vilket gör att begränsningen blir sämre.  

#### <a name="useful-resources"></a>Användbara resurser

Följande länkar ger ytterligare information om skalbarhets mål:

* Se [Azure Storage skalbarhets-och prestanda mål](storage-scalability-targets.md) för information om skalbarhets mål.
* Se [Azure Storage replikering](storage-redundancy.md) och blogg inlägget [Azure Storage redundans alternativ och Geo-redundant lagring med Läs åtkomst](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) för information om alternativ för redundans för lagring.
* Aktuell information om priser för Azure-tjänster finns i [priser för Azure](https://azure.microsoft.com/pricing/overview/).  

### <a name="subheading47"></a>Namngivnings konvention för partitioner

Azure Storage använder ett intervall baserat partitionerings schema för att skala och belastningsutjämna systemet. Partitionsnyckel (konto + container + BLOB) används för att partitionera data i intervall och dessa intervall är belastningsutjämnade i systemet. Detta innebär att namn konventioner som till exempel en lexikal ordning(till exempel dislöneering, *prestanda*, *anställda*osv.) eller användning av tidsstämplar (*log20160101*, *log20160102*, *log20160102*osv.) lånas ut till partitionerna som potentiellt samplacerade på samma partitions Server, tills en belastnings Utjämnings åtgärd delar upp dem i mindre intervall. Till exempel kan alla blobbar i en behållare hanteras av en enda server tills belastningen på dessa blobbar kräver ytterligare åter balansering av partitionens intervall. På samma sätt kan en grupp med lätt inlästa konton med namn ordnade i lexikal ordning hanteras av en enda server tills belastningen på ett eller alla dessa konton kräver att de delas upp på flera partitioner. Varje belastnings Utjämnings åtgärd kan påverka svars tiden för lagrings anrop under åtgärden. Systemets möjlighet att hantera en plötslig trafik av trafik till en partition begränsas av skalbarheten för en enda partitions Server tills belastnings Utjämnings åtgärden är igång och balanserar om partitionens nyckel intervall.

Du kan följa vissa metod tips för att minska frekvensen för sådana åtgärder.  

* Om möjligt ska du använda större sätt i BLOB-eller placerings block (större än 4 MiB för standard konton och fler än 256 KiB för Premium-konton) för att aktivera Block-Blob med hög data flöde (HTBB). HTBB ger högpresterande inmatningar som inte påverkas av namngivning av partitioner.
* Undersök namngivnings konventionen som du använder för konton, behållare, blobbar, tabeller och köer, nära. Överväg att förkorrigera konto, behållare eller BLOB-namn med en tresiffriga hash med en hash-funktion som passar dina behov bäst.  
* Om du ordnar dina data med hjälp av tidsstämplar eller numeriska identifierare måste du se till att du inte använder ett trafik mönster med enbart tillägg (eller lägga). Dessa mönster är inte lämpliga för ett intervallbaserade partitionerings system, och kan leda till all trafik till en enda partition och begränsa systemet från effektiv belastnings utjämning. Om du till exempel har dagliga åtgärder som använder ett BLOB-objekt med en tidsstämpel, till exempel *ÅÅÅÅMMDD*, dirigeras all trafik för den dagliga åtgärden till ett enda objekt, som hanteras av en enda partitions Server. Titta på om gränserna per blob och per partition uppfyller dina behov och Överväg att dela upp den här åtgärden i flera blobbar om det behövs. På samma sätt kan all trafik dirigeras till den sista delen av nyckel namn rummet om du lagrar tids serie data i dina tabeller. Om du måste använda tidsstämplar eller numeriska ID: n, prefixet ID med en tresiffriga hash, eller om tidsstämpel-prefixet är i sekunder en del av tiden som *ssyyyymmdd*. Om du rutinmässigt utför en lista och frågor om åtgärder, väljer du en hash-funktion som begränsar antalet frågor. I andra fall kan ett slumpmässigt prefix vara tillräckligt.  
* För ytterligare information om partitionerings schema som används i Azure Storage, se [Azure Storage: En moln lagrings tjänst med hög tillgänglighet med](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf)stark konsekvens.

### <a name="networking"></a>Nätverk

Även om API: et spelar någon roll, har ofta de fysiska nätverks begränsningarna i programmet en betydande inverkan på prestanda. Nedan beskrivs vissa begränsningar som användare kan stöta på.  

#### <a name="client-network-capability"></a>Klient nätverks funktion

##### <a name="subheading2"></a>Kapacitet

För bandbredd är problemet ofta klientens funktioner. Till exempel kan ett enda lagrings konto hantera 10 Gbit/s av ingress (se bandbredd för [bandbredd](#sub1bandwidth)), nätverks hastigheten i en "liten" Azure Worker Role-instans kan bara vara cirka 100 Mbit/s. Större Azure-instanser har nätverkskort med större kapacitet, så du bör överväga att använda en större instans eller flera virtuella datorer om du behöver högre nätverks gränser från en enda dator. Om du ansluter till en lagrings tjänst från ett lokalt program gäller samma regel: förstå nätverks funktionerna i klient enheten och nätverks anslutningen till Azure Storage plats och förbättra dem efter behov eller design program för att fungera inom sina funktioner.  

##### <a name="subheading3"></a>Länk kvalitet

Precis som med valfri nätverks användning måste du vara medveten om att nätverks förhållandena som resulterar i fel och paket förlust kommer att sakta in ett effektivt data flöde.  Att använda WireShark eller NetMon kan hjälpa dig att diagnostisera det här problemet.  

##### <a name="useful-resources"></a>Användbara resurser

Mer information om storlekar för virtuella datorer och allokerad bandbredd finns i [Windows VM-storlekar](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) eller [Linux VM-storlekar](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

#### <a name="subheading4"></a>Sökvägen

I alla distribuerade miljöer ger klienten nära-servern den bästa prestandan. För att få åtkomst till Azure Storage med den lägsta svars tiden är den bästa platsen för din klient i samma Azure-region. Om du till exempel har en Azure-webbplats som använder Azure Storage bör du hitta dem i en enda region (till exempel USA, västra eller Asien, sydöstra). Detta minskar svars tiden och kostnaden – vid tidpunkten för skrivning är bandbredds användningen inom en enskild region kostnads fri.  

Om dina klient program inte finns i Azure (t. ex. appar för mobil enhet eller lokalt Enterprise-tjänster), så minskar i allmänhet svars tiden för lagrings kontot i en region nära de enheter som kommer att få åtkomst till det. Om dina klienter är brett distribuerade (till exempel vissa i Nordamerika och några i Europa) bör du överväga att använda flera lagrings konton: ett som finns i en Nord amerikansk region och en i en europeisk region. På så sätt kan du minska svars tiden för användare i båda regionerna. Den här metoden är enklare att implementera om data som program arkivet är specifika för enskilda användare och inte behöver replikera data mellan lagrings konton.  För bred innehålls distribution rekommenderas ett CDN – mer information finns i nästa avsnitt.  

### <a name="subheading5"></a>Innehålls distribution

Ibland måste ett program hantera samma innehåll för många användare (till exempel en produkt demonstrations video som används på Start sidan för en webbplats), som finns i samma eller flera regioner. I det här scenariot bör du använda en Content Delivery Network (CDN), till exempel Azure CDN, och CDN skulle använda Azure Storage som data ursprung. Till skillnad från ett Azure Storage-konto som finns i en enda region och som inte kan leverera innehåll med låg latens till andra regioner, Azure CDN använder servrar i flera data center runtom i världen. Dessutom kan ett CDN vanligt vis stödja mycket högre utgående gränser än ett enda lagrings konto.  

Mer information om Azure CDN finns i [Azure CDN](https://azure.microsoft.com/services/cdn/).  

### <a name="subheading6"></a>Använda SAS och CORS

När du behöver auktorisera kod som Java Script i en användares webbläsare eller en mobil telefon-app för att komma åt data i Azure Storage, är en metod att använda ett program i webb rollen som en proxy: användarens enhet autentiseras med webb rollen , som i sin tur tillåter åtkomst till lagrings resurser. På så sätt kan du undvika att exponera lagrings konto nycklar på oskyddade enheter. Detta innebär dock en betydande belastning på webb rollen eftersom alla data som överförs mellan användarens enhet och lagrings tjänsten måste passera genom webb rollen. Du kan undvika att använda en webbroll som proxy för lagrings tjänsten med hjälp av signaturer för delad åtkomst (SAS), ibland tillsammans med resurs delnings huvud mellan ursprung (CORS). Med hjälp av SAS kan du låta användarens enhet göra begär Anden direkt till en lagrings tjänst med hjälp av en begränsad åtkomsttoken. Om en användare till exempel vill överföra ett foto till ditt program, kan din webbroll generera och skicka till användarens enhet en SAS-token som ger behörighet att skriva till en specifik BLOB eller behållare under de närmaste 30 minuterna (efter vilken SAS-token upphör att gälla).

Normalt tillåter en webbläsare inte Java Script på en sida som finns på en webbplats på en domän för att utföra specifika åtgärder, till exempel en "placering" till en annan domän. Om du till exempel är värd för en webb roll på "contosomarketing.cloudapp.net" och vill använda Java Script på klient sidan för att ladda upp en blob till ditt lagrings konto på "contosoproducts.blob.core.windows.net", kommer webbläsarens princip för att förbjuda den här åtgärden. CORS är en webb läsar funktion som gör det möjligt för mål domänen (i det här fallet lagrings kontot) att kommunicera med den webbläsare som den har betrott begär Anden från käll domänen (i det här fallet webb rollen).  

Båda dessa tekniker kan hjälpa dig att undvika onödig belastning (och Flask halsar) i ditt webb program.  

#### <a name="useful-resources"></a>Användbara resurser

Mer information om SAS finns i [bevilja begränsad åtkomst till Azure Storage resurser med hjälp av signaturer för delad åtkomst (SAS)](storage-sas-overview.md).  

Mer information om CORS finns i [stöd för resurs delning mellan ursprung (CORS) för Azure Storage-tjänsterna](https://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Cachelagring

#### <a name="subheading7"></a>Hämtar data

I allmänhet är det bättre att hämta data från en tjänst än att hämta den två gånger. Överväg exemplet på ett MVC-webbprogram som körs i en webbroll som redan har hämtat en 50 MB-BLOB från lagrings tjänsten för att fungera som innehåll till en användare. Programmet kan sedan hämta samma BLOB varje gång en användare begär det, eller cachelagra den lokalt på disk och återanvända den cachelagrade versionen för efterföljande användar förfrågningar. När en användare begär data kan programmet dessutom få ett problem med en villkors rubrik för ändrings tid, vilket skulle undvika att hela blobben hämtas om den inte har ändrats. Du kan använda samma mönster för att arbeta med tabell entiteter.  

I vissa fall kan du bestämma att ditt program kan anta att blobben är giltigt under en kort period efter att det har hämtats och att programmet inte behöver kontrol lera om blobben har ändrats under denna period.

Konfiguration, sökning och andra data som alltid används av programmet är bra kandidater för cachelagring.  

Mer information om villkorliga hämtningar finns i [ange villkorliga rubriker för BLOB service åtgärder](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

#### <a name="subheading8"></a>Laddar upp data i batchar

I vissa program scenarier kan du samla in data lokalt och överföra dem regelbundet i en batch i stället för att ladda upp varje data direkt. Ett webb program kan till exempel ha en loggfil med aktiviteter: programmet kan antingen ladda upp information om varje aktivitet när det sker som en tabell enhet (vilket kräver många lagrings åtgärder), eller så kan du spara aktivitets information till en lokal loggfil och sedan Överför regelbundet all aktivitets information som en avgränsad fil till en blob. Om varje loggpost är 1 KB kan du ladda upp tusentals i en enda "parkera BLOB"-transaktion (du kan ladda upp en BLOB på upp till 64 MB i samma storlek i en enskild transaktion). Om den lokala datorn kraschar före uppladdningen kan du förlora vissa loggdata: programutvecklaren måste utforma för möjligheten för klient enheter eller överförings problem.  Om aktivitets data måste hämtas för tidsintervallen (inte bara enskild aktivitet), rekommenderas blobbar över tabeller.

### <a name="net-configuration"></a>.NET-konfiguration

Om du använder .NET Framework visar det här avsnittet flera snabb konfigurations inställningar som du kan använda för att göra betydande prestanda förbättringar.  Om du använder andra språk kan du kontrol lera om liknande koncept gäller för det valda språket.  

#### <a name="subheading9"></a>Öka standard anslutnings gränsen

I .NET ökar följande kod standard anslutnings gränsen (vanligt vis 2 i en klient miljö eller 10 i en Server miljö) till 100. Normalt bör du ange värdet till ungefär så många trådar som används av ditt program.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Du måste ange anslutnings gränsen innan du öppnar några anslutningar.  

Information om andra programmeringsspråk finns i språk dokumentationen för att fastställa hur du ställer in anslutnings gränsen.  

Mer information finns i blogg inlägget [webb tjänster: Samtidiga anslutningar](https://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx).  

#### <a name="subheading10"></a>Öka det minsta antalet trådar om du använder synkron kod med asynkrona uppgifter

Den här koden kommer att öka det minsta antalet trådar i trådpoolen:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Mer information finns i [metoden trådpool. SetMinThreads](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="subheading11"></a>Dra nytta av .NET 4,5 och högre skräp insamling

Använd .NET 4,5 eller senare för klient programmet för att dra nytta av prestanda förbättringar i Server skräp insamling.

Mer information finns i artikeln [en översikt över prestanda förbättringar i .net 4,5](https://msdn.microsoft.com/magazine/hh882452.aspx).  

### <a name="subheading12"></a>Obegränsad parallellitet

Parallellitet kan vara bra för prestanda, var försiktig med att använda obegränsad parallellitet (ingen gräns för antalet trådar och/eller parallella begär Anden) för att överföra eller hämta data, med hjälp av flera arbetare för att få åtkomst till flera partitioner (behållare, köer eller Table partitions) i samma lagrings konto eller för att komma åt flera objekt i samma partition. Om parallellitet är obundet kan ditt program överskrida klient enhetens funktioner eller lagrings kontots skalbarhets mål som resulterar i längre latens och begränsning.  

### <a name="subheading13"></a>Lagrings klient bibliotek och verktyg

Använd alltid de senaste klient biblioteken och verktygen från Microsoft. Vid tidpunkten för skrivning finns det tillgängliga klient bibliotek för .NET, Windows Phone, Windows Runtime, Java och C++för hands versioner av andra språk. Dessutom har Microsoft publicerat PowerShell-cmdlets och Azure CLI-kommandon för att arbeta med Azure Storage. Microsoft utvecklar aktivt dessa verktyg med prestanda i åtanke, håller dem uppdaterade med de senaste service versionerna och ser till att de hanterar många av de beprövade prestanda metoderna internt.  

### <a name="retries"></a>Antal försök

#### <a name="subheading14"></a>Fel vid begränsning och Server upptagen

I vissa fall kan lagrings tjänsten begränsa ditt program eller kan helt enkelt inte hantera begäran på grund av ett tillfälligt tillstånd och returnera meddelandet "503 Server upptagen" eller "500 timeout".  Detta kan inträffa om ditt program närmar sig något av skalbarhets målen, eller om systemet ombalanserar dina partitionerade data för att möjliggöra större data flöden.  Klient programmet bör normalt försöka utföra åtgärden igen som orsakar felet: det går att försöka utföra samma begäran senare. Men om lagrings tjänsten begränsar ditt program eftersom det överskrider skalbarhets målen, eller även om tjänsten inte kunde hantera begäran av någon annan anledning, gör aggressiva försök vanligt vis problemet sämre. Av den anledningen bör du använda en exponentiell säkerhets kopiering (klient biblioteken som standard för det här beteendet). Ditt program kan till exempel försöka igen om 2 sekunder, sedan 4 sekunder, sedan 10 sekunder, sedan 30 sekunder, och sedan får upp helt. Det här beteendet resulterar i att programmet minskar avsevärt belastningen på tjänsten snarare än att exacerbating eventuella problem.  

Anslutnings fel kan göras omedelbart, eftersom de inte är resultatet av begränsningen och förväntas vara tillfälliga.  

#### <a name="subheading15"></a>Fel som inte går att försöka igen

Klient biblioteken är medvetna om vilka fel som är nya försök och vilka som inte är det. Men om du skriver din egen kod mot lagrings REST API bör du komma ihåg att det finns vissa fel som du inte bör försöka igen: till exempel anger ett svar på 400 (felaktig begäran) att klient programmet har skickat en begäran som inte kunde bearbetas eftersom det inte var i ett förväntat formulär. Om du skickar om den här begäran skickas samma svar varje gång, så det finns ingen punkt för att försöka igen. Om du skriver din egen kod mot lagrings REST API bör du vara medveten om vad felkoderna innebär och det rätta sättet att försöka igen (eller inte) för var och en av dem.  

#### <a name="useful-resources"></a>Användbara resurser

Mer information om lagrings fel koder finns i [status-och felkoder](https://msdn.microsoft.com/library/azure/dd179382.aspx) på Microsoft Azure webbplats.  

## <a name="blobs"></a>Blobar

Utöver beprövade metoder för [alla tjänster](#allservices) som beskrivs ovan gäller följande beprövade metoder specifikt för BLOB service.  

### <a name="blob-specific-scalability-targets"></a>BLOB-/regionsspecifika skalbarhets mål

#### <a name="subheading46"></a>Flera klienter som har åtkomst till ett enskilt objekt samtidigt

Om du har ett stort antal klienter som har åtkomst till ett enda objekt samtidigt, måste du ta hänsyn till skalbarhet per objekt och lagrings konto. Det exakta antalet klienter som kan komma åt ett enda objekt varierar beroende på faktorer som antalet klienter som begär objektet samtidigt, storleken på objektet, nätverks förhållandena osv.

Om objektet kan distribueras via ett CDN, till exempel bilder eller videor som hanteras från en webbplats, bör du använda ett CDN. Se [här](#subheading5).

I andra scenarier, till exempel vetenskapliga simuleringar där data är konfidentiella, har du två alternativ. Det första är att sprida arbets Belastningens åtkomst så att objektet kan nås under en viss tids period, jämfört med att nås samtidigt. Alternativt kan du tillfälligt kopiera objektet till flera lagrings konton, vilket ökar det totala antalet IOPS per objekt och över lagrings konton. Vid begränsad testning upptäckte vi att cirka 25 virtuella datorer samtidigt skulle kunna hämta en 100-GB-BLOB parallellt (varje virtuell dator var parallell med nedladdningen med 32-trådar). Om du hade 100-klienter som behöver åtkomst till objektet måste du först kopiera det till ett andra lagrings konto och sedan ha de 50 första virtuella datorerna åtkomst till den första blobben och de andra 50-VM: ar åtkomst till den andra blobben. Resultaten varierar beroende på hur dina program fungerar, så du bör testa detta under designen. 

#### <a name="subheading16"></a>Bandbredd och åtgärder per BLOB

Du kan läsa eller skriva till en enda BLOB på upp till högst 60 MB per sekund (detta är cirka 480 Mbit/s, vilket överskrider funktionerna i många nätverk på klient sidan (inklusive det fysiska NÄTVERKSKORTet på klient enheten). Dessutom stöder en enskild BLOB upp till 500 begär Anden per sekund. Om du har flera klienter som behöver läsa samma blob och du kan överskrida dessa gränser bör du överväga att använda CDN för att distribuera blobben.  

Mer information om mål data flöde för blobbar finns i [Azure Storage skalbarhets-och prestanda mål](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Kopiera och flytta blobbar

#### <a name="subheading17"></a>Kopiera BLOB

Lagrings REST API version 2012-02-12 introducerade den användbara möjligheten att kopiera blobbar mellan konton: ett klient program kan instruera lagrings tjänsten att kopiera en BLOB från en annan källa (eventuellt i ett annat lagrings konto) och sedan låta tjänsten utföra kopieringen asynkront. Detta kan avsevärt minska den bandbredd som krävs för programmet när du migrerar data från andra lagrings konton eftersom du inte behöver hämta och ladda upp data.  

Tänk dock på att när du kopierar mellan lagrings konton finns det ingen tids garanti när kopieringen ska slutföras. Om ditt program behöver slutföra en BLOB-kopiering snabbt under din kontroll kan det vara bättre att kopiera blobben genom att ladda ned den till en virtuell dator och sedan ladda upp den till målet.  För fullständig förutsägbarhet i den situationen, se till att kopian utförs av en virtuell dator som körs i samma Azure-region, eller andra nätverks förhållanden kan (och förmodligen påverka) dina kopierings prestanda.  Dessutom kan du övervaka förloppet för en asynkron kopia program mässigt.  

Kopior inom samma lagrings konto slutförs vanligt vis snabbt.  

Mer information finns i [Kopiera BLOB](https://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="subheading18"></a>Använd AzCopy

Azure Storages teamet har släppt ett kommando rads verktyg "AzCopy" som är avsett att hjälpa till med Mass överföring av många blobbar till, från och över lagrings konton.  Det här verktyget är optimerat för det här scenariot och kan uppnå höga överföringshastigheter.  Användningen rekommenderas för Mass uppladdning, nedladdning och kopierings scenarier. Mer information om det och hur du laddar ned det finns i [överföra data med kommando rads verktyget AzCopy](storage-use-azcopy.md).  

#### <a name="subheading19"></a>Azure import/export-tjänsten

För stora data mängder (mer än 1 TB) erbjuder Azure Storage tjänsten import/export, som gör det möjligt att ladda upp och ladda ned från Blob Storage genom att leverera hård diskar.  Du kan placera dina data på en hård disk och skicka dem till Microsoft för att ladda upp eller skicka en tom hård disk till Microsoft för att hämta data.  Mer information finns i [Använda tjänsten Microsoft Azure Import/Export för att överföra data till Blob Storage](../storage-import-export-service.md).  Detta kan vara mycket mer effektivt än att ladda upp/ladda ned denna data volym över nätverket.  

### <a name="subheading20"></a>Använd metadata

Blob Service stöder Head-begäranden som kan innehålla metadata om blobben. Om ditt program till exempel behövde EXIF-data från ett foto, kan det Hämta fotot och extrahera det. För att spara bandbredd och förbättra prestanda kan ditt program lagra EXIF-data i blobens metadata när programmet överförde fotot: du kan sedan hämta EXIF-data i metadata med bara en HEAD-begäran, spara större bandbredd och bearbetnings tid som krävs för att extrahera EXIF-data varje gången bloben läses. Detta kan vara användbart i scenarier där du bara behöver metadata och inte hela innehållet i en blob.  Endast 8 KB metadata kan lagras per BLOB (tjänsten accepterar inte en begäran om att lagra mer än den), så om data inte ryms i den storleken kanske du inte kan använda den här metoden.  

### <a name="rapid-uploading"></a>Snabb uppladdning

Om du snabbt vill ladda upp blobar är det första frågan att besvara: är du laddar upp en BLOB eller flera?  Använd anvisningarna nedan för att avgöra vilken metod som ska användas beroende på ditt scenario.  

#### <a name="subheading21"></a>Snabbt ladda upp en stor BLOB

Om du snabbt vill överföra en enda stor BLOB bör klient programmet Ladda upp sina block eller sidor parallellt (som mindful av skalbarhets målen för enskilda blobbar och lagrings kontot som helhet).  De officiella Microsoft-klient biblioteken med RTM-lagring (.NET, Java) har möjlighet att göra detta.  För varje bibliotek använder du det angivna objektet/egenskapen nedan för att ange nivån för samtidighet:  

* .NET: Ange ParallelOperationThreadCount för ett BlobRequestOptions-objekt som ska användas.
* Java/Android: Use BlobRequestOptions.setConcurrentRequestCount()
* Node. js: Använd parallelOperationThreadCount antingen i alternativ för begäran eller på Blob Service.
* C++: Använd metoden blob_request_options:: set_parallelism_factor.

#### <a name="subheading22"></a>Snabbt ladda upp många blobbar

Ladda upp flera blobbar snabbt genom att ladda upp blobar parallellt. Detta är snabbare än att ladda upp enskilda blobbar i taget med parallella block uppladdningar eftersom det sprider uppladdning över flera partitioner i lagrings tjänsten. En enda BLOB har endast stöd för ett data flöde på 60 MB/sekund (cirka 480 Mbit/s). Vid tidpunkten för skrivning har ett USA-baserat LRS-konto stöd för upp till 20 Gbit/s-ingångar, vilket är mycket mer än det data flöde som stöds av en enskild blob.  [AzCopy](#subheading18) utför överföringar parallellt som standard och rekommenderas för det här scenariot.  

### <a name="subheading23"></a>Välja rätt typ av BLOB

Azure Storage stöder två typer av BLOB: *Page* blobbar och *block* -blobar. För ett angivet användnings scenario kommer ditt val av Blob-typ att påverka lösningens prestanda och skalbarhet. Block blobbar är lämpliga när du vill överföra stora data mängder effektivt: till exempel kan ett klient program behöva ladda upp foton eller video till Blob Storage. Page blobbar är lämpliga om programmet behöver utföra slumpmässiga skrivningar på data: till exempel lagras Azure-VHD: ar som Page blobbar.  

Mer information finns i [förstå block-blobbar, bifoga blobbar och sid](https://msdn.microsoft.com/library/azure/ee691964.aspx)-blobar.  

## <a name="tables"></a>Tabeller

Utöver de beprövade metoderna för [alla tjänster](#allservices) som beskrivs ovan gäller följande beprövade metoder specifikt för tabell tjänsten.  

### <a name="subheading24"></a>Tabell-/regionsspecifika skalbarhets mål

Förutom bandbredds begränsningarna för ett helt lagrings konto har tabeller följande angivna skalbarhets gränser.  Systemet kommer att belastningsutjämna när trafiken ökar, men om trafiken har plötsliga burst-överföringar kanske du inte kan hämta data flödet direkt.  Om ditt mönster har burst-överföringar bör du förvänta dig att se begränsning och/eller tids gränser under burst när lagrings tjänsten automatiskt laddar upp saldon för din tabell.  Att öka långsamt i allmänhet har bättre resultat eftersom det ger system tiden att belastningsutjämna på lämpligt sätt.  

#### <a name="entities-per-second-storage-account"></a>Entiteter per sekund (lagrings konto)

Skalbarhets gränsen för att komma åt tabeller är upp till 20 000 entiteter (1 KB per sekund) per sekund för ett konto.  I allmänhet räknas varje enhet som infogas, uppdateras, tas bort eller skannas mot målet.  En batch-infogning som innehåller 100 entiteter räknas som 100 entiteter.  En fråga som skannar 1000 entiteter och returnerar 5 räknas som 1000 entiteter.  

#### <a name="entities-per-second-partition"></a>Entiteter per sekund (partition)

I en enda partition är skalbarhets målet för att komma åt tabeller 2 000 entiteter (1 KB per sekund) med samma inventering som beskrivs i föregående avsnitt.  

### <a name="configuration"></a>Konfiguration

Det här avsnittet innehåller flera snabb konfigurations inställningar som du kan använda för att göra betydande prestanda förbättringar i tabell tjänsten:  

#### <a name="subheading25"></a>Använd JSON

Från och med Storage Service version 2013-08-15 stöder Table service användningen av JSON i stället för det XML-baserade AtomPub-formatet för överföring av tabell data. Detta kan minska nytto Last storlekarna med så mycket som 75% och kan förbättra programmets prestanda avsevärt.

Mer information finns i post [Microsoft Azures tabeller: Presentation av](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) JSON [-och nytto Last format för tabell tjänst åtgärder](https://msdn.microsoft.com/library/azure/dn535600.aspx).

#### <a name="subheading26"></a>Inaktivera Nagle

Nagle-algoritmen implementeras ofta i TCP/IP-nätverk som ett sätt att förbättra nätverks prestanda. Men det är inte optimalt i alla situationer (till exempel mycket interaktiva miljöer). För Azure Storage har Nagle en negativ inverkan på prestanda för begär anden till tabellen och Queue Services och du bör inaktivera den om möjligt.

### <a name="schema"></a>Schema

Hur du representerar och frågar dina data är den största enkla faktorn som påverkar tabell tjänstens prestanda. Även om alla program är olika beskriver det här avsnittet några allmänna beprövade metoder som är relaterade till:  

* Tabell design
* Effektiva frågor
* Effektiva data uppdateringar  

#### <a name="subheading27"></a>Tabeller och partitioner

Tabellerna är indelade i partitioner. Varje enhet som lagras i en partition delar samma partitionsnyckel och har en unik rad nyckel för att identifiera den i den partitionen. Partitioner ger förmåner, men omfattar även skalbarhets gränser.  

* Fördelar: Du kan uppdatera entiteter i samma partition i en enskild, atomisk batch-transaktion som innehåller upp till 100 separata lagrings åtgärder (högst 4 MB storlek). Om du antar samma antal entiteter som ska hämtas kan du även fråga efter data inom en enda partition än data som sträcker sig över partitioner (Läs vidare om du vill veta mer om att fråga tabell data).
* Skalbarhets gräns: Åtkomst till entiteter som lagras i en enskild partition kan inte läsas in eftersom partitioner stöder atomiska batch-transaktioner. Därför är skalbarhets målet för en enskild tabell partition lägre än för tabell tjänsten som helhet.  

På grund av dessa egenskaper för tabeller och partitioner bör du införa följande design principer:  

* Data som klient programmet ofta uppdaterar eller efterfrågar i samma logiska arbets enhet bör finnas i samma partition.  Detta kan bero på att ditt program aggregerar skrivningar eller om du vill dra nytta av atomiska batch-åtgärder.  Dessutom kan data i en enda partition bli mer effektiva i en enskild fråga än data mellan partitioner.
* Data som klient programmet inte infogar/uppdaterar eller frågar i samma logiska arbets enhet (enskild fråga eller uppdatering av batch) måste finnas i separata partitioner.  En viktig anmärkning är att det inte finns någon gräns för antalet partitionstyper i en enskild tabell, så att det inte är något problem med miljon tals partitionerings nycklar och påverkar inte prestandan.  Om ditt program till exempel är en populär webbplats med användar inloggning, kan det vara ett bra val att använda användar-ID: t som partitionsnyckel.  

#### <a name="hot-partitions"></a>Varma partitioner

En varm partition är en som tar emot en oproportionerlig procent andel av trafiken till ett konto och kan inte läsas in eftersom det är en enda partition.  I allmänhet skapas varma partitioner på ett av två sätt:  

##### <a name="subheading28"></a>Endast tillägg och lägga mönster

Mönstret "Lägg till endast" är ett av alla (eller nästan alla) trafik till en bestämd PK ökar och minskar i enlighet med aktuell tid.  Ett exempel är om ditt program använder det aktuella datumet som en partitionsnyckel för loggdata.  Detta resulterar i alla infogningar som går till den sista partitionen i tabellen och systemet kan inte belastningsutjämna eftersom alla skrivningar kommer till slutet av din tabell.  Om volymen för trafik till partitionen överskrider skalbarhets målet på partitions nivå, leder det till en begränsning.  Det är bättre att se till att trafiken skickas till flera partitioner, så att du kan aktivera belastnings utjämning för förfrågningarna i din tabell.  

##### <a name="subheading29"></a>Data med hög trafik

Om ditt partitionerings schema resulterar i en enda partition som bara har data som är mycket mer använda än andra partitioner, kan du också se begränsning när den partitionen närmar sig skalbarhets målet för en enda partition.  Det är bättre att se till att ditt partitionsschema resulterar i att det inte finns någon partition som närmar sig skalbarhets målen.  

#### <a name="querying"></a>Fråga

I det här avsnittet beskrivs beprövade metoder för att skicka frågor till tabell tjänsten.  

##### <a name="subheading30"></a>Fråge omfång

Det finns flera sätt att ange intervallet för entiteter att fråga.  Följande är en beskrivning av de olika användnings områdena.  

I allmänhet bör du undvika genomsökningar (frågor som är större än en enskild entitet), men om du måste skanna kan du försöka organisera dina data så att dina sökningar hämtar de data du behöver utan att söka efter eller returnera större mängder entiteter som du inte behöver.  

###### <a name="point-queries"></a>Punkt frågor

En punkt fråga hämtar exakt en entitet. Det gör du genom att ange både partitionsnyckel och rad nyckel för entiteten som ska hämtas. Dessa frågor är effektiva och du bör använda dem där det är möjligt.  

###### <a name="partition-queries"></a>Partitionera frågor

En partitions fråga är en fråga som hämtar en uppsättning data som delar en gemensam partitionsnyckel. Normalt anger frågan ett intervall med rad nyckel värden eller ett värde intervall för viss enhets egenskap utöver en partitionsnyckel. Dessa är mindre effektiva än punkt frågor och bör användas sparsamt.  

###### <a name="table-queries"></a>Tabell frågor

En tabell fråga är en fråga som hämtar en uppsättning entiteter som inte delar en gemensam partitionsnyckel. Dessa frågor är inte effektiva och du bör undvika dem om möjligt.  

##### <a name="subheading31"></a>Frågans densitet

En annan viktig faktor i frågans effektivitet är antalet entiteter som returnerades jämfört med antalet entiteter som genomsöktes för att hitta den returnerade uppsättningen. Om programmet utför en tabell fråga med ett filter för ett egenskaps värde som bara är 1% av data resurserna, genomsöker frågan 100 entiteter för varje entitet som returneras. De tabell skalbarhets mål som diskuterats tidigare relaterar till antalet genomsökta enheter och inte antalet returnerade enheter: en låg frågans densitet kan enkelt orsaka att tabell tjänsten begränsar ditt program eftersom det måste genomsöka så många entiteter till Hämta entiteten som du letar efter.  Se avsnittet nedan om [avnormalisering](#subheading34) för mer information om hur du undviker detta.  

##### <a name="limiting-the-amount-of-data-returned"></a>Begränsa mängden data som returneras

###### <a name="subheading32"></a>Statisk

När du vet att en fråga kommer att returnera entiteter som du inte behöver i klient programmet kan du överväga att använda ett filter för att minska storleken på den returnerade uppsättningen. Även om entiteterna som inte returneras till klienten fortfarande räknas mot skalbarhets gränserna, förbättras programmets prestanda på grund av den minskade storleken på nätverkets nytto last och det reducerade antalet enheter som klient programmet måste bearbeta.  Se ovanstående anmärkning om [frågans densitet](#subheading31), men skalbarhets målen relaterar till antalet genomsökta entiteter, så en fråga som filtrerar ut många entiteter kan fortfarande leda till begränsning, även om några entiteter returneras.  

###### <a name="subheading33"></a>Projektion

Om klient programmet bara behöver en begränsad uppsättning egenskaper från entiteterna i tabellen kan du använda projektion för att begränsa storleken på den returnerade data uppsättningen. Som med filtrering bidrar detta till att minska belastningen på nätverket och klient bearbetningen.  

##### <a name="subheading34"></a>Denormalisering

Till skillnad från att arbeta med relations databaser, är beprövade metoder för att effektivt fråga tabell data att avnormalisera dina data. Det innebär att duplicera samma data i flera entiteter (en för varje nyckel som du kan använda för att hitta data) för att minimera antalet entiteter som en fråga måste genomsökas för att hitta de data som klienten behöver, i stället för att behöva söka igenom ett stort antal entiteter för att hitta de data som din app ogrampool-behov.  I en e-handelswebbplats kanske du till exempel vill hitta en order både i kund-ID: t (meddela mig till den här kundens order) och efter datumet (ge mig beställningar på ett datum).  I Table Storage är det bäst att lagra entiteten (eller en referens till den) två gånger – en gång med tabell namn, PN och kund för att under lätta sökning efter kund-ID, en gång för att under lätta att hitta den med datumet.  

#### <a name="insertupdatedelete"></a>Insert/Update/Delete

I det här avsnittet beskrivs beprövade metoder för att ändra entiteter som lagras i tabell tjänsten.  

##### <a name="subheading35"></a>Batchbearbetning

Batch-transaktioner kallas för enhets grupp transaktioner (ETG) i Azure Storage. alla åtgärder i en ETG måste finnas på en enda partition i en enda tabell. Använd om möjligt ETGs för att utföra infogningar, uppdateringar och borttagningar i batchar. Detta minskar antalet tur och inkörningar från klient programmet till servern, vilket minskar antalet fakturerbara transaktioner (en ETG räknas som en enskild transaktion för fakturerings syfte och kan innehålla upp till 100 lagrings åtgärder) och möjliggör atomiska uppdateringar (alla åtgärder lyckades eller fungerar inte inom en ETG). Miljöer med hög fördröjning, till exempel mobila enheter, kommer att ha stor nytta av att använda ETGs.  

##### <a name="subheading36"></a>Upsert

Använd Table **upsert** -åtgärder där det är möjligt. Det finns två typer av **upsert**, som båda kan vara mer effektiva än vanliga **insert** -och **uppdaterings** åtgärder:  

* **InsertOrMerge**: Använd det här när du vill ladda upp en delmängd av entitetens egenskaper, men är inte säker på om entiteten redan finns. Om entiteten finns uppdaterar det här anropet egenskaperna som ingår i **upsert** -åtgärden och lämnar alla befintliga egenskaper, om entiteten inte finns, infogar den nya entiteten. Detta liknar att använda projektion i en fråga, i att du bara behöver överföra de egenskaper som ändras.
* **InsertOrReplace**: Använd det här när du vill ladda upp en helt ny entitet, men är osäker på om den redan finns. Du bör endast använda detta när du vet att den nyligen överförda entiteten är helt korrekt, eftersom den gamla entiteten skrivs över helt. Till exempel vill du uppdatera entiteten som lagrar en användares aktuella plats oavsett om programmet har lagrat plats data tidigare lagrat för användaren. entiteten ny plats har slutförts och du behöver inte någon information från någon tidigare entitet.

##### <a name="subheading37"></a>Lagra data serier i en enskild entitet

Ibland lagrar ett program en serie data som ofta behöver hämtas samtidigt: ett program kan till exempel spåra CPU-användning över tid för att rita ett rullande diagram över data från de senaste 24 timmarna. En metod är att ha en tabell enhet per timme, där varje entitet representerar en viss timme och lagrar processor användningen för den timmen. För att kunna rita dessa data måste programmet hämta de entiteter som innehåller data från de 24 senaste timmarna.  

Alternativt kan ditt program lagra processor användningen för varje timme som en separat egenskap i en enskild enhet: om du vill uppdatera varje timme kan programmet använda ett enda **InsertOrMerge upsert** -anrop för att uppdatera värdet för den senaste timmen. För att du ska kunna rita data behöver programmet bara hämta en enda entitet i stället för 24, vilket gör det för en effektiv fråga (se ovan diskussion om [frågans omfång](#subheading30)).

##### <a name="subheading38"></a>Lagra strukturerade data i blobbar

Ibland är strukturerade data på samma sätt som det ska gå i tabeller, men intervall med entiteter hämtas alltid tillsammans och kan infogas.  Ett lämpligt exempel på detta är en loggfil.  I det här fallet kan du gruppera flera minuters loggar, infoga dem och sedan hämtar du alltid flera minuters loggar i taget.  I det här fallet är det bättre att använda blobbar i stället för tabeller eftersom du kan minska antalet objekt som skrivs/returneras, samt vanligt vis antalet begär Anden som behöver göras.  

## <a name="queues"></a>Köer

Utöver de beprövade metoderna för [alla tjänster](#allservices) som beskrivs ovan gäller följande beprövade metoder specifikt för Queue Service.  

### <a name="subheading39"></a>Skalbarhets gränser

En enskild kö kan bearbeta cirka 2 000 meddelanden (1 KB per sekund) per sekund (varje AddMessage, GetMessage och DeleteMessage räknas som ett meddelande här). Om detta inte är tillräckligt för ditt program bör du använda flera köer och sprida meddelandena över dem.  

Visa aktuella skalbarhets mål på [Azure Storage skalbarhet och prestanda mål](storage-scalability-targets.md).  

### <a name="subheading40"></a>Inaktivera Nagle

Se avsnittet om tabell konfiguration som diskuterar Nagle-algoritmen – Nagle-algoritmen är vanligt vis felaktig för prestanda i Queue-begäranden och du bör inaktivera den.  

### <a name="subheading41"></a>Meddelande storlek

Öka prestanda och skalbarhet för köer när meddelande storleken ökar. Du bör endast placera den information mottagaren behöver i ett meddelande.  

### <a name="subheading42"></a>Hämta batch

Du kan hämta upp till 32 meddelanden från en kö i en enda åtgärd. Detta kan minska antalet turer från klient programmet, vilket är särskilt användbart för miljöer, till exempel mobila enheter, med hög latens.  

### <a name="subheading43"></a>Avsöknings intervall för kö

De flesta program söker efter meddelanden från en kö, som kan vara en av de största källorna till transaktioner för programmet. Välj avsöknings intervall: avsökningen för ofta kan leda till att ditt program använder skalbarhets målen för kön. Men vid 200 000 transaktioner för $0,01 (vid tidpunkten för skrivning) är en enskild processor avsökning en gång per sekund under en månad som kostar mindre än 15 cent så kostnaden är inte vanligt vis en faktor som påverkar ditt val av avsöknings intervall.  

För uppdaterad kostnads information, se [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="subheading44"></a>Uppdatera meddelande

Du kan använda åtgärden **Uppdatera meddelande** för att öka tids gränsen för insikter eller uppdatera statusinformation för ett meddelande. Även om detta är kraftfullt, kom ihåg att varje **uppdaterings meddelande** åtgärd räknas mot skalbarhets målet. Detta kan dock vara en mycket mer effektiv metod än att ha ett arbets flöde som skickar ett jobb från en kö till nästa, eftersom varje steg i jobbet har slutförts. Med åtgärden **Uppdatera meddelande** kan ditt program spara jobb status i meddelandet och fortsätta att arbeta, i stället för att köa om meddelandet för nästa steg i jobbet varje gång ett steg slutförs.  

Mer information finns i artikeln [How to: Ändra innehållet i ett köat meddelande](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message).  

### <a name="subheading45"></a>Program arkitektur

Du bör använda köer för att göra din program arkitektur skalbar. Nedan visas några exempel på hur du kan använda köer för att göra programmet mer skalbart:  

* Du kan använda köer för att skapa arbets loggar för bearbetning och smidiga arbets belastningar i ditt program. Du kan till exempel köa upp förfrågningar från användare för att utföra processor intensiva arbeten, till exempel ändra storlek på överförda bilder.
* Du kan använda köer för att koppla bort delar av ditt program så att du kan skala dem oberoende av varandra. En webb klient del kan till exempel placera undersöknings resultat från användare i en kö för senare analys och lagring. Du kan lägga till fler arbets Rolls instanser för att bearbeta tjänstedata vid behov.  

## <a name="conclusion"></a>Sammanfattning

I den här artikeln beskrivs några av de vanligaste, beprövade metoderna för att optimera prestanda när du använder Azure Storage. Vi rekommenderar att alla programutvecklare utvärderar sina program med hjälp av ovanstående metoder, och överväger att följa rekommendationerna för att få ut mesta möjliga av de program som använder sig av Azure-lagring.
