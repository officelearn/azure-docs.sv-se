---
title: Azure Storage-prestanda och skalbarhet Checklista | Microsoft Docs
description: En checklista med beprövade metoder för användning med Azure Storage i att utveckla högpresterande program.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 12/08/2016
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: b8451a1195ab64d3cd7afda074d786a3209ce785
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793976"
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>Prestanda och skalbarhetschecklista för Microsoft Azure Storage
## <a name="overview"></a>Översikt
Microsoft har utvecklat ett antal beprövade metoder för att använda dessa tjänster på ett effektivt sätt sedan versionen av Microsoft Azure Storage-tjänster och den här artikeln fungerar att konsolidera den viktigaste inställningen av dem i en checklista-style-lista. Avsikten med den här artikeln är att hjälpa programutvecklare att kontrollera att de använder beprövade metoder med Azure Storage och hjälper dem att identifiera andra beprövade metoder som bör de överväga att använda. Den här artikeln använder inte att täcka alla möjliga optimering för prestanda och skalbarhet – den omfattar inte de som är små i deras inverkan eller inte bred front. I den utsträckning som går att förutse programmets beteende under design, är det bra att behålla dem i åtanke tidigt för att undvika Designer som kommer stöter på problem med prestanda.  

Alla programutvecklare som använder Azure Storage ska ta dig tid att läsa den här artikeln och kontrollera att hans eller hennes program följer var och en av de beprövade metoder som anges nedan.  

## <a name="checklist"></a>Checklista
Den här artikeln organiserar beprövade metoder i följande grupper. Beprövade metoder som gäller för:  

* Alla Azure Storage-tjänster (BLOB-objekt, tabeller, köer och filer)
* Blobar
* Tabeller
* Köer  

| Klart | Område | Kategori | Fråga |
| --- | --- | --- | --- |
| &nbsp; | Alla tjänster |Skalbarhetsmål |[Är programmet som utformat för att undvika närmar sig det för skalbarhetsmål?](#subheading1) |
| &nbsp; | Alla tjänster |Skalbarhetsmål |[Har din namngivningskonvention som utformats för att aktivera bättre belastningsutjämning?](#subheading47) |
| &nbsp; | Alla tjänster |Nätverk |[Har sida klientenheter tillräckligt hög bandbredd och låg fördröjning för att få de prestanda som behövs?](#subheading2) |
| &nbsp; | Alla tjänster |Nätverk |[Har sida klientenheter en tillräckligt hög kvalitet länk?](#subheading3) |
| &nbsp; | Alla tjänster |Nätverk |[Klientprogrammet finns ”” storage-konto?](#subheading4) |
| &nbsp; | Alla tjänster |Innehållsdistribution |[Använder du ett CDN för innehållsdistribution?](#subheading5) |
| &nbsp; | Alla tjänster |Dirigera klientåtkomst |[Använder du SAS- och CORS för direkt åtkomst till lagring i stället för proxy?](#subheading6) |
| &nbsp; | Alla tjänster |Cachelagring |[Är din cachelagring programdata som används flera gånger och ändringar sällan?](#subheading7) |
| &nbsp; | Alla tjänster |Cachelagring |[Ditt program är grupperade uppdateringar (cachelagring på klientsidan av dem och sedan laddas upp i större sets)?](#subheading8) |
| &nbsp; | Alla tjänster |.NET Configuration |[Har du konfigurerat din klient som använder ett tillräckligt antal samtidiga anslutningar](#subheading9) |
| &nbsp; | Alla tjänster |.NET Configuration |[Har du konfigurerat .NET för att använda ett tillräckligt antal trådar?](#subheading10) |
| &nbsp; | Alla tjänster |.NET Configuration |[Du använder .NET 4.5 eller senare, vilket har förbättrat skräpinsamling?](#subheading11) |
| &nbsp; | Alla tjänster |Parallellitet |[Har du sett att parallellitet avgränsas på lämpligt sätt så att du inte överbelasta dina klientfunktioner eller det för skalbarhetsmål?](#subheading12) |
| &nbsp; | Alla tjänster |Verktyg |[Är du med hjälp av den senaste versionen av Microsoft tillhandahöll klientbibliotek och verktyg?](#subheading13) |
| &nbsp; | Alla tjänster |Antal försök |[Är du med hjälp av en exponentiell backoff återförsöksprincip för begränsning av fel och tidsgränser?](#subheading14) |
| &nbsp; | Alla tjänster |Antal försök |[Är dina program Undvik återförsök för icke-återförsöksbar fel?](#subheading15) |
| &nbsp; | Blobar |Skalbarhetsmål |[Har du ett stort antal klienter som ansluter till ett enda objekt samtidigt?](#subheading46) |
| &nbsp; | Blobar |Skalbarhetsmål |[Är programmet dig inom skalbarhetsmålen bandbredd eller åtgärder för en enda blob?](#subheading16) |
| &nbsp; | Blobar |Kopiera BLOB |[Är du kopiera blobar på ett effektivt sätt?](#subheading17) |
| &nbsp; | Blobar |Kopiera BLOB |[Använder du AzCopy för bulk-kopior av blobbar?](#subheading18) |
| &nbsp; | Blobar |Kopiera BLOB |[Använder du Azure Import/Export för att överföra stora mängder data?](#subheading19) |
| &nbsp; | Blobar |Använda Metadata |[Lagrar du ofta använda metadata om BLOB-objekt i deras metadata?](#subheading20) |
| &nbsp; | Blobar |Ladda upp snabbt |[När du försöker ladda upp en blob snabbt du laddar upp block parallellt?](#subheading21) |
| &nbsp; | Blobar |Ladda upp snabbt |[När du försöker ladda upp många blobar snabbt du överför blobbar parallellt?](#subheading22) |
| &nbsp; | Blobar |Korrigera Blobtyp |[Använder du sidblobar eller blockblob-objekt när det är lämpligt?](#subheading23) |
| &nbsp; | Tabeller |Skalbarhetsmål |[Du närmar dig skalbarhetsmål för entiteter per sekund?](#subheading24) |
| &nbsp; | Tabeller |Konfiguration |[Använder du JSON för tabell-begäranden?](#subheading25) |
| &nbsp; | Tabeller |Konfiguration |[Har du stängt Nagle av för att förbättra prestanda för små begäranden?](#subheading26) |
| &nbsp; | Tabeller |Tabeller och partitioner |[Har du rätt partitionerat data?](#subheading27) |
| &nbsp; | Tabeller |Heta partitioner |[Är du undvika Lägg endast och endast åtkomstgruppen mönster?](#subheading28) |
| &nbsp; | Tabeller |Heta partitioner |[Infogningar/uppdateringar fördelade över många partitioner?](#subheading29) |
| &nbsp; | Tabeller |Omfång |[Du är utformade för ditt schema för återställningspunkt frågor som ska användas i de flesta fall och frågor för tabeller som ska användas sparsamt?](#subheading30) |
| &nbsp; | Tabeller |Fråga densitet |[Gör dina frågor vanligtvis endast genomsökning och returnerar rader som programmet ska använda?](#subheading31) |
| &nbsp; | Tabeller |Begränsa returnerade Data |[Du använder filtrering för att undvika att de enheter som inte behövs?](#subheading32) |
| &nbsp; | Tabeller |Begränsa returnerade Data |[Använder du projektion för att undvika att egenskaper som inte behövs?](#subheading33) |
| &nbsp; | Tabeller |Denormalisering |[Har du Avnormaliserade data så att du undviker ineffektiva frågor eller flera läsbegäranden vid försök att hämta data?](#subheading34) |
| &nbsp; | Tabeller |Insert/Update/Delete |[Är du batchbearbetning av begäranden som behöver vara transaktionella eller kan göras på samma gång för att minska turer?](#subheading35) |
| &nbsp; | Tabeller |Insert/Update/Delete |[Är du undvika hämtning av en entitet bara för att avgöra om att anropa insert eller update?](#subheading36) |
| &nbsp; | Tabeller |Insert/Update/Delete |[Har du tänkt lagra serien med data som hämtas ofta tillsammans i en enda entitet som egenskaper i stället för flera enheter](#subheading37) |
| &nbsp; | Tabeller |Insert/Update/Delete |[För entiteter som alltid att hämtas tillsammans och kan skrivas i batchar (till exempel time series-data), har du tänkt använda blobar i stället för tabeller](#subheading38) |
| &nbsp; | Köer |Skalbarhetsmål |[Du närmar dig skalbarhetsmål för meddelanden per sekund?](#subheading39) |
| &nbsp; | Köer |Konfiguration |[Har du stängt Nagle av för att förbättra prestanda för små begäranden?](#subheading40) |
| &nbsp; | Köer |Meddelandestorlek |[Är dina meddelanden CD för att förbättra prestanda för kön?](#subheading41) |
| &nbsp; | Köer |Hämta grupp |[Hämtar du flera meddelanden i en enda åtgärd ”hämta”?](#subheading42) |
| &nbsp; | Köer |Frågefrekvens för avsökningsfrekvens |[Är du avsökning tillräckligt ofta för att minska upplevd svarstiden för programmet?](#subheading43) |
| &nbsp; | Köer |Uppdatera meddelande |[Använder du UpdateMessage för att lagra förlopp i bearbetat meddelandena undvika att behöva bearbeta hela meddelandet om ett fel inträffar?](#subheading44) |
| &nbsp; | Köer |Arkitektur |[Använder du köer för att göra hela programmet mer skalbart genom att lagra långsiktiga arbetsbelastningar utanför den kritiska vägen och sedan skalas oberoende av varandra?](#subheading45) |

## <a name="allservices"></a>Alla tjänster
Det här avsnittet innehåller några beprövade metoder som gäller för användning av Azure Storage-tjänster (BLOB-objekt, tabeller, köer eller filer).  

### <a name="subheading1"></a>Skalbarhetsmål
Azure Storage själva har en gräns på 250 storage-konton per region per prenumeration. Om du når gränsen kan du inte skapa några fler storage-konton i kombinationen av prenumeration /-region.

Varje Azure Storage-tjänsterna har skalbarhetsmål för kapacitet (GB), transaktioner och bandbredd. Om ditt program närmar sig eller överskrider något av det för skalbarhetsmål, kan den stöta på ökad transaktion svarstider eller begränsas. När en lagringstjänst tiden begränsar ditt program, börjar tjänsten returnerar ”503 Server upptagen” eller ”tidsgräns för 500 åtgärd” felkoder för vissa lagringstransaktioner. Det här avsnittet beskrivs både den allmänna metoden för att ta itu med skalbarhetsmål och bandbredd skalbarhetsmål särskilt. Senare avsnitt som handlar om enskilda lagringstjänster beskrivs skalbarhetsmål i kontexten för den specifika tjänsten:  

* [BLOB-bandbredd och begäranden per sekund](#subheading16)
* [Tabellenheter per sekund](#subheading24)
* [Kömeddelanden per sekund](#subheading39)  

#### <a name="sub1bandwidth"></a>Bandbredd Skalbarhetsmålen för alla tjänster
Vid tidpunkten som skrivs är bandbredd mål i USA för ett konto med geo-redundant lagring (GRS) 10 Gigabit per sekund (Gbps) för ingress (data som skickas till storage-kontot) och 20 Gbit/s för utgående trafik (data som skickas från storage-konto). För ett konto med lokalt redundant lagring (LRS) gränserna är högre – 20 Gbit/s för ingångshändelser och 30 Gbit/s för utgående trafik.  Internationella bandbreddsgränser kan vara lägre och finns på vår [skalbarhet mål sidan](https://msdn.microsoft.com/library/azure/dn249410.aspx).  Mer information om redundansalternativ för lagring finns i länkarna i användbara resurser nedan.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>Vad gör närmar sig ett mål för skalbarhet
Om gränsen på storage-konton som du kan ha i en viss prenumeration/region kombination närmar, utvärdera dina program och användningen av storage-konton och avgör om något av dessa villkor gäller.

* Använda storage-konton som ohanterade diskar och lägga till diskarna till dina virtuella datorer. I det här scenariot, bör du använda [hanterade diskar](../../virtual-machines/windows/managed-disks-overview.md), som de hanterar skalbarhet för lagring disken åt dig utan att du behöver att skapa och hantera enskilda storage-konton.
* Med hjälp av ett lagringskonto på basis av per kund, i syfte att isolering av data. I det här scenariot bör du använda behållare för varje kund i stället för ett hela storage-konto. Azure Storage nu kan du ange rollbaserad åtkomstkontroll på en per [behållare basis](storage-auth-aad-rbac-portal.md).
* Använda flera lagringskonton till fragment för bättre skalbarhet för ingående/utgående/iops/kapacitet. I det här scenariot, om möjligt rekommenderar vi du dra nytta av den [ökade gränser](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) för standard storage-konton för att minska antalet lagringskonton som krävs för din arbetsbelastning.

Om ditt program närmar sig det för skalbarhetsmål för ett enda lagringskonto, bör du börja använda någon av följande metoder:  

* Ändra den arbetsbelastning som gör att ditt program till närmar sig eller överskrider skalbarhetsmålen. Kan du utforma på olika sätt att använda mindre bandbredd eller kapacitet eller färre transaktioner?
* Om ett program måste överstiger ett av målen för skalbarhet, bör du skapa flera lagringskonton och partitionera dina programdata på dessa flera lagringskonton. Om du använder det här mönstret kan vara noga med att designa programmet så att du kan lägga till fler lagringskonton i framtiden för belastningsutjämning. Varje Azure-prenumeration kan ha upp till 100 lagringskonton vid tidpunkten för skrivning.  Storage-konton har också utan kostnad än din användning när det gäller data som lagras, transaktioner som har gjorts eller data som överförs.
* Om ditt program når bandbredd mål, bör du du genom att komprimera data i klienten för att minska den bandbredd som krävs för att skicka data till lagringstjänsten.  Även om det här kan spara bandbredd och förbättra nätverkets prestanda, kan den också ha några negativa konsekvenser.  Du ska utvärdera prestanda påverkas av detta på grund av ytterligare bearbetning-kraven för att komprimera och expandera data i klienten. Dessutom kan kan lagra komprimerade data göra det svårare att felsöka problem Eftersom det kan vara svårare att visa lagrade data med standardverktyg.
* Om ditt program når det för skalbarhetsmål, ser du till att du använder en exponentiell backoff för återförsök (se [återförsök](#subheading14)).  Det är bättre att se till att du aldrig itu med det för skalbarhetsmål (med hjälp av någon av metoderna ovan), men det säkerställer att ditt program inte bara antalet tillåtna nedladdningsförsök snabbt, vilket gör den begränsning sämre resultat.  

#### <a name="useful-resources"></a>Användbara resurser
Följande länkar ger ytterligare information på skalbarhetsmål:

* Se [skalbarhet för lagring av Azure- och prestandamål](storage-scalability-targets.md) information om skalbarhetsmål.
* Se [Azure Storage-replikering](storage-redundancy.md) och i blogginlägget [redundansalternativ för Azure Storage och Read Access Geo Redundant Storage](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) information om alternativ för lagringsredundans.
* Aktuell information om priser för Azure-tjänster finns i [prissättning för Azure](https://azure.microsoft.com/pricing/overview/).  

### <a name="subheading47"></a>Namngivningskonventionen
Azure Storage använder ett intervallbaserat partitioneringsschema för skalning och belastningsutjämning i systemet. Partitionsnyckeln används för att partitionera data i intervall och dessa områden är Utjämning av nätverksbelastning i systemet. Det innebär att namngivningskonventioner, till exempel lexikal ordning (till exempel msftpayroll, msftperformance, msftemployees osv.) eller med tidsstämplar (log20160101, log20160102, log20160102 osv.) kommer lånar ut sig själv till partitioner som potentiellt samplaceras på den samma partition servern i tills en belastningsutjämning åtgärd delar upp dem ut i mindre intervall. Alla blobbar i en behållare kan till exempel betjänas av en enskild server tills belastningen på dessa blobar kräver ytterligare ombalansering av intervallen som partitionen. På samma sätt kan en grupp med lågt belastade konton med deras namn i lexikal ordning kan betjänas av en enskild server tills belastningen på en eller alla dessa konton kräver att användaren ska delas upp på flera partitioner servrar. Varje åtgärd för belastningsutjämning kan påverka svarstiden för lagring anrop under åtgärden. Systemets förmåga att hantera en plötslig av trafik till en partition är begränsat av skalbarheten i en enda partition server förrän åtgärden belastningsutjämning sparkar i och balanserar partitionsnyckelintervall.  

Du kan följa några Metodtips för att minska frekvensen för sådana åtgärder.  

* Granska den namngivningskonvention som du använder för konton, behållare, blobbar, tabeller och köer, nära. Du skapar prefix kontonamn med ett 3-siffriga hash-värde med hjälp av en hash-funktion som bäst passar dina behov.  
* Om du ordnar dina data med tidsstämplar eller numerisk identifierare som du behöver se till att du inte använder en lagringsplats för endast (eller bara Lägg till åtkomstgruppen) trafikmönster. Dessa mönster lämpar sig inte för en rad-baserade partitionering system och kan leda till all trafik som kommer att en enda partition och begränsa systemet från effektivt belastningsutjämning. Till exempel om du har dagliga åtgärder som använder ett blobbobjekt med en tidsstämpel, till exempel ÅÅÅÅMMDD dirigeras sedan all trafik för den dagliga driften till ett enda objekt som hanteras av en enda partition-server. Titta på om den blob-gränser per per partition gränser uppfyller dina behov och kan du dela den här åtgärden i flera blobar om det behövs. På samma sätt om du sparar time series-data i dina tabeller, dirigeras alla trafiken kan till den senaste del av viktiga namnområdet. Om du måste använda tidsstämplar eller numeriska ID: N, prefix prefix ID: T med ett 3-siffriga hash-värde eller när det gäller tidsstämplar sekunder en del av tiden, till exempel ssyyyymmdd. Visa och undersöka åtgärder utförs regelbundet, väljer du en hash-funktion som begränsar din antalet frågor. I annat fall kanske ett slumpmässigt prefix är tillräckligt.  
* Mer information om partitioneringsschemat används i Azure Storage finns SOSP paper [här](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

### <a name="networking"></a>Nätverk
Även om bara innehåller anrop till API: et kan har de fysiska begränsningarna för programmet ofta en betydande inverkan på prestanda. Nedan beskrivs några av begränsningar som användarna kan stöta på.  

#### <a name="client-network-capability"></a>Nätverkskapacitet för klienten
##### <a name="subheading2"></a>Dataflöde
Problemet är ofta funktionerna i klienten för bandbredd. Till exempel när ett enda lagringskonto kan hantera 10 Gbit/s eller flera av inkommande (se [bandbredd skalbarhetsmål](#sub1bandwidth)), nätverkshastigheten i en Arbetsroll ”små” Azure-instans kan bara cirka 100 Mbit/s. Större Azure-instanser har nätverkskort med större kapacitet, så du bör överväga att använda en större instans eller fler virtuella datorer om du behöver högre gränser för nätverk från en enda dator. Om du får åtkomst till en tjänst från ett på lokala program och sedan samma sak gäller: Förstå nätverksfunktioner av klientenheten och nätverksanslutningen till Azure Storage-plats och antingen förbättra dem efter behov eller utforma din programmet att fungera inom deras funktioner.  

##### <a name="subheading3"></a>Länkkvalitet
Precis som med alla nätverksanvändning, Tänk på att nätverkets tillstånd, vilket resulterar i fel och paketförlust kommer att bli långsammare effektiva dataflöde.  Med hjälp av WireShark eller NetMon kan hjälpa att diagnosticera problemet.  

##### <a name="useful-resources"></a>Användbara resurser
Läs mer om storlekar för virtuella datorer och tilldelad bandbredd, [Windows VM-storlekar](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) eller [Linux VM-storlekar](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

#### <a name="subheading4"></a>plats
I alla distribuerade miljöer levererar placerar klienten nära servern i bästa möjliga prestanda. För att komma åt Azure Storage med kortast svarstid, är den bästa platsen för din klient inom samma Azure-region. Till exempel om du har en Azure-webbplats som använder Azure Storage kan du söka efter dem båda inom en enda region (till exempel USA, Väst eller Sydostasien). Detta minskar svarstiden och kostnaden – vid tidpunkten för skrivning bandbreddsanvändning inom en enda region är kostnadsfri.  

Om klienten program inte ligger inom Azure (till exempel appar för mobila enheter eller tjänster på plats enterprise), sedan igen att storage-konto i en region nära de enheter som kommer åt den, kommer vanligtvis minska svarstiden. Om klienterna distribueras brett (till exempel, en del i Nordamerika och vissa i Europa), så du bör överväga att använda flera lagringskonton: en finns i Nordamerika-region och en i en europeisk region. På så sätt att svarstiden minskas för användare i båda regionerna. Den här metoden är enklare att implementera om programmet datalagren är specifik för enskilda användare och kräver inte replikera data mellan lagringskonton.  Ett CDN rekommenderas för bred innehållsdistribution – finns i nästa avsnitt för mer information.  

### <a name="subheading5"></a>Innehållsdistribution
Ibland kan behöver ett program visa samma innehåll till många användare (till exempel en produkt demonstrationsvideon används i startsidan till en webbplats), finns i samma eller flera regioner. I det här scenariot, bör du använda en Content Delivery Network (CDN), till exempel Azure CDN och CDN använder Azure storage som ursprung av data. Till skillnad från ett Azure Storage-konto som finns i en enda region och som det går inte att leverera innehåll med låg latens till andra regioner, använder Azure CDN-servrar i flera Datacenter runtom i världen. Dessutom stöder ett CDN normalt mycket högre utgående gränser än ett enda lagringskonto.  

Läs mer om Azure CDN [Azure CDN](https://azure.microsoft.com/services/cdn/).  

### <a name="subheading6"></a>Med hjälp av SAS och CORS
När du behöver auktorisera koden, till exempel JavaScript i en användares webbläsare eller en mobiltelefon-app för att komma åt data i Azure Storage, en metod är att använda ett program i web-roll som en proxy: användarens enhet autentiseras med web-roll , som i sin tur auktoriserar åtkomst till lagringsresurser. På så vis undviker du exponera dina lagringskontonycklar på osäkert enheter. Men innebär detta stor belastning på webbrollen eftersom alla data som överförs mellan användarens enhet och lagringstjänsten måste passera webbrollen. Du kan också använda en webbroll som proxy för storage-tjänsten med hjälp av signaturer för delad åtkomst (SAS), ibland tillsammans med rubriker om Cross-Origin Resource Sharing (CORS). Med SAS kan tillåta du användarens enhet att göra begäranden direkt till en lagringstjänst med hjälp av en begränsad åtkomst-token. Till exempel om en användare vill ladda upp ett foto till ditt program, kan web-roll generera och skicka till användarens enhet en SAS-token som ger behörighet att skriva till en specifik blob eller behållare för de närmsta 30 minuterna (efter vilken SAS-token upphör att gälla).

Normalt tillåter en webbläsare inte JavaScript i en sida som värd för en webbplats på en domän för att utföra specifika åtgärder, till exempel en ”PLACERA” till en annan domän. Om du vara värd för en webbroll på ”contosomarketing.cloudapp.net” och vill använda JavaScript för klientsidan för att ladda upp en blob till ditt storage-konto på ”contosoproducts.blob.core.windows.net”, kommer webbläsarna som ”princip om samma ursprung” förbjuda den här åtgärden. CORS är en webbläsarfunktion som gör måldomänen (i det här fallet lagringskontot) för att kommunicera med webbläsaren att den litar på förfrågningar som kommer i källdomänen (i det här fallet webbrollen).  

Båda dessa tekniker kan hjälpa dig att undvika onödig belastning (och flaskhalsar) på ditt webbprogram.  

#### <a name="useful-resources"></a>Användbara resurser
Mer information om SAS finns i [signaturer för delad åtkomst, del 1: Förstå SAS-modellen](../storage-dotnet-shared-access-signature-part-1.md).  

Läs mer om CORS [Cross-Origin Resource Sharing (CORS) Support för Azure Storage-tjänster](https://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Cachelagring
#### <a name="subheading7"></a>Hämta Data
I allmänhet är hämtar data från en tjänst en gång bättre än att få den två gånger. Studera exemplet med en MVC-webbprogram som körs i en webbroll som redan har hämtat en 50 MB blob från storage-tjänst som fungerar som innehåll till en användare. Programmet kan sedan hämta den samma blobben varje gång en användare begär det eller den cachelagra den lokalt till hårddisken och återanvända den cachelagrade versionen för efterföljande användarbegäranden. Dessutom när en användare begär data, programmet kan problemet komma med en villkorlig rubrik för ändring av tiden, vilket skulle undvika att få hela blobben om den inte har ändrats. Du kan använda det här samma mönster för att arbeta med tabellentiteter.  

I vissa fall kan välja du att ditt program kan anta att blobben förblir giltigt under en kort period efter hämtning det och att under denna period programmet inte behöver kontrollera om bloben har ändrats.

Konfiguration, sökning och andra data som används alltid av programmet är bra kandidater för cachelagring.  

Ett exempel på hur du kan hämta en blob-egenskaper för att identifiera senaste ändringsdatum med hjälp av .NET finns i [Set och hämta egenskaper och Metadata](../blobs/storage-properties-metadata.md). Läs mer om villkorlig nedladdningar [villkorligt uppdatera en lokal kopia av en Blob](https://msdn.microsoft.com/library/azure/dd179371.aspx).  

#### <a name="subheading8"></a>Ladda upp Data i batchar
I vissa Programscenarier du aggregera data lokalt och sedan regelbundet ladda upp den i en grupp i stället för att ladda upp varje datadel omedelbart. Till exempel ett program kan hålla en loggfil av aktiviteter: programmet kan antingen ladda upp information om varje aktivitet, när det händer som en tabell och enhet (som kräver många lagringsåtgärder) eller Aktivitetsinformation bespara till en lokal loggfil och sedan ladda upp alla aktivitetsinformation regelbundet som en avgränsad fil till en blob. Om varje loggpost är 1 KB i storlek, kan du ladda upp tusentals i en enda ”placera Blob”-transaktion (du kan ladda upp en blob med upp till 64 MB i storlek i en enskild transaktion). Om den lokala datorn går sönder före överföringen förlorar du eventuellt vissa loggdata: programutvecklaren måste utforma möjlighet till klientenheten eller ladda upp fel.  Om aktivitetsdata ska hämtas för tidsintervallen (inte bara enkel aktivitet), bör blobar över tabeller.

### <a name="net-configuration"></a>.NET Configuration
Om du använder .NET Framework, innehåller det här avsnittet flera snabbt vill konfigurera inställningar som du kan använda för att göra betydande prestandaförbättringar.  Kontrollera om liknande koncept gäller i ditt valda språk om du använder andra språk.  

#### <a name="subheading9"></a>Öka Standardgränsen för anslutning
I .NET ökar följande kod anslutning Standardgränsen (som vanligtvis är 2 i en klientmiljö med eller 10 i en servermiljö) och 100. Normalt bör du ange värdet för ungefär hur många trådar som används av ditt program.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Du måste ange anslutningsgränsen innan du öppnar några anslutningar.  

Andra programmeringsspråk, finns i dokumentationen för respektive språk att fastställa hur du ställer in anslutningsgränsen.  

Mer information finns i bloggposten [webbtjänster: Samtidiga anslutningar](https://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx).  

#### <a name="subheading10"></a>Öka ThreadPool Min Threads om använder synkron kod med asynkrona åtgärder
Den här koden ökar tråd pool min trådar:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Mer information finns i [ThreadPool.SetMinThreads metoden](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="subheading11"></a>Dra nytta av .NET 4.5 skräpinsamling
Använda .NET 4.5 eller senare för klientprogrammet för att dra nytta av prestandaförbättringar i server skräpinsamling.

Mer information finns i artikeln [en översikt av prestandaförbättringar i .NET 4.5](https://msdn.microsoft.com/magazine/hh882452.aspx).  

### <a name="subheading12"></a>Obegränsade parallellitet
Parallellitet kan vara bra för prestanda, var försiktig med obegränsade parallellitet (ingen gräns för antalet trådar och/eller parallella förfrågningar) för att överföra eller hämta data med flera arbetare för att få åtkomst till flera partitioner (behållare, köer, eller tabellpartitioner) i samma lagringskonto eller att få åtkomst till flera objekt i samma partition. Om parallellitet obundna, ditt program kan överstiga klienten enhetens funktioner eller lagringskontots skalbarhet riktar sig mot vilket resulterar i längre svarstider och begränsning.  

### <a name="subheading13"></a>Storage-klientbibliotek och verktyg
Använd alltid den senaste av Microsoft tillhandahållna klientbibliotek och verktyg. Vid tidpunkten för skrivning finns klientbibliotek som är tillgängliga för .NET, Windows Phone, Windows Runtime, Java och C++ samt preview-bibliotek för andra språk. Microsoft har också publicerat PowerShell-cmdletar och Azure CLI-kommandon för att arbeta med Azure Storage. Microsoft aktivt utvecklar dessa verktyg med prestanda, håller dem uppdaterade med de senaste versionerna för tjänsten och garanterar de hanterar många av de beprövade prestanda internt.  

### <a name="retries"></a>Antal försök
#### <a name="subheading14"></a>Begränsning/ServerBusy
I vissa fall kan tjänsten storage kan begränsa ditt program eller kan helt enkelt inte hantera begäran på grund av vissa tillfälliga villkor och returnerar ett meddelande om ”503 Server upptagen” eller ”500 löper ut”.  Detta kan inträffa om programmet närmar sig något av det för skalbarhetsmål, eller om systemet är ombalansering av dina partitionerade data för högre dataflöde.  Klientprogrammet bör vanligtvis försök som orsakar ett sådant fel: försök samma begäran senare kan lyckas. Men om lagringstjänsten begränsar ditt program eftersom det överskrider skalbarhetsmål, eller även om tjänsten kunde inte hantera begäran av någon anledning, att aggressiva återförsök vanligtvis problemet sämre. Därför bör du använda en exponentiell backoff (klienten bibliotek standard till det här beteendet). Exempelvis kan programmet försöka igen efter 2 sekunder, och sedan 4 sekunder sedan 10 sekunder sedan 30 sekunder och ger sedan upp helt. Detta resulterar i ditt program avsevärt minska belastningen på tjänsten i stället exacerbating eventuella problem.  

Anslutningsfel kan göras direkt, eftersom de inte är resultatet av begränsning och förväntas vara tillfälligt.  

#### <a name="subheading15"></a>Icke-Återförsöksbar fel
Klientbiblioteken är medvetna om vilka fel återförsök kan och som inte är. Men om du skriver din egen kod mot storage REST API, Kom ihåg att det finns några fel som du inte kan försöka: till exempel en 400 (felaktig begäran) som svaret anger att klientprogrammet har skickat en begäran som inte kunde bearbetas eftersom det inte i en förväntad form. Skicka den här begäran resulterar samma svar varje gång, så ingen finns det försöker den. Om du skriver din egen kod mot storage REST API, känna till vad de betyder och på rätt sätt och försök igen (eller inte) för var och en av dem.  

#### <a name="useful-resources"></a>Användbara resurser
Mer information om felkoder för lagring finns i [Status och felkoder](https://msdn.microsoft.com/library/azure/dd179382.aspx) på webbplatsen Microsoft Azure.  

## <a name="blobs"></a>Blobar
Förutom beprövade metoder för [alla tjänster](#allservices) som beskrivits tidigare följande metodtips som gäller specifikt för blob-tjänsten.  

### <a name="blob-specific-scalability-targets"></a>Skalbarhetsmål för BLOB-specifika
#### <a name="subheading46"></a>Flera klienter som ansluter till ett enda objekt samtidigt
Om du har ett stort antal klienter som ansluter till ett enda objekt samtidigt, behöver du överväga per skalbarhetsmål för objektet och lagring. Det exakta antalet klienter som har åtkomst till ett enda objekt kan variera beroende på faktorer som hur många klienter som begär objektet samtidigt, storleken på objektet, nätverk villkor osv.

Om objektet kan distribueras via ett nätverk för Innehållsleverans, till exempel bilder eller videofiler som hanteras från en webbplats och du bör använda ett CDN. Se [här](#subheading5).

I andra scenarier, till exempel vetenskapliga simuleringar där uppgiften är konfidentiell har du två alternativ. Först är att sprida ut din arbetsbelastning åtkomst så att komma åt objektet under en period på jämfört ifråga samtidigt. Du kan också tillfälligt kopiera objektet till flera lagringskonton, vilket ökar det totala antalet IOPS per objekt och över lagringskonton. I begränsad testning, vi har upptäckt att cirka 25 virtuella datorer samtidigt kan ladda ned en blob på 100 GB parallellt (varje virtuell dator parallellisera nedladdningen med 32 trådar). Om du har 100 klienter behöver komma åt objektet, först kopiera den till ett andra storage-konto och sedan låta de första 50 virtuella datorer åt den första blobben och de andra 50 virtuella datorerna åt andra blob. Resultaten kan variera beroende på din program-beteende så bör du testa under design. 

#### <a name="subheading16"></a>Bandbredd och -åtgärder per Blob
Du kan läsa eller skriva till en enda blob på upp till 60 MB per sekund (det är ungefär 480 Mbit/s, vilket överskrider kapaciteterna för många klientside-nätverk (inklusive det fysiska nätverkskortet på klientenheten). Dessutom kan en enda blob har stöd för upp till 500 begäranden per sekund. Om du har flera klienter som behöver läsa samma blob och du kan överskrida gränserna, bör du använda ett CDN för att distribuera blob.  

Läs mer om måldataflöde för blobar, [skalbarhet för lagring av Azure- och prestandamål](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Kopiera och flytta Blobar
#### <a name="subheading17"></a>Kopiera Blob
Storage REST-API version 2012-02-12 introducerades användbara möjligheten att kopiera BLOB-konton: ett klientprogram kan instruera tjänsten storage för att kopiera en blob från en annan källa (eventuellt i ett annat lagringskonto) och låt tjänsten utför kopian asynkront. Detta kan avsevärt minska den bandbredd som krävs för programmet när du migrerar data från andra lagringskonton eftersom du inte behöver hämta och överföra data.  

En övervägande är dock att när du kopierar mellan lagringskonton, det finns ingen tid garanti på när kopieringen slutförs. Om programmet behöver för att slutföra en blob-kopia snabbt under din kontroll, kan det vara bättre att kopiera blobben genom att hämta den till en virtuell dator och överföra den till målet.  Se till att kopian utförs av en virtuell dator som körs i samma Azure-region, annars nätverksförhållanden kan (och troligtvis kommer) påverkar din kopieringen bättre prestanda för fullständig förutsägbarhet i så fall.  Dessutom kan du övervaka förloppet för en asynkron kopia programmässigt.  

Kopior inom samma lagringskonto själva Allmänt slutförs snabbt.  

Mer information finns i [kopiering av Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="subheading18"></a>Använda AzCopy
Azure Storage-teamet har publicerat ett kommandoradsverktyg ”AzCopy” som är avsedd för att underlätta massinläsning överföra många blobar att från och över lagringskonton.  Det här verktyget är optimerad för det här scenariot och kan uppnå hög överföringshastighet.  Användning rekommenderas för bulköverföring, ladda ned och kopiera scenarier. Om du vill lära dig mer om den och ladda ned den, se [överföra data med kommandoradsverktyget Azcopy](storage-use-azcopy.md).  

#### <a name="subheading19"></a>Tjänsten Azure Import/Export
För stora mängder data (mer än 1 TB) erbjuder Azure Storage Import/Export-tjänsten, som kan användas för uppladdning och nedladdning från blob storage med endash hårddiskar.  Du kan placera dina data på en hårddisk och skicka denna till Microsoft för att ladda upp eller skicka en tom hårddisk till Microsoft för att hämta data.  Mer information finns i [Använda tjänsten Microsoft Azure Import/Export för att överföra data till Blob Storage](../storage-import-export-service.md).  Detta kan vara mycket mer effektivt än att ladda upp/ned den här mängden data över nätverket.  

### <a name="subheading20"></a>Använda metadata
Blob-tjänsten stöder head-begäranden som kan innehålla metadata om bloben. Om ditt program behövs EXIF-data från ett foto, kan den hämta fotot och extrahera den. För att spara bandbredd och förbättra prestanda, kan programmet lagra EXIF-data i den blob-metadata när programmet har laddat upp fotot: du kan sedan hämta EXIF-data i metadata med hjälp av endast en HEAD-begäran, spara betydande bandbredd och Bearbetningstid som krävs för att extrahera EXIF-data varje gång blobben läses. Detta är användbart i scenarier där du behöver bara metadata och inte det fullständiga innehållet på en blob.  Endast 8 KB av metadata kan lagras per blob (tjänsten inte kommer acceptera en begäran om att store mer än som), så om data inte passar i den storleken du inte kanske kan använda den här metoden.  

Ett exempel på hur du kan hämta en blob-metadata med hjälp av .NET finns i [Set och hämta egenskaper och Metadata](../blobs/storage-properties-metadata.md).  

### <a name="uploading-fast"></a>Ladda upp snabbt
För att ladda upp blobar snabbt svara på den första frågan är: är du laddar upp en blob eller många?  Använd den nedan för att fastställa rätt metod för att använda beroende på ditt scenario.  

#### <a name="subheading21"></a>Ladda upp en stor blob snabbt
Om du vill överföra en enda stor blob snabbt bör klientprogrammet ladda upp sin block eller sidor parallellt (att beakta också kolumnerna skalbarhetsmål för enskilda blobar och storage-konto som helhet).  De officiella Microsoft tillhandahåller RTM Storage-klientbibliotek (.NET, Java) har möjlighet att göra detta.  För var och en av bibliotek som använder den under angivna objektegenskap att ställa in samtidighet:  

* .NET: Ange ParallelOperationThreadCount för ett BlobRequestOptions som ska användas.
* Java/Android: Use BlobRequestOptions.setConcurrentRequestCount()
* Node.js: Använd parallelOperationThreadCount alternativen begäran eller blobtjänsten.
* C++: Använd metoden blob_request_options::set_parallelism_factor.

#### <a name="subheading22"></a>Ladda upp många blobar snabbt
Ladda upp blobar parallellt för att ladda upp många blobar snabbt. Det här är snabbare än att överföra enda blobar i taget med parallella block uppladdningar eftersom det sprids överföringen över flera partitioner av storage-tjänsten. En enda blob har endast stöd för ett dataflöde på 60 MB per sekund (cirka 480 Mbit/s). Vid tidpunkten som skrivs ett amerikanska LRS-konto har stöd för upp till 20 Gbit/s ingångshändelser, vilket är större än det dataflöde som stöds av en enskild blob.  [AzCopy](#subheading18) utför uppladdningar parallellt som standard och rekommenderas för det här scenariot.  

### <a name="subheading23"></a>Välja rätt typ av blob
Azure Storage stöder två typer av blob: *sidan* blobar och *blockera* blobar. För en viss användningsscenariot påverkar ditt val av blobbtypen prestanda och skalbarhet för din lösning. Blockblobar är lämplig när du vill ladda upp stora datamängder effektivt: till exempel ett klientprogram kan behöva ladda upp foton eller video till blob storage. Sidblobar är lämpligt om programmet måste utföra slumpmässiga skrivningar på data: till exempel Azure VHD lagras som sidblobar.  

Mer information finns i [förstå Blockblobbar, Tilläggsblobbar och Sidblobbar](https://msdn.microsoft.com/library/azure/ee691964.aspx).  

## <a name="tables"></a>Tabeller
Förutom beprövade metoder för [alla tjänster](#allservices) som beskrivits tidigare följande metodtips som gäller specifikt för table service.  

### <a name="subheading24"></a>Tabell-specifika skalbarhetsmål
Tabeller har följande specifika skalbarhetsgränsen förutom bandbreddsbegränsningar för en hel storage-konto.  Systemet kommer belastningsutjämna som ökar din trafik, men om trafiken har plötsliga ökningar, kan du inte att kunna få den här volymen dataflödets direkt.  Om mönstret har ökningar, bör du förväntar dig att se begränsning och/eller timeout under burst som lagring tjänsten automatiskt belastningen saldo ut din tabell.  Av dig långsamt Allmänt har bättre resultat som den får systemtid att belastningsutjämna på rätt sätt.  

#### <a name="entities-per-second-account"></a>Enheter per sekund (konto)
Gräns för skalbarhet för att komma åt tabeller är upp till 20 000 entiteter (1 KB som är varje) per sekund för ett konto.  I allmänhet varje entitet som har infogats kan uppdateras, tas bort eller genomsöks räknas mot det här målet.  En gruppinfogning som innehåller 100 entiteter skulle så räknas som 100 entiteter.  En fråga som söker igenom 1000 entiteter och returnerar 5 räknas som 1000 enheter.  

#### <a name="entities-per-second-partition"></a>Enheter per sekund (Partition)
På en enda partition är skalbarhetsmålen för att komma åt tabeller 2 000 entiteter (1 KB som är varje) per sekund, med samma inventering enligt beskrivningen i föregående avsnitt.  

### <a name="configuration"></a>Konfiguration
Det här avsnittet innehåller flera snabb konfigurationsinställningar som du kan använda för att göra betydande prestandaförbättringar i tabelltjänsten:  

#### <a name="subheading25"></a>Använd JSON
Från och med storage service-version 2013-08-15, stöder table service användningen av JSON i stället för XML-baserade AtomPub-format för att överföra data från tabeller. Detta kan minska nyttolaststorlekar med så mycket som 75% och förbättrar programmets prestanda.

Mer information finns i inlägget [Microsoft Azure-tabeller: Introduktion till JSON](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) och [Nyttolastformatet för tabellen tjänståtgärder](https://msdn.microsoft.com/library/azure/dn535600.aspx).

#### <a name="subheading26"></a>Nagle av
Nagles algoritmen implementeras över TCP/IP-nätverk som ett sätt att förbättra nätverkets prestanda. Det är dock inte optimal i samtliga fall (till exempel interaktiva miljöer). Nagles algoritmen har en negativ inverkan på prestanda för förfrågningar till tabell och kö-tjänster för Azure Storage, och bör du inaktivera den om möjligt.

### <a name="schema"></a>Schema
Hur du representerar och skicka frågor till dina data är den största enda faktor som påverkar prestanda för tabelltjänsten. Varje program är olika, beskrivs det här avsnittet vissa allmänna beprövade metoder som är relaterade till:  

* Tabelldesign
* Effektiva frågor
* Effektiv datauppdateringar  

#### <a name="subheading27"></a>Tabeller och partitioner
Tabeller är indelade i partitioner. Varje entitet som lagras i en partition delar samma partitionsnyckel och har en unik rad för att identifiera den i partitionen. Partitioner ger fördelar, men medför också skalbarhetsbegränsningar.  

* Fördelar: Du kan uppdatera entiteter i samma partition i en enda,-atomiska, batch-transaktion som innehåller upp till 100 separat lagringsåtgärder (högst 4 MB total storlek). Anta att samma antal entiteter som ska hämtas och kan du också fråga data på en enda partition mer effektivt än data från flera partitioner (dock läsa på ytterligare rekommendationer för hämtar data från tabeller).
* Gräns för skalbarhet: Åtkomst till entiteter som lagras i en enda partition kan inte vara Utjämning av nätverksbelastning eftersom partitioner stöder Batchtransaktioner atomiska. Därför är skalbarhetsmålen för en enskild tabellpartition lägre än för tabelltjänsten som helhet.  

På grund av följande egenskaper för tabeller och partitioner bör du anta följande designprinciper:  

* Data som ditt klientprogram uppdateras ofta eller efterfrågas i samma logiska arbetsenheten måste finnas i samma partition.  Detta kan vara eftersom programmet sammanställer skrivningar eller eftersom du vill dra nytta av atomiska batchåtgärder.  Dessutom söka på en enda partition mer effektivt data i en enskild fråga än data över partitioner.
* Data som ditt klientprogram inte infoga/uppdatera eller frågan i samma logiska enhet av arbete (enskild fråga eller batchuppdatering) måste finnas i olika partitioner.  Ett viktigt är att det finns ingen gräns för antal partitionsnycklar i en enda tabell, så har miljontals partitionsnycklar är inte ett problem och påverkar inte prestanda.  Till exempel om programmet är en populär webbplats med användarinloggning, kan med hjälp av användar-ID som partitionsnyckel vara ett bra alternativ.  

#### <a name="hot-partitions"></a>Heta partitioner
En frekvent partition är en som tar emot en för stor andel av trafiken till ett konto och kan inte vara belastningsutjämnade eftersom det är en enda partition.  I allmänhet skapas heta partitioner på två sätt:  

##### <a name="subheading28"></a>Lägg endast till och Lägg till åtkomstgruppen endast mönster
Mönstret ”Lägg till bara” är en där alla (eller nästan alla) trafik till en viss PK ökar och minskar enligt den aktuella tiden.  Ett exempel är om ditt program används aktuellt datum som en partitionsnyckel för loggdata.  Detta resulterar i alla infogningar går till den senaste partition i tabellen och det går inte att läsa belastningsutjämna eftersom alla skrivningar kommer i slutet av din tabell.  Om mängden trafik till partitionen överskrider partition på servernivå skalbarhetsmålen, resulterar det i begränsning.  Är det bättre att se till att trafik skickas till flera partitioner, för att aktivera belastningsutjämning begäranden i din tabell.  

##### <a name="subheading29"></a>Hög trafik Data
Om partitioneringsschemat resulterar i en enda partition som bara innehåller data som används mycket mer än andra partitioner, kan du också se begränsning som att partitionen närmar sig skalbarhetsmålen för en enda partition.  Är det bättre att se till att din partitionsschema leder till att ingen enskild partition som närmar sig det för skalbarhetsmål.  

#### <a name="querying"></a>Fråga
Det här avsnittet beskrivs några beprövade metoder för att fråga table service.  

##### <a name="subheading30"></a>Omfång
Det finns flera sätt att ange en uppsättning entiteter för att fråga.  Här följer en beskrivning av användningen av var och en.  

I allmänhet undvika genomsökningar (frågor som är större än en enda entitet), men om du måste skannar, försök att ordna data så att dina skanningar hämta de data du behöver utan att skanna eller returnera stora mängder entiteter som du inte behöver.  

###### <a name="point-queries"></a>Punktfrågor
En punkt-fråga hämtar exakt en entitet. Det gör du genom att ange både partitionsnyckel och radnyckel för att hämta entiteten. De här frågorna är effektiva och du bör använda dem om möjligt.  

###### <a name="partition-queries"></a>Partition frågor
En partition fråga är en fråga som hämtar en datamängd som delar en gemensam partitionsnyckel. Frågan anger vanligtvis ett antal viktiga radvärden eller ett intervall med värden för vissa entitetsegenskap förutom en partitionsnyckel. Dessa är mindre effektivt än punktfrågor och bör användas sparsamt.  

###### <a name="table-queries"></a>Tabellen frågor
En tabellfråga är en fråga som hämtar en uppsättning entiteter som inte delar en gemensam partitionsnyckel. De här frågorna är inte effektiva och du bör undvika dem om möjligt.  

##### <a name="subheading31"></a>Fråga densitet
En annan viktig faktor i frågans effektiviteten är antalet enheter som returneras jämfört med antal entiteter som genomsöks för att hitta den returnerade uppsättningen. Om ditt program utför en tabellfråga med ett filter för ett egenskapsvärde som att endast 1% av dataresurser frågan söker igenom 100 entiteter för varje en entitet som returneras. Tabellen skalbarhetsmål som beskrivs tidigare alla avser antalet enheter som har genomsökts och inte antalet enheter som returneras: densitet frågor enkelt kan orsaka tabelltjänsten att begränsa ditt program eftersom det måste genomsöka så många entiteter att Hämta det entitet som du letar efter.  Se avsnittet nedan på [denormalisering](#subheading34) för mer information om hur du undviker detta.  

##### <a name="limiting-the-amount-of-data-returned"></a>Begränsa mängden Data som returneras
###### <a name="subheading32"></a>Filtrering
Överväg att använda ett filter för att minska storleken på den returnerade uppsättningen där du vet att en fråga returnerar entiteter som du inte behöver i klientprogrammet. Även om de enheter som inte returneras till klienten fortfarande tillgodoräknas skalbarhetsgränserna förbättras programprestanda på grund av nyttolast minskade nätverk och minskat antal entiteter som ditt klientprogram måste bearbeta.  Se över Observera på [fråga densitet](#subheading31), men – det för skalbarhetsmål avser antalet enheter som har genomsökts så att en fråga som filtrerar bort många entiteter kan fortfarande resultera i begränsningar, även om några entiteter returneras.  

###### <a name="subheading33"></a>Projektion
Om klientprogrammet behöver endast en begränsad uppsättning egenskaper från enheter i din tabell, kan du kan använda projektion för att begränsa storleken på returnerade datauppsättningen. Precis som med filtrering, detta är för att minska nätverksbelastningen och bearbetning av klienten.  

##### <a name="subheading34"></a>Denormalisering
Till skillnad från arbetar med relationsdatabaser, leda beprövade metoder för att effektivt fråga tabelldata till avnormalisera data. Det vill säga duplicera samma data på flera enheter (en för varje nyckel som du kan använda för att hitta data) att minimera antalet enheter som en fråga måste genomsöka för att hitta data klienten måste du, i stället för att skanna stort antal entiteter för att hitta data din app ogrampool måste.  Till exempel webbplatser för e-handel, du kanske vill hitta en beställning båda efter kund-ID (jag den här kundens order) och efter datumet (jag order på ett datum).  I Table Storage, är det bäst att lagra entiteten (eller en referens till den) två gånger – en gång med namnet på tabellen PK och Rksskrivare för att underlätta att hitta av kund-ID: T, en gång för att underlätta att söka efter datumet.  

#### <a name="insertupdatedelete"></a>Insert/Update/Delete
Det här avsnittet beskrivs några beprövade metoder för att ändra entiteter som lagras i tabelltjänsten.  

##### <a name="subheading35"></a>Batchbearbetning
Batchtransaktioner är kända som entiteten grupp transaktioner (ETG) i Azure-lagring. alla åtgärder i en ETG måste finnas på en enda partition i en enda tabell. Om möjligt, kan du använda ETGs för att utföra infogningar, uppdateringar och borttagningar i batchar. Detta minskar antalet sändningar från klientprogrammet till servern, minskar antalet faktureringsbara transaktioner (en ETG räknas som en enda transaktion för fakturering och kan innehålla upp till 100 lagringsåtgärder) och aktiverar atomiska uppdateringar (alla åtgärder lyckas eller misslyckas i en ETG). Miljöer med hög fördröjning, som mobila enheter kan dra avsevärt från att använda ETGs.  

##### <a name="subheading36"></a>Upsert
Användningstabell **Upsert** åtgärder när så är möjligt. Det finns två typer av **Upsert**, som kan vara effektivare än en traditionell **infoga** och **uppdatering** åtgärder:  

* **InsertOrMerge**: Använd det här alternativet när du vill ladda upp en delmängd av egenskaperna för den entiteten, men är osäker på om entiteten finns redan. Om entiteten finns det här anropet uppdaterar egenskaperna som ingår i den **Upsert** åtgärd, och lämnar alla befintliga egenskaper som de är, om entiteten inte finns, infogas den nya entiteten. Det här är ungefär som att använda projektion i en fråga, eftersom du behöver bara överför de egenskaper som ändrar.
* **InsertOrReplace**: Använd det här alternativet när du vill ladda upp en helt ny entitet, men du är osäker på om den redan finns. Du bör endast använda detta när du vet att den nyligen uppladdade entiteten är helt korrekt eftersom det helt skriver över den gamla entiteten. Exempelvis kan vill du uppdatera det entitet som lagrar användarens aktuella plats, oavsett om programmet har tidigare lagrade platsdata för användaren. den nya plats-entiteten är klar och du behöver inte någon information från föregående entiteter.

##### <a name="subheading37"></a>Lagra dataserien i en enda entitet
Ibland kan ett program lagrar en serie med data som ofta behöver hämta allt samtidigt: ett program kan till exempel Spåra processoranvändning över tid för att rita ett rullande diagram av data från de senaste 24 timmarna. En metod är att ha tabellen enheter per timme, med varje entitet som representerar en viss timme och lagrar CPU-användningen för den timmen. Om du vill rita ut dessa data, måste programmet hämta entiteter som innehåller data från de senaste 24 timmarna.  

Du kan också ditt program kan lagra CPU-användning för varje timme som en separat egenskap för en enda entitet: för att uppdatera varje timme, ditt program kan använda en enda **InsertOrMerge Upsert** anrop för att uppdatera värdet för den senaste timme. Om du vill rita data programmet endast måste hämta en enda entitet i stället för 24, vilket gör för en effektiv fråga (se ovan diskussion på [fråga omfång](#subheading30)).

##### <a name="subheading38"></a>Lagra strukturerade data i blobbar
Ibland strukturerade data känns som om det ska gå i tabeller, men intervallen för entiteter hämtas alltid tillsammans och kan batch infogas.  Ett bra exempel på detta är en loggfil.  I det här fallet kan du batch flera minuters loggar, infoga dem och sedan du hämtar alltid flera minuters loggar i taget samt.  I det här fallet för prestanda är det bättre att använda blobar i stället för tabeller, eftersom du kan avsevärt minska antalet objekt som har skrivits/returneras, samt vanligtvis antalet förfrågningar som behöver.  

## <a name="queues"></a>Köer
Förutom beprövade metoder för [alla tjänster](#allservices) som beskrivits tidigare följande metodtips som gäller specifikt för kötjänsten.  

### <a name="subheading39"></a>Skalbarhetsgränserna
En enskild kö kan bearbeta ungefär 2 000 meddelanden (1 KB som är varje) per sekund (som ett meddelande här AddMessage och GetMessage DeleteMessage räknas). Om det inte är tillräckligt för ditt program bör du använda flera köer och sprids dem meddelanden.  

Visa aktuella skalbarhetsmål på [skalbarhet för lagring av Azure- och prestandamål](storage-scalability-targets.md).  

### <a name="subheading40"></a>Nagle av
Se avsnittet om tabell-konfiguration som beskrivs algoritmen Nagle – Nagle-algoritmen är vanligtvis bra för prestanda för begäranden i kö och bör du inaktivera den.  

### <a name="subheading41"></a>Meddelandestorlek
Minska när meddelandet storlek ökar kö prestanda och skalbarhet. Du bör placera bara den information som behövs för mottagaren i ett meddelande.  

### <a name="subheading42"></a>Batch-hämtning
Du kan hämta upp till 32 meddelanden från en kö i en enda åtgärd. Detta kan minska antalet turer fram och tillbaka från klientprogrammet, vilket är särskilt användbart för miljöer, till exempel mobila enheter med lång svarstid.  

### <a name="subheading43"></a>Avsökningsintervall för kö
De flesta program att söka efter meddelanden från en kö som kan vara något av de största källorna för transaktioner för programmet. Välj din avsökningsintervallet motiverar: avsökningsintervall för ofta kan göra att ditt program att skalbarhetsmål för kön. På 200 000 transaktioner för $0.01 (vid tidpunkten för skrivning) är en enskild processor avsökning när varje sekund i en månad skulle kosta mindre än 15 cent så kostnad dock inte vanligtvis en faktor som påverkar ditt val av avsökningsintervall.  

Aktuell kostnadsinformation finns i [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="subheading44"></a>UpdateMessage
Du kan använda **UpdateMessage** att öka tidsgränsen för osynlighet eller för att uppdatera information om tillstånd för ett meddelande. Detta är kraftfulla, Kom ihåg att varje **UpdateMessage** åtgärden räknas mot skalbarhetsmålen. Detta kan dock vara ett mycket effektivare sätt än med ett arbetsflöde som skickar ett jobb från en kö till nästa, eftersom varje steg i jobbet har slutförts. Med hjälp av den **UpdateMessage** åtgärden tillåter programmet att spara jobbets status till meddelandet och sedan fortsätta arbeta i stället för requeuing meddelandet för nästa steg i jobbet varje gång ett steg har slutförts.  

Mer information finns i artikeln [så här: Ändra innehållet i ett meddelande i kön](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message).  

### <a name="subheading45"></a>Programarkitektur
Du bör använda köer för att göra din programarkitektur skalbara. Här nedan listas några sätt som du kan använda köer för att göra programmet mer skalbart:  

* Du kan använda köer för att skapa kvarvarande uppgifter för bearbetning och jämna ut arbetsbelastningar i ditt program. Du kan till exempel köa begäranden från användare för att utföra processorn arbetar, till exempel storleksändring av överförda bilder.
* Du kan använda köer för att frikoppla delar av ditt program så att du kan skala dem oberoende av varandra. Till exempel placera en frontwebb undersökningsresultatet från användare till en kö för senare analys och lagring. Du kan lägga till flera worker-rollinstanser för att bearbeta kö data vid behov.  

## <a name="conclusion"></a>Sammanfattning
Den här artikeln beskrivs några av de vanligaste och mest beprövade metoderna för prestandaoptimering när du använder Azure Storage. Vi rekommenderar att alla programutvecklare utvärderar sina program med hjälp av ovanstående metoder, och överväger att följa rekommendationerna för att få ut mesta möjliga av de program som använder sig av Azure-lagring.
