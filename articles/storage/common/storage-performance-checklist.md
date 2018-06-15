---
title: Azure Storage-prestanda och skalbarhet Checklista | Microsoft Docs
description: En checklista beprövade metoder för användning med Azure Storage i utvecklar performant program.
services: storage
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 959d831b-a4fd-4634-a646-0d2c0c462ef8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: rogarana
ms.openlocfilehash: 945289a172270eea56625287baf437fd4b70c7f3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
ms.locfileid: "30246227"
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>Prestanda och skalbarhetschecklista för Microsoft Azure Storage
## <a name="overview"></a>Översikt
Microsoft har utvecklat ett antal beprövade metoder för att använda dessa tjänster på ett sätt som performant efter utgivningen av Microsoft Azure Storage-tjänster, och den här artikeln används för konsolidera viktigaste av dem till en lista med checklista-format. Syftet med den här artikeln är att kontrollera de använder beprövade metoder med Azure Storage programutvecklare och hjälper dem att identifiera andra beprövade metoder som de bör införandet. Den här artikeln försöker inte att täcka alla möjliga optimering av prestanda och skalbarhet – den utesluter som är litet i deras inverkan eller brett ej tillämpligt. Att programmets beteende kan förutsägas under design, är det bra att behålla dem i åtanke för tidigt för att undvika design som ska köras i prestandaproblem.  

Varje programutvecklaren med hjälp av Azure Storage bör ta tid att läsa den här artikeln och kontrollera att hans eller hennes program följer varje beprövade metoder som anges nedan.  

## <a name="checklist"></a>Checklista
Den här artikeln organiserar beprövade metoder i följande grupper. Beprövade metoder som gäller för:  

* Alla Azure Storage-tjänster (blobbar, tabeller, köer och filer)
* Blobar
* Tabeller
* Köer  

| Klart | Område | Kategori | Fråga |
| --- | --- | --- | --- |
| &nbsp; | Alla tjänster |Skalbarhetsmål |[Är programmet som utformats för att undvika närmar sig skalbarhetsmål?](#subheading1) |
| &nbsp; | Alla tjänster |Skalbarhetsmål |[Din namngivningskonvention utformats för att bättre belastningsutjämning?](#subheading47) |
| &nbsp; | Alla tjänster |Nätverk |[Har sida klientenheter tillräckligt hög bandbredd och låg latens för att uppnå de prestanda som behövs?](#subheading2) |
| &nbsp; | Alla tjänster |Nätverk |[Har sida klientenheter en tillräckligt hög kvalitet länk?](#subheading3) |
| &nbsp; | Alla tjänster |Nätverk |[Klientprogrammet finns ”” storage-konto?](#subheading4) |
| &nbsp; | Alla tjänster |Innehållsdistribution |[Använder du en CDN för innehållsdistribution?](#subheading5) |
| &nbsp; | Alla tjänster |En klient direkt åtkomst |[Du använder SAS och CORS för direkt åtkomst till lagring i stället för proxy?](#subheading6) |
| &nbsp; | Alla tjänster |Cachelagring |[Är programmet cachelagring data används flera gånger och ändringar sällan?](#subheading7) |
| &nbsp; | Alla tjänster |Cachelagring |[Tillämpningsprogrammet är grupperade uppdateringar (cachelagring på klientsidan för dem och sedan ladda upp i större mängder)?](#subheading8) |
| &nbsp; | Alla tjänster |.NET-konfiguration |[Har du konfigurerat din klient om du vill använda ett tillräckligt antal samtidiga anslutningar?](#subheading9) |
| &nbsp; | Alla tjänster |.NET-konfiguration |[Har du konfigurerat .NET för att använda ett tillräckligt antal trådar?](#subheading10) |
| &nbsp; | Alla tjänster |.NET-konfiguration |[Du använder .NET 4.5 eller senare som har förbättrats skräpinsamling?](#subheading11) |
| &nbsp; | Alla tjänster |Parallellitet |[Har du sett till att parallellitet begränsas på lämpligt sätt så att du inte överlagra klientens kapacitet eller skalbarhetsmål?](#subheading12) |
| &nbsp; | Alla tjänster |Verktyg |[Är du med hjälp av den senaste versionen av Microsoft tillhandahålls klientbibliotek och verktyg?](#subheading13) |
| &nbsp; | Alla tjänster |Antal försök |[Är du med hjälp av en exponentiell backoff försök principer för begränsning av fel och tidsgränser?](#subheading14) |
| &nbsp; | Alla tjänster |Antal försök |[Är programmet Undvik nya försök för icke-återförsökbart fel?](#subheading15) |
| &nbsp; | Blobar |Skalbarhetsmål |[Har du ett stort antal klienter som ansluter till ett enda objekt samtidigt?](#subheading46) |
| &nbsp; | Blobar |Skalbarhetsmål |[Är programmet dig inom bandbredd eller åtgärder skalbarhet mål för en enda blob?](#subheading16) |
| &nbsp; | Blobar |Kopiera BLOB |[Vill du kopiera BLOB på ett effektivt sätt?](#subheading17) |
| &nbsp; | Blobar |Kopiera BLOB |[Använder du AzCopy för bulk kopior av blobbar?](#subheading18) |
| &nbsp; | Blobar |Kopiera BLOB |[Använder du Azure Import/Export för att överföra stora mängder data?](#subheading19) |
| &nbsp; | Blobar |Använda Metadata |[Lagrar du vanliga metadata om blobbar i sina metadata?](#subheading20) |
| &nbsp; | Blobar |Snabb överföring |[När du försöker överföra en blob snabbt du överför block parallellt?](#subheading21) |
| &nbsp; | Blobar |Snabb överföring |[När du försöker överföra många blobbar snabbt du överför blobbar parallellt?](#subheading22) |
| &nbsp; | Blobar |Korrigera Blobbtypen |[Du använder sidblobbar eller blockblobbar när det är lämpligt?](#subheading23) |
| &nbsp; | Tabeller |Skalbarhetsmål |[Du närmar dig skalbarhetsmål för entiteter per sekund?](#subheading24) |
| &nbsp; | Tabeller |Konfiguration |[Använder du JSON för tabell-begäranden?](#subheading25) |
| &nbsp; | Tabeller |Konfiguration |[Har du inaktiverat Nagle kan förbättra prestanda för små begäran?](#subheading26) |
| &nbsp; | Tabeller |Tabeller och partitioner |[Har du rätt partitionerat data?](#subheading27) |
| &nbsp; | Tabeller |Varm partitioner |[Är du undvika Lägg endast och endast lägga mönster?](#subheading28) |
| &nbsp; | Tabeller |Varm partitioner |[Infogningar/uppdateringar är fördelade på flera partitioner?](#subheading29) |
| &nbsp; | Tabeller |Frågeomfattningen |[Har du skapat schemat för punkt frågor som ska användas i de flesta fall och tabellen frågor som ska användas sparsamt?](#subheading30) |
| &nbsp; | Tabeller |Frågan densitet |[Gör dina frågor vanligtvis endast genomsökning och returnerar rader som ska använda för ditt program?](#subheading31) |
| &nbsp; | Tabeller |Begränsa returnerade Data |[Du använder filtrering för att undvika att enheter som inte behövs?](#subheading32) |
| &nbsp; | Tabeller |Begränsa returnerade Data |[Använder du projektion för att undvika att egenskaper som inte behövs?](#subheading33) |
| &nbsp; | Tabeller |Denormalization |[Har du Avnormaliserade dina data så att du undviker ineffektiva frågor eller flera läsbegäranden vid försök att hämta data?](#subheading34) |
| &nbsp; | Tabeller |Insert/Update/Delete |[Är du batchbearbetning begäranden som måste vara en transaktionskö eller kan göras samtidigt minska turer?](#subheading35) |
| &nbsp; | Tabeller |Insert/Update/Delete |[Är du undvika att hämta en entitet bara för att avgöra om att anropa insert eller update?](#subheading36) |
| &nbsp; | Tabeller |Insert/Update/Delete |[Har du funderat lagra serien med data som hämtas ofta tillsammans i en enda enhet som egenskaper i stället för flera enheter?](#subheading37) |
| &nbsp; | Tabeller |Insert/Update/Delete |[För enheter som hämtas alltid tillsammans och kan skrivas i batchar (t.ex. serien tidsdata), har du funderat med blobbar i stället för tabeller?](#subheading38) |
| &nbsp; | Köer |Skalbarhetsmål |[Du närmar dig skalbarhetsmål för meddelanden per sekund?](#subheading39) |
| &nbsp; | Köer |Konfiguration |[Har du inaktiverat Nagle kan förbättra prestanda för små begäran?](#subheading40) |
| &nbsp; | Köer |Meddelandestorlek |[Är meddelanden-CD för att förbättra prestanda i kön?](#subheading41) |
| &nbsp; | Köer |Hämta bulk |[Hämtar du flera meddelanden i en enda åtgärd ”hämta”?](#subheading42) |
| &nbsp; | Köer |Avsökningsfrekvens |[Är du avsökning så ofta för att minska upplevd svarstiden för ditt program?](#subheading43) |
| &nbsp; | Köer |Uppdatera meddelande |[Använder du UpdateMessage för att lagra förlopp i behandlar meddelanden undvika att behöva Ombearbeta hela meddelandet om ett fel inträffar?](#subheading44) |
| &nbsp; | Köer |Arkitektur |[Använder du köer för att göra hela programmet mer skalbar genom att hålla tidskrävande arbetsbelastningar utanför den kritiska linjen och skala sedan oberoende av varandra?](#subheading45) |

## <a name="allservices"></a>Alla tjänster
Det här avsnittet innehåller beprövade metoder som gäller för användning av någon av de Azure Storage-tjänsterna (blobbar, tabeller, köer eller filer).  

### <a name="subheading1"></a>Skalbarhetsmål
Varje Azure Storage-tjänster har skalbarhetsmål för kapacitet (GB), Transaktionshastighet och bandbredd. Om ditt program närmar sig eller överskrider något av skalbarhetsmål, stöta ökad transaktion fördröjningar eller begränsning. När en lagringstjänsten begränsar ditt program, börjar tjänsten att returnera ”503 servern upptagen” eller ”tidsgräns för 500 åtgärd” felkoder för vissa lagringstransaktioner. Det här avsnittet beskrivs både det allmänna tillvägagångssättet för hantera skalbarhetsmål och skalbarhetsmål för bandbredd särskilt. Senare avsnitt som handlar om enskilda lagringstjänster beskrivs skalbarhetsmål i kontexten för den specifika tjänsten:  

* [BLOB-bandbredd och begäranden per sekund](#subheading16)
* [Tabellentiteter per sekund](#subheading24)
* [Kömeddelanden per sekund](#subheading39)  

#### <a name="sub1bandwidth"></a>Bandbredd skalbarhet mål för alla tjänster
Vid tidpunkten för skrivning är bandbredd mål i USA för ett konto med geo-redundant lagring (GRS) 10 Gigabit per sekund (Gbps) för inkommande trafik (data som skickas till lagringskontot) och 20 Gbit/s för utgående trafik (data som skickas från storage-konto). För ett lokalt redundant lagringskonto (LRS) gränserna som är högre – 20 Gbit/s för ingående och 30 Gbit/s för utgående trafik.  Internationella bandbreddsgränser kan vara lägre och finns på vår [skalbarhet mål sidan](http://msdn.microsoft.com/library/azure/dn249410.aspx).  Mer information om lagringsalternativ för redundans finns i [användbara resurser](#sub1useful) nedan.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>Vad gör närmar sig ett mål för skalbarhet
Om ditt program närmar sig skalbarhetsmål för ett enda lagringskonto, bör du införandet av en av följande metoder:  

* Ändra den arbetsbelastning som gör att programmet till närmar sig eller överskrider skalbarhet målet. Kan du utforma på olika sätt att använda mindre bandbredd eller kapacitet eller färre transaktioner?
* Om ett program måste överskrider ett av målen för skalbarhet, bör du skapa flera lagringskonton och partition dina programdata mellan dessa flera lagringskonton. Om du använder det här mönstret sedan bör du utforma ditt program så att du kan lägga till flera lagringskonton i framtiden för belastningsutjämning. Varje Azure-prenumeration kan ha upp till 100 lagringskonton vid tiden för skrivning.  Storage-konton har också utan kostnad än förbrukningen termer data som lagras, transaktioner som har gjorts eller data som överförs.
* Om ditt program träffar bandbredd mål, Överväg att komprimera data i klienten och minska den bandbredd som krävs för att skicka data till storage-tjänst.  Observera att även om detta kan spara bandbredd och förbättra nätverkets prestanda, den kan också innehålla några negativa konsekvenser.  Du bör utvärdera prestandapåverkan detta på grund av ytterligare bearbetning kraven för att komprimera och expandera data på klienten. Dessutom kan kan lagra komprimerade data göra det svårare att felsöka problem Eftersom det kan vara svårare att visa lagrade data med standardverktyg.
* Om ditt program träffar skalbarhetsmål, kontrollera att du använder en exponentiell backoff för återförsök (se [återförsök](#subheading14)).  Det är bättre att se till att du aldrig närmar sig skalbarhetsmål (med hjälp av en av metoderna ovan), men det säkerställer att programmet inte bara att fortsätta försöka snabbt, vilket gör begränsningen värre.  

#### <a name="useful-resources"></a>Användbara resurser
Följande länkar innehåller ytterligare information om skalbarhetsmål:

* Se [Azure Storage skalbarhets- och prestandamål](storage-scalability-targets.md) information om skalbarhetsmål.
* Se [Azure Storage-replikering](storage-redundancy.md) och blogginlägget [Azure lagringsalternativ för redundans och Geo-Redundant lagring med läsbehörighet](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) information om lagringsalternativ för redundans.
* Aktuell information om priser för Azure-tjänster finns [priser för Azure](https://azure.microsoft.com/pricing/overview/).  

### <a name="subheading47"></a>Namngivningskonventionen för partition
Azure Storage använder ett intervall-baserade partitioneringsschema skalning och inläsning balans i systemet. Partitionsnyckeln används för att partitionera data till intervall och dessa områden är belastningsutjämnad hela systemet. Detta innebär namngivningskonventioner, till exempel lexikala ordning (t.ex. msftpayroll, msftperformance, msftemployees osv.) eller med tidsstämplar (log20160101, log20160102, log20160102 osv.) kan låna ut sig själva partitioner som potentiellt är placerade på samma server partition, förrän en NLB åtgärden delar ut dem i mindre intervall. Alla blobbar i en behållare kan till exempel hanteras av en enskild server förrän belastningen på dessa blobbar kräver ytterligare omfördelning av partition intervall. På samma sätt kan en grupp med lågt belastade konton med namnen i lexikala ordning kan hanteras av en enskild server förrän belastningen på en eller alla dessa konton behöver dem för att delas upp på flera servrar i partitioner. Varje belastningsutjämningsregel åtgärden kan påverka svarstiden för lagring anrop under åtgärden. Systemets möjlighet att hantera en plötslig burst av trafik till en partition begränsas av skalbarhet i en enda partition server förrän åtgärden för belastningsutjämning sparkar i och balanserar partitionsnyckelintervallet.  

Du kan följa några metoder för att minska frekvensen av dessa åtgärder.  

* Granska namngivningskonvention som du använder för konton, behållare, blobbar, tabeller och köer, nära. Du skapar prefix kontonamn med ett 3-siffriga hash-värde med hjälp av en hash-funktionen som bäst passar dina behov.  
* Om du organisera dina data med tidsstämplar eller numerisk identifierare, måste du se till att du inte använder en append-only (eller endast lägga) trafikmönster. Dessa mönster lämpar sig inte för ett intervall-baserade partitionering system och kan leda till all trafik som kommer att en enskild partition och begränsa system från effektivt belastningsutjämning. Till exempel om du har dagliga åtgärder som använder ett blob-objekt med en tidsstämpel, till exempel ÅÅÅÅMMDD omdirigeras sedan all trafik för den dagliga driften till ett enda objekt som hanteras av en enda partition-servern. Titta på om den per blob gränser och partition gränser uppfyller dina behov och kan du dela den här åtgärden i flera blobbar om det behövs. På samma sätt om du sparar tid series-data i tabeller dirigeras alla trafiken kan vara till den senaste del av nyckeln namnområdet. Om du måste använda tidsstämplar eller numeriska ID, prefix prefix id med 3-siffriga hash eller tidsstämplar sekunder en del av tid som ssyyyymmdd. Visa och undersöka åtgärder utförs regelbundet, välja en hash-funktionen som begränsar antalet frågor. I annat fall kan en slumpmässig prefixet vara tillräckligt.  
* För ytterligare information om partitioneringsschema används i Azure Storage, läsa SOSP paper [här](http://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

### <a name="networking"></a>Nätverk
När API-anrop frågan, har fysiska nätverksbegränsningar i programmet ofta en betydande inverkan på prestanda. Nedan beskrivs några begränsningar kan användarna få.  

#### <a name="client-network-capability"></a>Klienten nätverkskapacitet
##### <a name="subheading2"></a>Dataflöde
Problemet är ofta funktionerna i klienten för bandbredd. Till exempel när ett enda storage-konto kan hantera 10 Gbit/s eller flera av ingång (se [bandbredd skalbarhetsmål](#sub1bandwidth)), nätverkshastigheten i en instans av ”liten” Azure-Arbetsroll kan endast cirka 100 Mbit/s. Större Azure-instanser har nätverkskort med större kapacitet, så bör du använda en större instans eller flera Virtuella datorer om du behöver högre gränser i nätverket från en enda dator. Om du ansluter till en Storage-tjänst från ett på lokala program och samma sak gäller: Förstå nätverksfunktioner på klientenheten och nätverksanslutningen till lagringsplatsen för Azure och antingen förbättra dem som behövs eller designa programmet att fungera inom deras funktioner.  

##### <a name="subheading3"></a>Länkkvalitet
Precis som med alla nätverksanvändning Tänk på att nätverksförhållanden ledde till ett fel och paketförlust kommer långsamma effektiva genomflöde.  WireShark eller NetMon hjälp kan lösa problemet.  

##### <a name="useful-resources"></a>Användbara resurser
Mer information om storlekar för virtuella datorer och allokerad bandbredd finns [Windows VM-storlekar](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) eller [Linux VM-storlekar](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

#### <a name="subheading4"></a>Plats
I en distribuerad miljö ger placerar klienten nära servern bästa prestanda. För att komma åt Azure Storage med den lägsta fördröjningen är den bästa platsen för din klient inom samma Azure-region. Till exempel om du har en Azure-webbplats som använder Azure Storage kan du söka efter dem båda inom en enskild region (till exempel oss Väst eller Asien/Stillahavsområdet, sydost). Detta minskar svarstiden och kostnaden – vid tidpunkten för skrivning bandbreddsanvändning inom en enskild region är ledig.  

Om klienten program inte finns i Azure (till exempel appar för mobila enheter eller lokala företagstjänster), sedan igen placerar storage-konto i en region nära de enheter som kommer åt, vanligtvis minskar latens. Om klienterna distribueras brett (till exempel, en del i Nordamerika och vissa i Europa), så bör du använda flera lagringskonton: placerad i Nordamerika region och en i en europeisk region. Detta hjälper till att minska svarstiden för användare i båda regioner. Den här metoden är vanligtvis enklare att implementera om data programmet lagrar är specifik för enskilda användare och kräver inte replikerar data mellan lagringskonton.  En CDN rekommenderas för bred innehållsdistribution – finns i nästa avsnitt för mer information.  

### <a name="subheading5"></a>Innehållsdistribution
Ibland kan behöver ett program hantera samma innehåll till flera användare (t.ex. en produkt demonstrationsvideon används i startsidan för en webbplats), finns i samma eller flera regioner. I det här scenariot bör du använda en innehåll innehållsleveransnätverk (CDN), till exempel Azure CDN och CDN ska använda Azure storage startpunkt som data. Till skillnad från ett Azure Storage-konto som finns i en region och som det går inte att leverera innehåll med låg latens till andra regioner använder Azure CDN-servrar i flera Datacenter runtom i världen. Dessutom kan en CDN vanligtvis stöder mycket högre utgång gränser än ett enda storage-konto.  

Mer information om Azure CDN finns [Azure CDN](https://azure.microsoft.com/services/cdn/).  

### <a name="subheading6"></a>Med hjälp av SAS och CORS
När du vill tillåta kod exempelvis JavaScript i webbläsare för en användare eller en mobiltelefon-app för att komma åt data i Azure Storage, en metod är att använda ett program i webbroll som en proxy: användarens enhet autentiseras med webbrollen, som i sin tur autentiserar med storage-tjänst. På så sätt kan undvika du att exponera dina lagringskontonycklar på osäker enheter. Men placerar detta en stor belastning på webbrollen eftersom alla data som överförs mellan enheten och lagringstjänsten måste passera webbrollen. Du kan undvika att använda en webbroll som en proxy för lagringstjänsten med delad åtkomst signaturer (SAS), ibland tillsammans med rubriker om Cross-Origin Resource Sharing (CORS). Med SAS kan tillåta du användaren att göra förfrågningar direkt till en lagringstjänst med hjälp av en begränsad åtkomst-token. Till exempel om en användare vill överför en bild till ditt program, kan web-roll generera och skicka till användarens enhet en SAS-token som ger behörighet att skriva till en specifik blobb eller en behållare för de kommande 30 minuterna (efter vilken SAS-token upphör att gälla).

En webbläsare kan normalt inte JavaScript på en sida som en webbplats på en domän att utföra specifika åtgärder som en ”PLACERA” till en annan domän som värd. Om du är värd en webbroll på ”contosomarketing.cloudapp.net”, och vill använda client side JavaScript för att ladda upp en blob storage-konto på ”contosoproducts.blob.core.windows.net”, webbläsarens exempelvis ”samma ursprung policy” kommer förbjuda den här åtgärden. CORS är en webbläsarfunktion som tillåter måldomänen (i det här fallet lagringskontot) för att kommunicera med webbläsaren att den litar på begäranden med ursprung i källdomänen (i det här fallet webbrollen).  

Båda dessa tekniker kan hjälpa dig att undvika onödiga belastning (och flaskhalsar) på ditt webbprogram.  

#### <a name="useful-resources"></a>Användbara resurser
Mer information om SAS finns [signaturer för delad åtkomst, del 1: Förstå SAS-modellen](../storage-dotnet-shared-access-signature-part-1.md).  

Läs mer om CORS [Cross-Origin Resource Sharing (CORS) stöd för Azure Storage-tjänster](http://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Cachelagring
#### <a name="subheading7"></a>Hämtning av Data
I allmänhet är hämtar data från en tjänst när bättre än tas den två gånger. Studera exemplet som en MVC-webbapp körs i en webbroll som har redan hämtas en 50MB blob storage-tjänst som fungerar som innehåll till en användare. Programmet kan sedan hämta samma blobben varje gång en användare begär den eller det kan cachelagra den lokalt disken och återanvända den cachelagrade versionen för efterföljande användarförfrågningar. Dessutom, när en användare begär data, programmet kan problemet får med ett villkorat huvud för ändringstid som skulle undvika hela blobben om den inte har ändrats. Du kan använda den här samma mönster för att arbeta med tabellentiteter.  

I vissa fall kan du välja att programmet kan anta att blob förblir giltigt under en kort period efter hämtning av det och att under denna tid programmet inte behöver kontrollera om blob har ändrats.

Konfiguration, sökning och andra data som används alltid av programmet är bra kandidater för cachelagring.  

Ett exempel på hur en blob-egenskaper för att identifiera senaste ändringsdatum med hjälp av .NET finns [Set och hämta egenskaper och Metadata](../blobs/storage-properties-metadata.md). Mer information om nedladdningar av villkorlig finns [villkorligt uppdatera en lokal kopia av en Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx).  

#### <a name="subheading8"></a>Ladda upp Data i batchar
I vissa program, kan aggregera data lokalt och sedan regelbundet ladda upp den i en grupp i stället för att överföra varje datadel omedelbart. Till exempel ett webbprogram ha en loggfil över aktiviteter: programmet kan antingen ladda upp information om varje aktivitet som det sker som en tabell-enhet (som kräver många lagringsåtgärder) eller så kan spara information om datoraktivitet till en lokal loggfil och sedan överföra alla aktivitetsinformation regelbundet som en avgränsad fil till en blobb. Om varje loggpost är 1KB i storlek, kan du överföra tusentalsavgränsare i en enda transaktion för ”placera Blob” (du kan överföra en blob med upp till 64MB i storlek i en enda transaktion). Naturligtvis, om den lokala datorn kraschar före överföringen, potentiellt förlorar du vissa loggdata: programutvecklaren måste utforma möjlighet till klientenheten eller Överför fel.  Om aktivitetsdata behöver laddas ner för timespans (inte bara enkel aktivitet), bör blobbar över tabeller.

### <a name="net-configuration"></a>.NET-konfiguration
Om du använder .NET Framework innehåller det här avsnittet flera snabb konfigurationsinställningar som du kan använda för att göra betydande prestandaförbättringar.  Om du använder andra språk, kontrollera för att se om liknande koncept som tillämpas på ditt valda språk.  

#### <a name="subheading9"></a>Öka Standardgränsen för anslutning
I .NET ökas följande kod anslutning Standardgränsen (vilket är normalt 2 i en klientmiljö med eller 10 i en servermiljö) och 100. Normalt ska du ange värdet till ungefär antalet trådar som används av ditt program.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Du måste ange anslutningsgränsen innan du öppnar alla anslutningar.  

Andra programmeringsspråk, finns i dokumentationen för det språket att fastställa hur du ställer in det angivna antalet.  

Mer information finns i bloggposten [Web Services: samtidiga anslutningar](http://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx).  

#### <a name="subheading10"></a>Öka ThreadPool Min Threads om synkron kod med asynkrona uppgifter
Den här koden kommer att öka tråd pool min trådar:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Mer information finns i [ThreadPool.SetMinThreads metoden](http://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="subheading11"></a>Dra nytta av .NET 4.5 skräpinsamling
Använda .NET 4.5 eller senare för klientprogrammet för att dra nytta av bättre prestanda i server skräpinsamling.

Mer information finns i artikeln [en översikt av prestandaförbättringarna i .NET 4.5](http://msdn.microsoft.com/magazine/hh882452.aspx).  

### <a name="subheading12"></a>Unbounded parallellitet
Parallellitet kan vara bra prestanda, vara försiktig med att använda unbounded parallellitet (ingen gräns för antalet trådar och/eller parallella begäranden) att överföra eller hämta data med flera personer att få åtkomst till flera partitioner (behållare, köer eller tabellpartitioner) i samma lagringskonto eller komma åt flera objekt i samma partition. Om parallellitet unbounded tillämpningsprogrammet kan överstiga klienten enhetsfunktioner eller storage-konto skalbarhet mål ledde till ett längre svarstider och begränsning.  

### <a name="subheading13"></a>Verktyg och Storage-klientbibliotek
Använd alltid senaste som Microsoft tillhandahåller klientbibliotek och verktyg. Vid tidpunkten som skrivs finns klientbibliotek för .NET, Windows Phone, Windows Runtime, Java och C++ samt preview bibliotek för andra språk. Dessutom har Microsoft släppt PowerShell-cmdlets och Azure CLI-kommandona för att arbeta med Azure Storage. Microsoft aktivt utvecklar dessa verktyg med prestanda, för att hålla dem uppdaterade med de senaste versionerna av tjänsten och garanterar de hanterar många av aktuella beprövade prestanda internt.  

### <a name="retries"></a>Antal försök
#### <a name="subheading14"></a>Throttling/ServerBusy
I vissa fall lagringstjänsten kan begränsa tillämpningsprogrammet eller helt enkelt går inte att hantera begäran på grund av vissa övergående tillstånd och returnerar meddelandet ”503 servern upptagen” eller ”tidsgräns för 500”.  Detta kan inträffa om tillämpningsprogrammet närmar sig något skalbarhetsmål, eller om systemet är ombalansering partitionerade data för bättre genomströmning.  Klientprogrammet bör normalt gör om åtgärden som orsakar ett sådant fel: försöker samma begäran senare kan genomföras. Men göra om lagringstjänsten begränsning är ditt program eftersom det överskrider skalbarhetsmål eller om tjänsten kunde inte svara på begäran av någon anledning, aggressivt återförsök oftast problemet worse. Därför bör du använda en exponentiell undantagsläge (klienten bibliotek standard till det här beteendet). Programmet kan t.ex, försök igen efter 2 sekunder sedan 4 sekunder sedan 10 sekunder sedan 30 sekunder och ge helt. Detta resulterar i ditt program avsevärt minska belastningen på tjänsten i stället exacerbating eventuella problem.  

Observera att anslutningsfel kan göras omedelbart, eftersom de inte är resultatet av begränsning och förväntas vara tillfälligt.  

#### <a name="subheading15"></a>Icke-Återförsökbart fel
Klientbiblioteken är medveten om vilka fel kan retry och som inte är. Tänk dock på om du skriver egen kod mot storage REST-API, det finns några fel som du inte bör försöka: till exempel en 400 (felaktig begäran) svaret anger att klientprogrammet har skickat en begäran inte kunde bearbetas eftersom det inte var ett förväntat format. Skicka om begäran resulterar samma svar varje gång, så det finns ingen anledning du försöker den. Om du skriver egen kod mot storage REST API vara medveten om vad de betyder och på rätt sätt att försöka igen (eller inte) för var och en av dem.  

#### <a name="useful-resources"></a>Användbara resurser
Mer information om lagring felkoder finns [Status och felkoder](http://msdn.microsoft.com/library/azure/dd179382.aspx) på webbplatsen Microsoft Azure.  

## <a name="blobs"></a>Blobar
Förutom beprövade metoder för [alla tjänster](#allservices) som beskrivs ovan, följande beprövade metoder som gäller specifikt för blob-tjänsten.  

### <a name="blob-specific-scalability-targets"></a>BLOB-specifika skalbarhetsmål
#### <a name="subheading46"></a>Flera klienter som ansluter till ett enda objekt samtidigt
Om du har ett stort antal klienter som ansluter till ett enda objekt samtidigt behöver du överväga per objekt och lagring skalbarhetsmål för lagringskontot. Det exakta antalet klienter som kan komma åt ett objekt ska variera beroende på faktorer som hur många klienter som begär objektet samtidigt, storleken på objektet, nätverk villkor osv.

Om objektet kan distribueras via en CDN, till exempel bilder eller videofiler hanteras från en webbplats och du bör använda en CDN. Se [här](#subheading5).

I andra scenarier, till exempel vetenskapliga simulering där data är konfidentiellt har du två alternativ. Först är att sprida ut din arbetsbelastning åtkomst så att komma åt objektet under en period om jämfört används samtidigt. Du kan också kopiera objektet tillfälligt till flera storage-konton, vilket ökar den totala IOPS per objekt och över storage-konton. Begränsad testning påträffades att cirka 25 virtuella datorer kan hämta en 100GB blob parallellt (varje virtuell dator parallelizing nedladdningen med 32 trådar) samtidigt. Om du har 100 klienter behöver komma åt objektet, först kopiera den till en andra storage-konto och har de första 50 virtuella datorer åtkomst till den första blobben och andra 50 virtuella datorer åtkomst till andra blob. Resultatet varierar beroende på din program-beteende så bör du testa under design. 

#### <a name="subheading16"></a>Bandbredd och -åtgärder per Blob
Du kan läsa eller skriva till en enda blob på högst 60 MB per sekund (detta är ungefär 480 Mbit/s som överskrider kapaciteterna för flera nätverk för klient på klientsidan (inklusive det fysiska nätverkskortet på klientenheten). Dessutom kan stöder en enda blob upp till 500 begäranden per sekund. Om du har flera klienter som behöver läsa samma blob och du kanske överskrider gränserna, bör du använda en CDN för att distribuera blob.  

Mer information om mål-genomströmning för BLOB finns [Azure Storage skalbarhets- och prestandamål](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Kopiera och flytta Blobbar
#### <a name="subheading17"></a>Kopiera Blob
Storage REST API version 2012-02-12 införs användbar möjligheten att kopiera BLOB mellan konton: ett klientprogram kan instruera lagringstjänsten att kopiera en blobb från en annan källa (eventuellt i ett annat lagringskonto) och därefter låta tjänsten kopiera asynkront. Detta kan avsevärt minska den bandbredd som krävs för programmet när du migrerar data från andra lagringskonton eftersom du inte behöver hämta och överföra data.  

En fråga, men är att, när du kopierar mellan lagringskonton finns det ingen garanti för tid för när kopieringen slutförs. Om ditt program behöver slutföra en blob-kopia snabbt under din kontroll, kan det vara bättre att kopiera blob genom att hämta den till en virtuell dator och överföra den till målet.  Se till att kopian utförs av en virtuell dator som körs i samma Azure-region, annars nätverksförhållanden kan (och troligtvis kommer) påverkar prestandan kopia för fullständig förutsägbarhet i så fall.  Dessutom kan du övervaka förloppet för en asynkron kopia programmässigt.  

Observera att kopior inom samma lagringskonto själva är vanligtvis slutförda snabbt.  

Mer information finns i [kopiera Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="subheading18"></a>Använda AzCopy
Azure Storage-teamet har publicerat ett kommandoradsverktyg ”AzCopy” som är tänkt att hjälpa med bulk överför många BLOB till, från och mellan lagringskonton.  Det här verktyget är optimerad för det här scenariot och uppnå hög överföringshastighet.  Används rekommenderas för bulk-överföringen, hämtning och kopiera scenarier. Om du vill veta mer om den och ladda ned den finns [överföra data med kommandoradsverktyget Azcopy](storage-use-azcopy.md).  

#### <a name="subheading19"></a>Azure Import/Export Service
För mycket stora mängder data (mer än 1TB) erbjuder Azure Storage Import/Export-tjänsten, som gör det möjligt att överföra och ladda ned från blob storage med leverans hårddiskar.  Du kan publicera dina data på en hårddisk och skickar informationen till Microsoft för överföring eller skicka en tom hårddisk till Microsoft för att hämta data.  Mer information finns i [använda tjänsten Microsoft Azure Import/Export för att överföra Data till Blob Storage](../storage-import-export-service.md).  Det kan vara mycket effektivare än att överföra/hämta volymen av data över nätverket.  

### <a name="subheading20"></a>Använda metadata
Blob-tjänsten stöder head-begäranden som kan innehålla metadata om blob. Om ditt program behövs EXIF-data från ett foto, kan den hämta bilden och extrahera det. För att spara bandbredd och förbättra prestanda, kan ditt program lagra data EXIF i blobens metadata när programmet har överförts bilden: du kan hämta EXIF-data i metadata med hjälp av endast en HEAD-begäran, spara betydande bandbredd och tid som behövs för att extrahera EXIF data varje gång blob läses. Detta kan vara användbart i scenarier där du behöver bara metadata och inte det fullständiga innehållet på en blob.  Observera att endast 8 KB metadata kan lagras per blob (tjänsten inte accepterar en begäran om att lagra större än), så om data inte ryms i den storleken, kan du inte kunna använda den här metoden.  

Ett exempel på hur du hämtar en blob-metadata med hjälp av .NET finns [Set och hämta egenskaper och Metadata](../blobs/storage-properties-metadata.md).  

### <a name="uploading-fast"></a>Snabb överföring
Om du vill överföra blobbar snabb, är den första frågan besvaras: är du överföra en blob eller många?  Använd den nedan för att avgöra vilken metod som har rätt att använda beroende på ditt scenario.  

#### <a name="subheading21"></a>Ladda upp en stor blob snabbt
Om du vill överföra en enda stor blob snabbt klientprogrammet ladda upp dess block eller sidor parallellt (är uppmärksam på skalbarhetsmål för enskilda blobbar och lagringskontot som helhet).  Observera att de officiella Microsoft RTM lagring klientbibliotek (.NET, Java) ha möjlighet att göra detta.  För varje bibliotek använder den under angivna objektegenskaper att ställa in samtidighet:  

* .NET: Ange ParallelOperationThreadCount på ett BlobRequestOptions-objekt som ska användas.
* Java/Android: Use BlobRequestOptions.setConcurrentRequestCount()
* Node.js: Använd parallelOperationThreadCount alternativen begäran eller blob-tjänsten.
* C++: Använda metoden blob_request_options::set_parallelism_factor.

#### <a name="subheading22"></a>Överför många blobbar snabbt
Om du vill överföra många blobbar snabbt överföra blobbar parallellt. Det här är snabbare än att överföra enda blobbar i taget med parallellt block överföringar eftersom det sprids överföringen över flera partitioner för lagringstjänsten. En enda blob stöder bara en genomströmning på 60 MB per sekund (cirka 480 Mbit/s). Ett konto i USA-baserade LRS stöder upp till 20 Gbit/s-ingång som är större än dataflöde som stöds av en enskild blob vid tidpunkten för skrivning.  [AzCopy](#subheading18) utför överföringar parallellt som standard och rekommenderas för det här scenariot.  

### <a name="subheading23"></a>Att välja rätt typ av blob
Azure Storage stöder två typer av blob: *sidan* blobbar och *block* blobbar. För en given användningsscenariot påverkar valet av blobbtypen prestanda och skalbarhet i lösningen. Blockblobbar är lämplig när du vill överföra stora mängder data effektivt: till exempel ett klientprogram kan behöva ladda upp bilder eller video till blob storage. Sidblobbar är lämpliga om programmet behöver utföra slumpmässiga skrivningar på data: till exempel Azure virtuella hårddiskarna lagras som sidblobar.  

Mer information finns i [förstå Blockblobbar, Tilläggsblobbar och Sidblobbar](http://msdn.microsoft.com/library/azure/ee691964.aspx).  

## <a name="tables"></a>Tabeller
Förutom beprövade metoder för [alla tjänster](#allservices) som beskrivs ovan, följande beprövade metoder gäller särskilt för tabelltjänsten.  

### <a name="subheading24"></a>Tabell-specifika skalbarhetsmål
Utöver bandbreddsbegränsningar för en hel lagringskonto har tabeller följande specifika skalbarhetsgränsen.  Observera att systemet ska belastningsutjämna som ökar din trafik, men om trafiken har plötslig belastning kan du kanske inte går att hämta den här volymen genomströmning omedelbart.  Om mönstret har belastning, bör du förväntar dig att se begränsning och tidsgränser under burst som lagring tjänsten automatiskt belastningen saldon i tabellen.  Långsamt vanligtvis av dig har bättre resultat som den ger systemtiden att belastningsutjämna på lämpligt sätt.  

#### <a name="entities-per-second-account"></a>Entiteter per sekund (konto)
Skalbarhetsgränsen för att komma åt tabeller är upp till 20 000 enheter (1KB som är varje) per sekund för ett konto.  I allmänhet varje entitet som infogas, uppdateras, tas bort eller genomsöks antal mot det här målet.  Infoga en batch som innehåller 100 entiteter skulle så räknas som 100 entiteter.  En fråga som genomsöker 1000 entiteter och returnerar 5 räknas som 1000 enheter.  

#### <a name="entities-per-second-partition"></a>Entiteter per sekund (Partition)
Inom en enskild partition är skalbarhet målet för att komma åt tabeller 2 000 enheter (1KB som är varje) per sekund, med samma inventering som beskrivs i föregående avsnitt.  

### <a name="configuration"></a>Konfiguration
Det här avsnittet innehåller flera snabb konfigurationsinställningar som du kan använda för att göra betydande prestandaförbättringar i tabelltjänsten:  

#### <a name="subheading25"></a>Använd JSON
Från och med version av storage service 2013-08-15, stöder tabelltjänsten användningen av JSON i stället för XML-baserade AtomPub-format för att överföra tabelldata. Detta kan minska nyttolast storlekar som 75% och förbättrar prestandan för din app.

Mer information finns i inlägg [Microsoft Azure-tabeller: introduktion till JSON](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) och [Nyttolastformat för tabellen tjänståtgärder](http://msdn.microsoft.com/library/azure/dn535600.aspx).

#### <a name="subheading26"></a>Nagle ut
Nagles algoritmen implementeras över TCP/IP-nätverk som används för att förbättra nätverksprestanda. Det är dock inte optimalt i samtliga fall (till exempel interaktiva miljöer). Nagles algoritm har en negativ inverkan på prestanda för begäranden till tjänsterna tabell och kön för Azure Storage, och bör du inaktivera den om möjligt.  

Mer information finns i vår blogginlägget [Nagles algoritmen är inte egna gentemot små begäran](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx), som förklarar varför Nagles algoritmen dåligt samverkar med tabell- och köegenskaper begäranden och visar hur du inaktiverar det i ditt klientprogram.  

### <a name="schema"></a>Schema
Hur du representera och fråga data är den största en faktor som påverkar prestanda för tabelltjänsten. Varje program är olika, beskrivs i det här avsnittet några allmänna beprövade metoder som relaterar till:  

* Tabelldesign
* Effektiva frågor
* Effektiv uppdateringar  

#### <a name="subheading27"></a>Tabeller och partitioner
Tabeller är indelade i partitioner. Varje enhet som lagras i en partition delar samma partitionsnyckel och har en unik rad för att identifiera den i den aktuella partitionen. Partitioner ger fördelar, men också introducera skalbarhetsbegränsningar.  

* Fördelar: Du kan uppdatera entiteter i samma partition i en enskild-atomic, batch-transaktion som innehåller upp till 100 separat lagringsåtgärder (högst 4MB, total storlek). Under förutsättning att samma antal enheter som ska hämtas, kan du också fråga data i en enda partition effektivare än data sträcker sig över flera partitioner (även om läsa på ytterligare rekommendationer för frågar tabelldata).
* Skalbarhetsgränsen: åtkomst till entiteter som lagras i en partition kan inte vara belastningsutjämnad eftersom partitioner stöder atomiska Batchtransaktioner. Därför är skalbarhet mål för en enskild tabell partition lägre än för tabelltjänsten som helhet.  

På grund av dessa tabeller och egenskaper partitioner, bör du vidta följande designprinciperna:  

* Data som ditt klientprogram uppdateras ofta eller frågas i samma logiska arbetsenheten måste finnas i samma partition.  Det kan vara eftersom programmet är sammanställa skrivningar eller eftersom du vill dra nytta av atomiska batchåtgärder.  Dessutom kan data i en partition effektivare efterfrågas i en enskild fråga än data över partitioner.
* Data som ditt klientprogram inte infoga/uppdatera eller fråga i samma logiska enhet arbete (enskild fråga eller gruppuppdatering) måste finnas i olika partitioner.  Ett viktigt är att det finns ingen gräns för antalet partitionsnycklar i en tabell med miljontals partitionsnycklar är inte ett problem och påverkar inte prestanda.  Till exempel om ditt program är en populär webbplats med användarinloggning, vara med hjälp av användar-Id som partitionsnyckel ett bra alternativ.  

#### <a name="hot-partitions"></a>Varm partitioner
En varm partition är en som tar emot en stor andel av trafiken till ett konto och kan inte vara belastningsutjämnade eftersom det är en enskild partition.  I allmänhet skapas varm partitioner på två sätt:  

##### <a name="subheading28"></a>Lägg endast till och lägga endast mönster
Mönstret ”Lägg till bara” är en där alla (eller nästan alla) av trafik till en viss PK ökar och minskar enligt den aktuella tiden.  Ett exempel är om ditt program används det aktuella datumet som en partitionsnyckel för loggdata.  Detta resulterar i alla infogningar går till den senaste partition i tabellen och det går inte att läsa belastningsutjämna eftersom alla skrivningar kommer till slutet av tabellen.  Om mängden trafik till denna partition överskrider målet partition nivå skalbarhet, resulterar det i begränsning.  Det är bättre så att trafik som skickas till flera partitioner för att aktivera belastningsutjämning av begäranden i tabellen.  

##### <a name="subheading29"></a>Hög trafik Data
Om din partitioneringsschema resulterar i en enda partition som bara innehåller data som används mycket mer än andra partitioner, kan du också se begränsning som att partitionen närmar sig skalbarhet målet för en enskild partition.  Det är bättre att se till att din partitionsschema resulterar i någon enskild partition som närmar sig skalbarhetsmål.  

#### <a name="querying"></a>Fråga
Det här avsnittet beskrivs beprövade metoder för att fråga efter tabelltjänsten.  

##### <a name="subheading30"></a>Frågeomfattningen
Det finns flera sätt att ange intervallet för entiteterna som frågas.  Här följer en beskrivning av användning av varje.  

I allmänhet undvika genomsökningar (frågor som är större än en enda enhet), men om du måste skannar, försök att ordna data så att dina skanningar hämta de data du behöver utan genomsökning eller returnera stora mängder enheter inte behöver du.  

###### <a name="point-queries"></a>Punkt-frågor
En punkt-fråga hämtar exakt en enhet. Det gör du genom att ange både partitionsnyckel och radnyckel för att hämta entiteten. De här frågorna är mycket effektivt och du bör använda dem om möjligt.  

###### <a name="partition-queries"></a>Partitionen frågor
En partitionsfrågan är en fråga som hämtar en datamängd som delar en gemensam partitionsnyckel. Frågan anger vanligtvis ett intervall av nyckelvärden för raden eller ett intervall med värden för vissa entitetsegenskap förutom en partitionsnyckel. Dessa är mindre effektiva än punkt frågor och bör användas sparsamt.  

###### <a name="table-queries"></a>Tabell frågor
En tabellfråga är en fråga som hämtar en uppsättning enheter som inte delar en gemensam partitionsnyckel. Dessa frågor är inte effektivt och du bör undvika dem om möjligt.  

##### <a name="subheading31"></a>Frågan densitet
En annan nyckelfaktor i frågan effektivitet är antalet enheter som returneras jämfört med antal entiteter som genomsöks för att hitta den returnerade uppsättningen. Om ditt program utför en tabellfråga med ett filter för ett värde för egenskapen att bara 1% av data resurser frågan genomsöks 100 entiteter för var en entitet som returneras. Tabellen skalbarhetsmål beskrivs tidigare alla avser antalet enheter som har genomsökts och inte antal entiteter som returneras: låg frågan densitet enkelt kan orsaka tabelltjänsten att begränsa ditt program eftersom det måste söka så många entiteter för att hämta entiteten som du letar efter.  I avsnittet nedan på [denormalization](#subheading34) mer information om hur du undviker detta.  

##### <a name="limiting-the-amount-of-data-returned"></a>Begränsa mängden Data som returneras
###### <a name="subheading32"></a>Filtrering
Överväg att använda ett filter för att minska storleken på den returnerade uppsättningen om du vet att en fråga returnerar enheter som du inte behöver i klientprogrammet. När enheterna inte returneras till klienten fortfarande räknas in skalbarhetsgränser, förbättrar programmets prestanda på grund av minskade nätverket nyttolastens storlek och minskat antal entiteter som klientprogrammet måste bearbeta.  Se över anteckning på [frågan densitet](#subheading31), men – skalbarhetsmål avser antalet enheter som genomsöks, så att en fråga som filtrerar ut många entiteter fortfarande kan resultera i begränsning, även om några enheter returneras.  

###### <a name="subheading33"></a>Projektion
Om klientprogrammet måste begränsad uppsättning egenskaper från entiteter i tabellen, kan du använda projektion för att begränsa storleken på datamängden som returnerades. Precis som med filtrering, bidrar detta till att minska nätverksbelastningen och klienten bearbetning.  

##### <a name="subheading34"></a>Denormalization
Till skillnad från arbete med relationsdatabaser leda beprövade metoder för att effektivt frågar tabelldata till denormalizing dina data. Som är dubblering av samma data i flera enheter (en för varje nyckel som du kan använda för att hitta data) att minimera antalet enheter som en fråga måste söka igenom för att söka efter data klienten måste i stället att skanna stort antal enheter för att hitta data programmet behöver.  Till exempel webbplatser för e-handel, du kanske vill hitta en order både kund-ID: t (mig kundens order) och efter datumet (mig order på ett datum).  I Table Storage är det bäst att lagra entiteten (eller en referens till den) två gånger – en gång med tabellnamn, PK och RK för att underlätta hitta av kunden ID, en gång för att underlätta söka efter datumet.  

#### <a name="insertupdatedelete"></a>Insert/Update/Delete
Det här avsnittet beskrivs beprövade metoder för att ändra entiteter som lagras i tabelltjänsten.  

##### <a name="subheading35"></a>Batchbearbetning
Batchtransaktioner är kända som entiteten grupp transaktioner (ETG) i Azure Storage; alla åtgärder i en ETG måste finnas på en partition i en tabell. Om möjligt bör du använda ETGs för att utföra infogningar, uppdateringar och borttagningar i batchar. Detta minskar antalet sändningar från ditt klientprogram till servern, minskar antalet fakturerbar transaktioner (en ETG räknas som en enda transaktion för fakturering och kan innehålla upp till 100 lagringsåtgärder) och aktiverar atomiska uppdateringar (alla åtgärder lyckas eller misslyckas inom en ETG alla). Miljöer med hög fördröjning till exempel från mobila enheter kommer avsevärt fördelarna med att använda ETGs.  

##### <a name="subheading36"></a>Upsert
Användningstabell **Upsert** åtgärder när så är möjligt. Det finns två typer av **Upsert**, som kan vara effektivare än traditionella **infoga** och **uppdatering** åtgärder:  

* **InsertOrMerge**: Använd den här när du vill överföra en delmängd av enhetens egenskaper, men är osäker på om entiteten finns redan. Om entiteten finns det här anropet uppdateras de egenskaper som ingår i den **Upsert** åtgärd och lämnar alla befintliga egenskaper som de är, om entiteten inte finns, infogas ny entitet. Detta liknar att projektion har använts i en fråga i att du behöver bara ladda upp de egenskaper som ändrar.
* **InsertOrReplace**: Använd den här när du vill överföra en helt ny entitet, men du är osäker på om den redan finns. Du bör endast använda den när du vet att nyligen uppladdade entiteten är helt korrekt eftersom det helt skriver över den gamla entiteten. Till exempel vill du uppdatera en entitet som lagrar användarens aktuella plats oavsett om programmet har tidigare lagrad lokaliseringsuppgifter för användaren. den nya plats entiteten är klar och du behöver inte någon information från tidigare entiteter.

##### <a name="subheading37"></a>Lagra dataserien i en enda entitet
Ibland kan ett program lagrar en serie med data som ofta behöver hämta allt samtidigt: ett program kan till exempel Spåra processoranvändning över tid för att rita ett rullande diagram av data från de senaste 24 timmarna. En metod är att tabellen enheter per timme, med varje entitet som representerar en specifik timme och lagrar CPU-användningen för den timmen. Programmet måste hämta data från de senaste 24 timmarna för innehavare för att rita dessa data.  

Du kan också programmet kan lagra CPU-användningen för varje timme som en separat av en enda entitet: för att uppdatera varje timme, programmet kan använda en enda **InsertOrMerge Upsert** anrop för att uppdatera värdet för den senaste timmen. Om du vill rita data programmet behöver bara en enda entitet i stället för 24, för ett mycket effektivt frågan att hämta (se ovan diskussion på [fråga scope](#subheading30)).

##### <a name="subheading38"></a>Lagra strukturerade data i BLOB
Ibland strukturerade data känns som det ska gå i tabeller, men intervallen för entiteter hämtas alltid tillsammans och kan batch infogas.  Ett bra exempel på detta är en loggfil.  I det här fallet kan du batch-flera minuters loggar, infoga dem och sedan du hämtar alltid flera minuter loggar i taget samt.  I det här fallet för prestanda är det bättre att använda blobbar i stället för tabeller, eftersom du kan avsevärt minska antalet objekt skrivs/returnerade, samt vanligtvis antalet begäranden som behöver.  

## <a name="queues"></a>Köer
Förutom beprövade metoder för [alla tjänster](#allservices) som beskrivs ovan, följande beprövade metoder som gäller specifikt för kötjänsten.  

### <a name="subheading39"></a>Skalbarhetsgränser
En enskild kö kan bearbeta ungefär 2 000 meddelanden (1KB som är varje) per sekund (varje AddMessage och GetMessage DeleteMessage antal som ett meddelande här). Om detta inte är tillräckligt för ditt program bör du använda flera köer och sprids dem meddelanden.  

Visa den aktuella skalbarhetsmål på [Azure Storage skalbarhets- och prestandamål](storage-scalability-targets.md).  

### <a name="subheading40"></a>Nagle ut
Se avsnittet om konfigurationen som beskrivs algoritmen Nagle – Nagle algoritmen är vanligtvis bra för prestanda för kön begäranden och bör du inaktivera den.  

### <a name="subheading41"></a>Meddelandestorlek
Kön prestanda och skalbarhet minskas när meddelandet storlek ökar. Du bör placera bara den information som mottagaren måste i ett meddelande.  

### <a name="subheading42"></a>Batch-hämtning
Du kan hämta upp till 32 meddelanden från en kö i en enda åtgärd. Detta minskar antalet turer fram och tillbaka från klientprogrammet, vilket är särskilt användbart för företagsmiljöer, t.ex mobila enheter med hög latens.  

### <a name="subheading43"></a>Avsökningsintervall för kön
De flesta program att söka efter meddelanden från en kö som kan vara något av de största källorna till transaktioner för programmet. Välj din avsökningsintervallet klokt: avsökning för ofta kan orsaka tillämpningsprogrammet att närma skalbarhetsmål för kön. På 200 000 transaktioner för $0,01 (vid tidpunkten för skrivning) är en enskild processor avsökning när varje sekund för en månad skulle kosta mindre än 15 cent så kostnaden dock inte vanligtvis en faktor som påverkar ditt val av avsökningsintervallet.  

Uppdaterade kostnadsinformation finns [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="subheading44"></a>UpdateMessage
Du kan använda **UpdateMessage** att öka tidsgränsen för osynlighet eller för att uppdatera information om tillstånd för ett meddelande. Detta är kraftfulla, Kom ihåg att varje **UpdateMessage** åtgärden räknar mot mål för skalbarhet. Det kan dock vara ett mycket effektivare sätt än med ett arbetsflöde som skickar ett jobb från en kö till nästa, eftersom varje steg i jobbet har slutförts. Med hjälp av den **UpdateMessage** åtgärden tillåter programmet att spara jobbets status till meddelandet och sedan fortsätta arbeta i stället för queuing meddelandet för nästa steg i jobbet igen varje gång ett steg har slutförts.  

Mer information finns i artikeln [så här: ändra innehållet i ett meddelande i kön](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message).  

### <a name="subheading45"></a>Programarkitektur
Du bör använda köer för att göra din programarkitektur skalbara. Här nedan listas några metoder som du kan använda köer för att göra programmet mer skalbar:  

* Du kan använda köer för att skapa eftersläpningar för bearbetning och Utjämna arbetsbelastningar i ditt program. Du kan till exempel köa-begäranden från användare arbetar processor, till exempel storleksändring överförda bilder.
* Du kan använda köer för att frikoppla delar av ditt program så att du kan skala oberoende av varandra. Till exempel placera en webbklientdel undersökningsresultat från användare i en kö för senare analys och lagring. Du kan lägga till flera worker rollinstanser för att bearbeta kön data vid behov.  

## <a name="conclusion"></a>Sammanfattning
Den här artikeln beskrivs några av de vanligaste, beprövade metoder för att optimera prestanda när du använder Azure Storage. Vi rekommenderar att alla programutvecklare utvärderar sina program med hjälp av ovanstående metoder, och överväger att följa rekommendationerna för att få ut mesta möjliga av de program som använder sig av Azure-lagring.