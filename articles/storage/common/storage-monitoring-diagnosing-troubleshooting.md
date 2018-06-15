---
title: Övervaka, diagnostisera och felsöka Azure Storage | Microsoft Docs
description: Använda funktioner som storage analytics loggning på klientsidan och andra verktyg från tredje part att identifiera, diagnostisera och felsöka Azure Storage-relaterade problem.
services: storage
documentationcenter: ''
author: fhryo-msft
manager: jahogg
editor: tysonn
ms.assetid: d1e87d98-c763-4caa-ba20-2cf85f853303
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: fhryo-msft
ms.openlocfilehash: b89071048594e1e11efb321da3d0b48005824b46
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
ms.locfileid: "29740671"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Övervaka, diagnostisera och felsök Microsoft Azure Storage
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Översikt
Diagnostisera och felsöka problem i ett distribuerat program som finns i en molnmiljö kan vara mer komplicerat än i vanliga miljöer. Program kan distribueras i en PaaS eller IaaS-infrastruktur lokalt på en mobil enhet eller i en kombination av dessa miljöer. Normalt nätverkstrafik för ditt program kan bläddra offentliga och privata nätverk och ditt program kan använda flera lagringstekniker som Microsoft Azure Storage-tabeller, Blobbar, köer eller filer och andra data lagras sådana som relationella och dokumentera databaser.

Om du vill hantera sådana program har bör du övervaka dem proaktivt och lär dig att diagnostisera och felsöka alla aspekter av dem och deras beroende tekniker. Som användare av Azure Storage-tjänster, bör du alltid övervaka Storage-tjänster som används i ditt program för eventuella oväntade ändringar i beteendet (till exempel långsammare än vanligt svarstider) och använder loggning att samla in mer detaljerade data och analysera problem på djupet. Diagnostikinformationen du hämta från både övervakning och loggning hjälper dig att fastställa orsaken till problemet uppstod i ditt program. Sedan kan du felsöka problemet och fastställa lämpliga åtgärder du kan vidta kan åtgärdas. Azure Storage är en grundläggande Azure-tjänsten och utgör en viktig del av flesta lösningar som kunder distribuera till Azure-infrastrukturen. Azure Storage innehåller funktioner för att förenkla övervaka, diagnostisera och felsöka problem med lagring i dina molnbaserade program.

> [!NOTE]
> Azure Files stöder inte loggning just nu.
> 

En praktisk guide till slutpunkt till slutpunkt för felsökning i Azure Storage-program finns [slutpunkt till slutpunkt felsökning med hjälp av Azure Storage-mätvärden och loggning, AzCopy och Message Analyzer](../storage-e2e-troubleshooting.md).

* [Introduktion]
  * [Hur den här guiden är organiserat]
* [övervakning lagringstjänsten]
  * [Övervakning av tjänstens hälsa]
  * [Övervakning av kapacitet]
  * [Övervaka tillgänglighet]
  * [Övervaka prestanda]
* [diagnostisera problem med lagring]
  * [Hälsotillståndsproblem med tjänsten för]
  * [prestandaproblem]
  * [Diagnostisera fel]
  * [Storage-emulatorn problem]
  * [Loggningsverktyg för lagring]
  * [Med hjälp av verktyg för loggning]
* [slutpunkt till slutpunkt spårning]
  * [Korrelerar loggdata]
  * [ID för klientbegäran]
  * [Server begäran-ID]
  * [Tidsstämplar]
* [felsökningsanvisningar]
  * [mätvärdena visar AverageE2ELatency hög och låg AverageServerLatency]
  * [Mätningar visar låga värden för AverageE2ELatency och AverageServerLatency, men klienten har ändå långa svarstider]
  * [Mätningar visar ett högt värde för AverageServerLatency]
  * [Du upplever oväntade fördröjningar i en köad meddelandeleverans]
  * [mätvärdena visar en ökning i PercentThrottlingError]
  * [mätvärdena visar en ökning i PercentTimeoutError]
  * [Mätningar visar en ökning i PercentNetworkError]
  * [Klienten tar emot HTTP 403 (förbjuden) meddelanden]
  * [Klienten tar emot HTTP 404 (inget hittas) meddelanden]
  * [Klienten tar emot HTTP 409 (konflikt) meddelanden]
  * [mätvärdena visar låg PercentSuccess eller analytics loggposter innehålla åtgärder med transaktionsstatus av ClientOtherErrors]
  * [Kapacitetsdata visar en oväntad ökning i kapacitetsförbrukning för lagring]
  * [Problemet uppstår från med hjälp av storage-emulatorn för utveckling eller testning]
  * [Det uppstår problem med att installera Azure SDK för .NET]
  * [Du har ett annat problem med en storage-tjänst]
  * [Felsökning av virtuella hårddiskar på virtuella Windows-datorer](../../virtual-machines/windows/troubleshoot-vhds.md)   
  * [Felsökning av virtuella hårddiskar på virtuella Linux-datorer](../../virtual-machines/linux/troubleshoot-vhds.md)
  * [Felsökning av problem med Windows Azure-filer med](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Felsökning av problem med Azure-filer med Linux](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [tilläggen]
  * [bilaga 1: med Fiddler att samla in HTTP och HTTPS-trafik]
  * [bilaga 2: använder Wireshark för att avbilda nätverkstrafik]
  * [tillägg 3: använda Microsoft Message Analyzer för att avbilda nätverkstrafik]
  * [Tillägg 4: Använda Excel för att visa mått och logga data]
  * [Tillägg 5: Övervaka med Programinsikter för Visual Studio Team Services]

## <a name="introduction"></a>Introduktion
Den här guiden visar hur du använder funktioner, till exempel Azure Storage Analytics klientsidan loggning i Azure Storage-klientbiblioteket och andra verktyg från tredje part för att identifiera, diagnostisera och felsöka Azure Storage-relaterade problem.

![][1]

Den här guiden är avsedd att läsas i första hand av utvecklare av online-tjänster som använder Azure Storage-tjänster och IT-experter som ansvarar för att hantera dessa online services. Målen med den här guiden är:

* Som hjälper dig att upprätthålla hälsotillstånd och prestanda för dina Azure Storage-konton.
* Att tillhandahålla nödvändiga processer och verktyg för att avgöra om ett problem eller problem i ett program som relaterar till Azure Storage.
* Du kan få tillämplig vägledning för att lösa problem som rör Azure Storage.

### <a name="how-this-guide-is-organized"></a>Hur den här guiden är organiserat
I avsnittet ”[övervakning lagringstjänsten]” beskriver hur du övervakar hälsotillstånd och prestanda för ditt Azure Storage-tjänster med hjälp av Azure Storage Analytics mätvärden (Storage-mätvärden).

I avsnittet ”[diagnostisera problem med lagring]” beskriver hur du diagnostisera problem med Azure Storage Analytics loggning (Storage loggning). Det beskriver också hur du aktiverar loggning för klientsidan med funktionerna i en av klientbiblioteken exempelvis Storage-klientbiblioteket för .NET eller Azure SDK för Java.

I avsnittet ”[slutpunkt till slutpunkt spårning]” beskriver hur du kan jämföra informationen i olika loggfiler och mätvärdesdata.

I avsnittet ”[felsökningsanvisningar]” ger riktlinjer för felsökning för några vanliga lagringsrelaterade problem du kan stöta på.

Den ”[tilläggen]” innehåller information om hur du använder andra verktyg som Wireshark och Netmon för analysera nätverk paketdata, Fiddler för att analysera HTTP/HTTPS-meddelanden och Microsoft Message Analyzer för korrelation mellan logga data.

## <a name="monitoring-your-storage-service"></a>Övervaka storage-tjänst
Om du är bekant med prestandaövervakning av Windows kan du se Storage-mätvärden som motsvarande Azure Storage för Windows-prestandaräknare. Storage-mätvärden innehåller en omfattande uppsättning mått (räknare i Prestandaövervakaren terminologi), till exempel tjänsttillgänglighet, totalt antal begäranden till tjänsten eller procent av lyckade begäranden till tjänsten. En fullständig lista över tillgängliga mått finns [Storage Analytics mätvärden tabellschemat](http://msdn.microsoft.com/library/azure/hh343264.aspx). Du kan ange om du vill att lagringstjänsten för att samla in och sammanställa mätvärden varje timme eller varje minut. Mer information om hur du aktivera mätvärden och övervaka dina lagringskonton finns [aktivera storage-mätvärden och visa mätvärdesdata](http://go.microsoft.com/fwlink/?LinkId=510865).

Du kan välja vilka timvis mått som du vill visa i den [Azure-portalen](https://portal.azure.com) och konfigurera regler som administratörer meddelas via e-post när en timvis måttet överskrider ett visst tröskelvärde. Mer information finns i [ta emot aviseringar](/azure/monitoring-and-diagnostics/monitoring-overview-alerts). 

Lagringstjänsten samlar in mått med bästa prestanda, men kan inte registrera varje Lagringsåtgärden.

Du kan visa mätvärden, till exempel tillgänglighet, totalt antal begäranden och genomsnittlig svarstid för nummer för ett lagringskonto i Azure-portalen. En meddelanderegel har också angetts som varnar administratörer om tillgänglighet sjunker under en viss nivå. Från att visa dessa data, ett möjligt området för undersökning är tabellen service lyckade är lägre än 100% (Mer information finns i avsnittet ”[mätvärdena visar låg PercentSuccess eller analytics loggposter innehålla åtgärder med transaktionsstatus av ClientOtherErrors]”).

Du bör alltid övervaka din Azure-program att säkerställa att de är felfria och fungerar som väntat genom att:

* Etablera vissa baslinjen mått för program som gör att du kan jämföra aktuella data och identifiera betydande förändringar i beteendet för Azure storage och ditt program. Värdena för baslinje-mätvärden, i många fall blir specifika program och du bör fastställa dem när du är prestandatestning ditt program.
* Registrering minut mått och använda dem för att övervaka aktivt för oväntade fel och inkonsekvenser, till exempel toppar i fel räknar eller be priser.
* Registrering av varje timme mått och använda dem för att övervaka genomsnittsvärden som genomsnittlig fel antal och begära priser.
* Undersöker möjliga problem med diagnosverktyg som beskrivs senare i avsnittet ”[diagnostisera problem med lagring]”.

Diagrammen i följande bild illustrerar hur medelvärdet som inträffar för varje timme mått kan dölja toppar i aktiviteten. Varje timme mått visas en konstant andel begäranden under den minuten mått avslöja variationer som verkligen äger rum.

![][3]

Resten av det här avsnittet beskriver vilka mått som du bör övervaka och varför.

### <a name="monitoring-service-health"></a>Övervakning av tjänstens hälsa
Du kan använda den [Azure-portalen](https://portal.azure.com) att visa hälsotillståndet för lagringstjänsten (och andra Azure-tjänster) i alla regioner som Azure runtom i världen. Övervakning kan påverkar du direkt se om problemet inte kan kontrollera Storage-tjänsten i den region som du använder för ditt program.

Den [Azure-portalen](https://portal.azure.com) kan också ge meddelanden om incidenter som påverkar olika Azure-tjänster.
Obs: Den här informationen tidigare var tillgänglig, tillsammans med historiska data på den [Azure Service instrumentpanelen](http://status.azure.com).

När den [Azure-portalen](https://portal.azure.com) samlar in hälsouppgifter från inuti Azure datacenter (innanför ut övervakning), du kan också överväga införandet av en utanför metod för att generera syntetiska transaktioner som regelbundet använder Azure-baserad webbprogrammet från flera platser. Tjänster som erbjuds av [Dynatrace](http://www.dynatrace.com/en/synthetic-monitoring) och Programinsikter för Visual Studio Team Services är exempel på den här metoden. Mer information om Application Insights för Visual Studio Team Services finns i tillägget ”[tillägg 5: övervaka med Programinsikter för Visual Studio Team Services](#appendix-5)”.

### <a name="monitoring-capacity"></a>Övervakning av kapacitet
Storage-mätvärden lagrar bara kapacitetsdata för blob-tjänsten eftersom blobbar vanligtvis hänsyn till största del av lagrade data (vid tidpunkten för skrivning, det går inte att använda Storage-mätvärden för att övervaka kapacitet för tabeller och köer). Du hittar dessa data i den **$MetricsCapacityBlob** tabell om du har aktiverat övervakning för Blob-tjänsten. Storage-mätvärden registrerar dessa data en gång per dag, och du kan använda värdet för den **RowKey** att fastställa om raden innehåller en entitet som är kopplat till användardata (värdet **data**) eller analysdata (värdet **analytics**). Varje lagrade entitet innehåller information om hur mycket lagringsutrymme som används (**kapacitet** mätt i byte) och det aktuella antalet behållare (**ContainerCount**) och blobbar (**ObjectCount**) används i lagringskontot. Mer information om kapacitetsdata som lagras i den **$MetricsCapacityBlob** tabell, se [Storage Analytics mätvärden tabellschemat](http://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> Du bör övervaka de här värdena för en tidig varning att du närmar dig kapacitetsbegränsningar för ditt lagringskonto. Du kan lägga till Varningsregler för att meddela dig om sammanställd lagringsanvändning överskrider eller understiger tröskelvärden som du anger i Azure-portalen.
> 
> 

Om du behöver hjälp uppskatta storlek för olika lagringsobjekt,, till exempel blobbar, finns i bloggposten [förstå Azure Storage fakturering – bandbredd, transaktioner och kapacitet](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

### <a name="monitoring-availability"></a>Övervaka tillgänglighet
Du bör övervaka tillgängligheten för storage-tjänster i ditt lagringskonto genom att övervaka värdet i den **tillgänglighet** kolumn i tabellerna timvis eller minut mått – **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. Den **tillgänglighet** kolumnen innehåller ett procentvärde som visar tillgängligheten för tjänsten eller API-åtgärd som representeras av raden (den **RowKey** visar om raden innehåller mått för tjänsten som helhet eller för en specifik åtgärd API).

Alla värdet som är mindre än 100% anger att vissa lagringsbegäranden skickas. Du kan se varför de inte genom att undersöka andra kolumner i mätvärdesdata som visar antalet begäranden med olika feltyper som **ServerTimeoutError**. Du bör se **tillgänglighet** tillfälligt faller under 100% skäl, till exempel tillfälligt server-timeout när tjänsten flyttar partitioner till bättre belastningsutjämning begäran; logik för omprövning i ditt klientprogram ska hantera dessa återkommande villkor. Artikeln [Storage Analytics loggade åtgärder och statusmeddelanden](http://msdn.microsoft.com/library/azure/hh343260.aspx) visar transaktionstyper med Storage-mätvärden i dess **tillgänglighet** beräkning.

I den [Azure-portalen](https://portal.azure.com), kan du lägga till Varningsregler för att meddela dig om **tillgänglighet** för en tjänst som ligger under ett tröskelvärde som du anger.

Den ”[felsökningsanvisningar]” i den här guiden beskriver några vanliga storage service problem som rör tillgängligheten.

### <a name="monitoring-performance"></a>Övervaka prestanda
Du kan använda följande mått från varje timme och minut mått tabeller för att övervaka prestanda för storage-tjänster.

* Värdena i den **AverageE2ELatency** och **AverageServerLatency** kolumner visar den genomsnittliga tiden lagringstjänsten eller API åtgärdstypen tar det att bearbeta begäranden. **AverageE2ELatency** är ett mått på svarstid för slutpunkt-till-slutpunkt som innehåller den tid det tar att läsa begäran och skicka svaret förutom den tid det tar att bearbeta begäran (därför innehåller Nätverksfördröjningen när begäran når lagring tjänst). **AverageServerLatency** är ett mått på bara bearbetningstiden och därför utesluter alla nätverks-svarstid som är relaterade till att kommunicera med klienten. Se avsnittet ”[mätvärdena visar AverageE2ELatency hög och låg AverageServerLatency]” senare i den här guiden för en beskrivning av varför det finnas betydande skillnader mellan dessa två värden.
* Värdena i den **TotalIngress** och **TotalEgress** kolumner visar den totala mängden data i byte, kommer till och kommer utanför lagringstjänsten eller via en viss typ för API-åtgärden.
* Värdena i den **TotalRequests** kolumn visar det totala antalet förfrågningar som tar emot lagringstjänsten av API-åtgärd. **TotalRequests** är det totala antalet förfrågningar som tar emot storage-tjänst.

Normalt ska övervakas av oväntade förändringar i någon av dessa värden som en indikator på att du har ett problem som kräver en undersökning.

I den [Azure-portalen](https://portal.azure.com), kan du lägga till Varningsregler för att meddela dig om något av prestandamåtten för den här tjänsten faller under eller överskrider ett tröskelvärde som du anger.

Den ”[felsökningsanvisningar]” i den här guiden beskriver några vanliga storage service problem relaterade till prestanda.

## <a name="diagnosing-storage-issues"></a>Diagnostisera problem med lagring
Det finns ett antal sätt att kan du blir medveten om problem och frågor i ditt program, inklusive:

* Ett allvarligt fel inträffar som gör att programmet kraschar eller slutar fungera.
* Betydande förändringar från baslinjevärdena i de mätvärden som du övervakar enligt beskrivningen i föregående avsnitt ”[övervakning lagringstjänsten]”.
* -Rapporter från användare av ditt program inte har en viss åtgärd slutförs som förväntat eller att vissa funktionen inte fungerar.
* Fel som har genererats i programmet som visas i loggfiler eller via någon annan metod för meddelande.

Normalt problem relaterade till Azure-lagringstjänster indelas i fyra kategorier:

* Programmet har ett prestandaproblem rapporteras av användarna eller framgick av ändringar i prestandamåtten.
* Det finns ett problem med Azure Storage-infrastrukturen i en eller flera regioner.
* Programmet har uppstått ett fel rapporterat av användarna eller visas med en ökning i någon av fel antal mått som du övervakar.
* Under utveckling och testning, kan du använda den lokala lagringsemulatorn; Du kan stöta på problem som rör särskilt användning av storage-emulatorn.

I följande avsnitt beskrivs de steg som du bör följa att diagnostisera och felsöka problem i alla dessa fyra kategorier. I avsnittet ”[felsökningsanvisningar]” senare i den här guiden ger fler detaljer för några vanliga problem du kan stöta på.

### <a name="service-health-issues"></a>Hälsotillståndsproblem med tjänsten för
Tjänstens hälsotillstånd är vanligtvis inte kan kontrollera. Den [Azure-portalen](https://portal.azure.com) ger information om pågående problem med Azure-tjänster inklusive lagringstjänster. Om du har valt för Geo-Redundant lagring med läsbehörighet när du skapade ditt lagringskonto, kan sedan om dina data inte är tillgänglig på den primära platsen tillämpningsprogrammet tillfälligt växla till skrivskyddad kopia på den sekundära platsen. Om du vill läsa från sekundärt måste tillämpningsprogrammet kan växla mellan att använda de primära och sekundära lagringsplatser och kan arbeta i begränsat läge med skrivskyddade data. Azure Storage-klientbibliotek kan du definiera en återförsöksprincip som kan läsa från sekundär lagring om en läsning från primära lagring misslyckas. Programmet måste också vara medveten om att data på den sekundära platsen är överensstämmelse. Mer information finns i bloggposten [Azure lagringsalternativ för redundans och Geo-Redundant lagring med läsbehörighet](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a>prestandaproblem
Upplevelsen av programprestanda kan vara högst subjektiv, särskilt från ett användarperspektiv. Därför är det viktigt att ha tillgång till jämförelsemått, så att du tydligt ser när det blir problem med prestandan. Många faktorer kan påverka prestanda för ett Azure storage-tjänst ur klientens perspektiv för programmet. Dessa faktorer kan fungera i lagringstjänsten, klienten eller nätverksinfrastruktur. Det är därför viktigt att ha en strategi för att identifiera prestandaproblem ursprung.

När du har identifierat sannolika var orsaken till prestandaproblemet från mätvärdena använda du sedan loggfilerna för att hitta detaljerad information för att diagnostisera och Felsök problemet.

I avsnittet ”[felsökningsanvisningar]” senare i den här guiden innehåller mer information om några vanliga prestandarelaterade problem du kan stöta på.

### <a name="diagnosing-errors"></a>Diagnostisera fel
Användare av ditt program kan meddela dig om felen som rapporteras av klientprogrammet. Storage-mätvärden även information om antal fel för olika typer av storage-tjänster som **NetworkError**, **ClientTimeoutError**, eller **AuthorizationError**. Medan Storage-mätvärden spelar bara antal olika feltyper, hittar du mer information om enskilda begäranden genom att undersöka serversidan och klientsidan loggar för nätverket. Normalt ger HTTP-statuskoden som returnerades av lagringstjänsten en indikation om varför begäran misslyckades.

> [!NOTE]
> Kom ihåg att du bör se återkommande fel: till exempel fel på grund av tillfälliga nätverksproblem eller programfel.
> 
> 

Följande resurser är användbara för att förstå lagringsrelaterade status och felkoder:

* [Vanliga REST API-felkoder](http://msdn.microsoft.com/library/azure/dd179357.aspx)
* [Felkoder för BLOB-tjänst](http://msdn.microsoft.com/library/azure/dd179439.aspx)
* [Felkoder för kön](http://msdn.microsoft.com/library/azure/dd179446.aspx)
* [Felkoder för tabellen](http://msdn.microsoft.com/library/azure/dd179438.aspx)
* [Felkoder för filen](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a>Storage-emulatorn problem
Azure SDK innehåller en storage-emulatorn som du kan köra på en arbetsstation för utveckling. Den här emulatorn simulerar de flesta beteendet för Azure storage-tjänster och är användbart under utveckling och testning, så att du kan köra program som använder Azure storage-tjänster utan att behöva en Azure-prenumeration och ett Azure storage-konto.

Den ”[felsökningsanvisningar]” i den här guiden beskriver några vanliga problem som uppstod med storage-emulatorn.

### <a name="storage-logging-tools"></a>Loggningsverktyg för lagring
Storage-loggning innehåller serversidan loggningen av lagring i ditt Azure storage-konto. Mer information om hur du aktiverar loggning på serversidan och komma åt loggdata finns [aktivera loggning för lagring och åtkomst till loggdata](http://go.microsoft.com/fwlink/?LinkId=510867).

Storage-klientbiblioteket för .NET kan du samla in loggdata för klientsidan som är kopplat till storage-åtgärder som utförs av ditt program. Mer information finns i artikeln om [klientloggning med .NET Storage Client Library](http://go.microsoft.com/fwlink/?LinkId=510868).

> [!NOTE]
> I vissa fall (till exempel SAS auktoriseringsproblem) kan en användare rapportera ett fel som du kan hitta inga data om begäran i loggarna för serversidan lagring. Du kan använda funktioner för loggning av Storage-klientbiblioteket för att ta reda på om problemet beror på klienten eller använda verktyg för nätverksövervakning för att undersöka nätverket.
> 
> 

### <a name="using-network-logging-tools"></a>Med hjälp av verktyg för loggning
Du kan avbilda trafik mellan klienten och servern för att tillhandahålla detaljerad information om de data som klienten och servern utbyter och underliggande nätverksförhållanden. Användbara verktyg för loggning är:

* [Fiddler](http://www.telerik.com/fiddler) är en gratis webbplatser felsökning proxy som du kan undersöka sidhuvuden och nyttolasten för HTTP och HTTPS-begäran och svar-meddelanden. Mer information finns i [bilaga 1: använder Fiddler för att samla in HTTP och HTTPS-trafik](#appendix-1).
* [Microsoft Network Monitor (Netmon)](http://www.microsoft.com/download/details.aspx?id=4865) och [Wireshark](http://www.wireshark.org/) är ledigt nätverk protokollet analyzers som gör att du kan visa detaljerad paketinformationen för en mängd olika nätverksprotokoll. Läs mer om Wireshark ”[bilaga 2: använda Wireshark att avbilda nätverkstrafik](#appendix-2)”.
* Microsoft Message Analyzer är ett verktyg från Microsoft som ersätter Netmon och att hämta paket nätverksdata, kan du visa och analysera loggdata som hämtats från andra verktyg. Mer information finns i ”[tillägg 3: använda Microsoft Message Analyzer att avbilda nätverkstrafik](#appendix-3)”.
* Om du vill utföra en grundläggande anslutningstest för att kontrollera att klientdatorn kan ansluta till Azure storage-tjänst i nätverket, du kan göra detta med vanlig **ping** verktyget på klienten. Du kan använda den [ **tcping** verktyget](http://www.elifulkerson.com/projects/tcping.php) vill kontrollera anslutningen.

Loggdata från lagring loggning och Storage-klientbiblioteket ska vara tillräcklig för att diagnostisera problem i många fall, men i vissa fall kan du behöva mer detaljerad information som kan tillhandahålla dessa verktyg för loggning. Med Fiddler visa HTTP och HTTPS-meddelanden kan du visa huvudet och nyttolasten data som skickas till och från storage-tjänster som gör att du kan granska hur ett klientprogram försöker lagringsåtgärder. Protokollet analyzers, till exempel Wireshark fungerar på paketnivå som gör att du kan visa data för TCP, vilket gör att du kan felsöka förlorade paket och problem med nätverksanslutningen. Message Analyzer kan köras på både HTTP och TCP-lager.

## <a name="end-to-end-tracing"></a>Slutpunkt till slutpunkt-spårning
Spårning för slutpunkt till slutpunkt med ett antal olika loggfiler är en användbar teknik för att undersöka potentiella problem. Du kan använda datum/tid-informationen från dina data mått som en indikation på var du vill börja söka i loggfiler för detaljerad information som hjälper dig att felsöka problemet.

### <a name="correlating-log-data"></a>Korrelerar loggdata
När du visar loggar från klientprogram spårar nätverk och lagring för serversidan loggning är det viktigt att kunna korrelera begäranden mellan de olika loggfilerna. Loggfilerna innehåller ett antal olika fält som är användbara Korrelations-ID: n. Klient-ID för begäran är mest användbara fältet som används för att korrelera poster i olika loggarna. Men ibland kan det vara praktiskt att använda server begäran-ID eller tidsstämplar. Följande avsnitt innehåller mer information om dessa alternativ.

### <a name="client-request-id"></a>ID för klientbegäran
Storage-klientbiblioteket genererar automatiskt ett unikt ID för klientbegäran för varje begäran.

* I loggen för klientsidan som skapar Storage-klientbiblioteket, klient-ID för begäran som visas i den **ID för klientbegäran** i varje loggpost som rör begäran.
* En spårning i nätverket, till exempel en avbildas Fiddler, klient-ID för begäran är synligt i begärandemeddelanden som den **x-ms-client-request-id** värde för HTTP-huvudet.
* I serversidan lagring loggning loggfilen visas klient-ID för begäran i kolumnen klient-ID för begäran.

> [!NOTE]
> Det är möjligt för flera begäranden att dela samma begäran-ID för klienten eftersom klienten tilldelas värdet (även om Storage-klientbiblioteket tilldelar automatiskt ett nytt värde). När klienten försöker dela alla försök samma klient-begäran-ID. När det gäller en batch som skickats från klienten har batchen en enskild klient begärande-ID.
> 
> 

### <a name="server-request-id"></a>Server-ID för begäran
Lagringstjänsten genererar automatiskt server begäran-ID: n.

* Det begärt-ID visas i loggen lagring loggning serversidan den **huvud för begäran-ID** kolumn.
* I en spårning i nätverket, till exempel en avbildas av Fiddler, visas det begärt-ID i svarsmeddelanden som den **x-ms-begäran-id** värde för HTTP-huvudet.
* I loggen för klientsidan som skapar Storage-klientbiblioteket, det begärt-ID som visas i den **åtgärden Text** för loggposten visar information om svaret från servern.

> [!NOTE]
> Lagringstjänsten tilldelar alltid en unik server begärande-ID till varje begäran som den tar emot så att varje nytt försök från klienten och varje funktion som ingår i en batch har ett unikt server begäran-ID.
> 
> 

Om Storage-klientbiblioteket genererar en **StorageException** i klienten den **RequestInformation** -egenskapen innehåller en **RequestResult** -objekt som innehåller en **ServiceRequestID** egenskapen. Du kan också komma åt en **RequestResult** objekt från en **OperationContext** instans.

Kodexemplet nedan visar hur du anger en anpassad **ClientRequestId** värde genom att koppla en **OperationContext** objekt-begäran till storage-tjänst. Visar även hur du hämtar den **ServerRequestId** värde från svarsmeddelandet.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
OperationContext oc = new OperationContext();
oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

try
{
    CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
    ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
    var downloadToPath = string.Format("./{0}", blob.Name);
    using (var fs = File.OpenWrite(downloadToPath))
    {
        blob.DownloadToStream(fs, null, null, oc);
        Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
    }
}
catch (StorageException storageException)
{
    Console.WriteLine("Storage exception {0} occurred", storageException.Message);
    // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
    foreach (var result in oc.RequestResults)
    {
            Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
    }
}
```

### <a name="timestamps"></a>Tidsstämplar
Du kan också använda tidsstämplar för att hitta relaterade loggposter, men var försiktig med en förskjutning av klockan mellan klienten och servern som kan finnas. Sök plus eller minus 15 minuter för att matcha serversidan poster baserat på tidsstämpeln på klienten. Kom ihåg att blobmetadata för blob som innehåller mått Anger tidsintervall för de mätvärden som lagras i blob. Den här tidsintervallet är användbart om du har många mått BLOB för samma minut eller timme.

## <a name="troubleshooting-guidance"></a>Riktlinjer för felsökning
Det här avsnittet hjälper dig med diagnos och felsökning av vanliga problem ditt program kan uppstå när du använder Azure-lagringstjänster. Använd listan nedan för att hitta informationen som är relevant för ditt problem.

**Felsökning av beslutsträdet**

---
Problemet relaterar till prestanda för en av lagringstjänsterna?

* [mätvärdena visar AverageE2ELatency hög och låg AverageServerLatency]
* [Mätningar visar låga värden för AverageE2ELatency och AverageServerLatency, men klienten har ändå långa svarstider]
* [Mätningar visar ett högt värde för AverageServerLatency]
* [Du upplever oväntade fördröjningar i en köad meddelandeleverans]

---
Ditt problem som rör tillgängligheten för en av lagringstjänsterna?

* [mätvärdena visar en ökning i PercentThrottlingError]
* [mätvärdena visar en ökning i PercentTimeoutError]
* [Mätningar visar en ökning i PercentNetworkError]

---
 Klientprogrammet tar emot en HTTP-svar som 4XX (till exempel 404) från en lagringstjänsten?

* [Klienten tar emot HTTP 403 (förbjuden) meddelanden]
* [Klienten tar emot HTTP 404 (inget hittas) meddelanden]
* [Klienten tar emot HTTP 409 (konflikt) meddelanden]

---
[mätvärdena visar låg PercentSuccess eller analytics loggposter innehålla åtgärder med transaktionsstatus av ClientOtherErrors]

---
[Kapacitetsdata visar en oväntad ökning i kapacitetsförbrukning för lagring]

---
[Det uppstår oväntade omstarter av virtuella datorer som har ett stort antal anslutna virtuella hårddiskar]

---
[Problemet uppstår från med hjälp av storage-emulatorn för utveckling eller testning]

---
[Det uppstår problem med att installera Azure SDK för .NET]

---
[Du har ett annat problem med en storage-tjänst]

---
### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Mätvärdena visar AverageE2ELatency hög och låg AverageServerLatency
Bilden nedan från den [Azure-portalen](https://portal.azure.com) övervakning verktyget visar ett exempel där den **AverageE2ELatency** är avsevärt högre än den **AverageServerLatency**.

![][4]

Lagringstjänsten beräknas endast måttet **AverageE2ELatency** för lyckade begäranden och till skillnad från **AverageServerLatency**, innehåller den tid som klienten använder för att skicka data och ta emot bekräftelse från storage-tjänst. Därför skillnad mellan **AverageE2ELatency** och **AverageServerLatency** kan vara antingen på grund av klientprogrammet som svarar långsamt eller på grund av villkor i nätverket.

> [!NOTE]
> Du kan också visa **E2ELatency** och **ServerLatency** logga data för enskilda lagringsåtgärder i loggning av lagring.
> 
> 

#### <a name="investigating-client-performance-issues"></a>Undersöka prestandaproblem för klienten
Möjliga orsaker till klienten svarar långsamt innehåller med ett begränsat antal tillgängliga anslutningar eller -trådar, eller ha ont om resurser, t.ex CPU, minne eller nätverket bandbredd. Du kanske kan lösa problemet genom att ändra klientkod så att mer effektivt (till exempel med hjälp av asynkrona anrop till tjänsten storage) eller genom att använda en större virtuell dator (med flera kärnor och mer minne).

För tjänsterna tabell och kön algoritmen Nagle kan också orsakas av hög **AverageE2ELatency** jämfört med **AverageServerLatency**: Mer information finns i inlägg [Nagle's Algoritmen är inte egna gentemot små begäran](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx). Du kan inaktivera algoritmen Nagle i kod med hjälp av den **ServicePointManager** klassen i den **System.Net** namnområde. Du bör göra detta innan du gör ett anrop till tabellen eller queue-tjänster i ditt program eftersom det inte påverkar anslutningar som redan är öppna. I följande exempel kommer från den **Application_Start** metod i en arbetsroll.

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
tableServicePoint.UseNagleAlgorithm = false;
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

Du bör kontrollera klientsidan-loggarna för att se hur många begäranden att klientprogrammet skickar och Sök efter allmän .NET relaterade flaskhalsar i din klient, till exempel CPU, .NET skräpinsamling, belastningen på nätverket eller minne. Som en startpunkt för felsökning av .NET-klientprogram finns [felsökning och spårning Profiling](http://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Undersöka problem med nätverkssvarstiden
Fördröjningar som slutpunkt till slutpunkt på grund av nätverket är vanligtvis på grund av övergående förhållanden. Du kan undersöka både tillfälliga och permanenta nätverksproblem, till exempel ignorerade paket med hjälp av verktyg som Wireshark eller Microsoft Message Analyzer.

Mer information om hur du använder Wireshark felsökning av nätverksproblem finns ”[bilaga 2: använder Wireshark för att avbilda nätverkstrafik]”.

Mer information om hur du använder Microsoft Message Analyzer felsökning av nätverksproblem finns ”[tillägg 3: använda Microsoft Message Analyzer för att avbilda nätverkstrafik]”.

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Mätvärdena visar låg AverageE2ELatency och låg AverageServerLatency men klienten har hög fördröjning
I det här fallet är den troligaste orsaken en fördröjning i lagring begäran når storage-tjänst. Du bör undersöka varför förfrågningar från klienten inte gör det via blob-tjänsten.

En möjlig orsak för klienten att skicka förfrågningar är att det finns ett begränsat antal tillgängliga anslutningar eller -trådar.

Också kontrollera om klienten utför upprepade försök och ta reda på varför om det är. För att avgöra om klienten utför flera försök, kan du:

* Granska loggarna för Storage Analytics. Om flera försök sker, visas flera åtgärder med samma klient begäran-ID, men med olika serverbegäran ID: N.
* Granska loggarna för klienten. Utförlig loggning indikerar att ett nytt försök har genomförts.
* Felsöka din kod och kontrollera egenskaperna för den **OperationContext** objektet som är associerat med begäran. Om åtgärden har gjorts i **RequestResults** egenskap innehåller flera unika serverbegäran ID: N. Du kan också kontrollera start- och sluttider för varje begäran. Mer information finns i avsnittet kodexemplet [Server begäran-ID].

Om det inte finns några problem i klienten, bör du undersöka potentiella nätverksproblem, till exempel paketförlust. Du kan använda verktyg som Wireshark eller Microsoft Message Analyzer för att undersöka nätverksproblem.

Mer information om hur du använder Wireshark felsökning av nätverksproblem finns ”[bilaga 2: använder Wireshark för att avbilda nätverkstrafik]”.

Mer information om hur du använder Microsoft Message Analyzer felsökning av nätverksproblem finns ”[tillägg 3: använda Microsoft Message Analyzer för att avbilda nätverkstrafik]”.

### <a name="metrics-show-high-AverageServerLatency"></a>Mätvärdena visar hög AverageServerLatency
Vid hög **AverageServerLatency** för blob-hämtningsbegäranden, bör du använda lagring loggning loggarna om det finns upprepade begäranden för samma blob (eller uppsättning blobbar). För blob överföringsbegäranden, bör du undersöka vilka blockera klienten ligger med (till exempel block som mindre än 64 kB kan resultera i kostnaderna om läsningar finns också i mindre än 64 kB blocken) och om flera klienter överför block till samma blob i stycke llellt. Du bör också kontrollera mått per minut för toppar i antalet begäranden som resulterar i mer än den per andra skalbarhetsmål: också se ”[mätvärdena visar en ökning i PercentTimeoutError]”.

Om du ser högt **AverageServerLatency** blob hämtas begäranden när det upprepas begär samma blob eller uppsättning blobbar och sedan bör du cachelagring dessa blobar med hjälp av Azure Cache eller Azure Content Delivery Network (CDN). Du kan förbättra genomflödet med hjälp av en större blockstorlek för överföringsbegäranden. För frågor till tabeller är det också möjligt att implementera klientcachelagring på klienter som utför åtgärder för samma fråga och där data inte ändras ofta.

Hög **AverageServerLatency** värden kan också vara ett symtom på dåligt utformad tabeller eller frågor att resultera i genomsökningen eller som följer Lägg till/lägga ett mönster. Mer information finns i ”[mätvärdena visar en ökning i PercentThrottlingError]”.

> [!NOTE]
> Du hittar en omfattande checklista prestanda checklista här: [Microsoft Azure Storage prestanda och skalbarhet checklista](storage-performance-checklist.md).
> 
> 

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Det uppstår oväntade fördröjningar i leverans av meddelanden i en kö
Om det uppstår en fördröjning mellan den tid som ett program lägger till ett meddelande till en kö och den tid det blir tillgängligt för att läsa från kön, bör du utföra följande steg för att diagnostisera problemet:

* Kontrollera att programmet har på att lägga till meddelanden i kön. Kontrollera att programmet inte försöker på nytt den **AddMessage** metoden flera gånger innan du lyckas. Storage-klientbibliotek loggarna visas alla upprepade försök av lagringsåtgärder.
* Kontrollera det finns ingen klocka skeva mellan worker-rollen som lägger till meddelandet i kön och den arbetsroll som läser meddelandet från kön som gör det visas som om det uppstår en fördröjning i bearbetningen.
* Kontrollera om den arbetsroll som läser meddelanden från kön skadad. Om en kö klient anropar den **GetMessage** metod men misslyckas att svara med en bekräftelse meddelandet förblir osynliga kön tills den **invisibilityTimeout** period har löpt ut. Nu blir meddelandet tillgängliga för bearbetning av igen.
* Kontrollera om kölängden ökar med tiden. Detta kan inträffa om du inte har tillräcklig anställda att utföra alla meddelanden som andra personer monterar kön. Kontrollera också mått för att se om delete-begäranden skickas och dequeue räkna på meddelanden som kan tyda på upprepade misslyckade försök att ta bort meddelandet.
* Granska loggarna lagring loggning för alla åtgärder i kön som har högre än förväntat **E2ELatency** och **ServerLatency** värden under en längre tid än vanligt.

### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Mätvärdena visar en ökning i PercentThrottlingError
Bandbreddsbegränsning fel uppstår när du överskrider skalbarhetsmål för storage-tjänst. Storage service begränsas till att säkerställa att ingen enskild klient eller klient kan använda tjänsten på bekostnad av andra. Mer information finns i [Azure Storage skalbarhets- och prestandamål](storage-scalability-targets.md) detaljer om skalbarhetsmål för storage-konton och prestandamål för partitioner i storage-konton.

Om den **PercentThrottlingError** måttet visar en ökning i procent av begäranden som misslyckas med felet bandbreddsbegränsning, måste du undersöka en av två scenarier:

* [Tillfällig ökning PercentThrottlingError]
* [Permanent ökning PercentThrottlingError fel]

En ökning av **PercentThrottlingError** ofta uppstår samtidigt som en ökning av antalet förfrågningar om lagring, eller när du först läsa testa ditt program. Detta kan också visa sig i klienten som ”503 servern upptagen” eller ”tidsgräns för 500 åtgärd” HTTP statusmeddelanden från lagringsåtgärder.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Tillfällig ökning PercentThrottlingError
Om du ser toppar i värdet för **PercentThrottlingError** som sammanfaller med perioder med hög aktivitet för program bör du implementera en exponentiell (inte linjär) inte strategi för nya försök i din klient. Försök inte minska omedelbar belastningen på partitionen och hjälpa ditt program om du vill jämna ut toppar i trafiken. Mer information om hur du implementerar försök principer med hjälp av Storage-klientbiblioteket finns [Microsoft.WindowsAzure.Storage.RetryPolicies Namespace](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx).

> [!NOTE]
> Du kan också se toppar i värdet för **PercentThrottlingError** som inte krockar med perioder med hög aktivitet för program: den troligaste orsaken är lagringstjänsten flytta partitioner för att förbättra belastningsutjämning.
> 
> 

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Permanent ökning PercentThrottlingError fel
Om du ser ett konsekvent högt värde för **PercentThrottlingError** efter en permanent ökning i din transaktionsvolymer eller när du utför din första last testar för ditt program, måste du utvärdera hur ditt program använder partitioner för lagring och om den närmar sig skalbarhetsmål för ett lagringskonto. Till exempel om du ser begränsning fel på en kö (som räknas som en enda partition), bör sedan du använda ytterligare köer kan sprida sig transaktioner över flera partitioner. Om du ser begränsning fel på en tabell, måste du bör du sprida dina transaktioner över flera partitioner med hjälp av ett brett spektrum av partitionsnyckelvärden med en annan partitioneringsschema. En vanlig orsak till det här problemet är prepend/lägga till ett mönstret där du väljer datumet som partitionsnyckel och sedan alla data på en viss dag skrivs till en partition: belastning detta kan resultera i en flaskhals för skrivning. Överväg att en annan partitionering design eller utvärdera om med blob storage kan vara en bättre lösning. Också kontrollera om begränsning sker till följd av toppar i trafiken och undersök sätt av Utjämning mönstret för förfrågningar.

Om du distribuerar dina transaktioner över flera partitioner, måste du fortfarande vara medveten om gränserna skalbarhet för lagringskontot. Till exempel om du använde tio köer bearbetning högst 2 000 1KB meddelanden per sekund vara på övergripande högst 20 000 meddelanden per sekund för lagringskontot. Om du måste bearbeta fler än 20 000 enheter per sekund, bör du använda flera lagringskonton. Du bör också ha i åtanke att storleken på din begäran och entiteter har påverkas när lagringstjänsten begränsar klienterna: Om du har större begäranden och enheter kan du kanske att begränsas snabbare.

Ineffektiv frågedesigntillståndet kan också medföra att träffa skalbarhetsbegränsningar för tabellpartitioner. Till exempel behöver en fråga med ett filter som endast väljer en procent av enheterna i en partition men som söker igenom alla entiteter i en partition åtkomst till varje entitet. Varje entitet läsa räknas som en av det totala antalet transaktioner i den aktuella partitionen; Därför kan du enkelt kan nå skalbarhetsmål.

> [!NOTE]
> Din prestandatestning bör Visa inga ineffektiv frågan Designer i ditt program.
> 
> 

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Mätvärdena visar en ökning i PercentTimeoutError
Din mätvärdena visar en ökning av **PercentTimeoutError** för en storage-tjänster. På samma gång klienten tar emot en stor volym med ”tidsgräns för 500 åtgärd” HTTP statusmeddelanden från lagringsåtgärder.

> [!NOTE]
> Du kan se timeout-fel tillfälligt som lagringstjänsten läsa in saldon begäranden genom att flytta en partition till en ny server.
> 
> 

Den **PercentTimeoutError** mått är en sammanställning av följande mått: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**, och **SASServerTimeoutError**.

Server-timeout orsakas av ett fel på servern. Klient-timeout bero på att en åtgärd på servern har överskridit tidsgränsen som angetts av klienten. en klient som använder Storage-klientbiblioteket kan till exempel ange en tidsgräns för en åtgärd med hjälp av den **ServerTimeout** -egenskapen för den **QueueRequestOptions** klass.

Server-timeout tyda på ett problem med tjänsten storage som kräver ytterligare undersökning. Du kan använda mått för att se om du träffa skalbarhetsbegränsningar för tjänsten och identifiera eventuella toppar i trafik som kan orsaka det här problemet. Om problemet är tillfälligt, kan det bero på belastningsutjämning aktivitet i tjänsten. Om problemet inte orsakas av programmet träffa skalbarhetsbegränsningar för tjänsten är beständig, ska du höja ett supportproblem. För klient-timeout måste du bestämma om tidsgränsen har angetts till ett lämpligt värde i klienten och antingen ändra timeout-värdet i klienten eller undersöka hur du kan förbättra prestanda för åtgärder i lagringstjänsten, till exempel genom att optimera dina frågor för tabellen eller minska storleken på dina meddelanden.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>Mätvärdena visar en ökning i PercentNetworkError
Din mätvärdena visar en ökning av **PercentNetworkError** för en storage-tjänster. Den **PercentNetworkError** mått är en sammanställning av följande mått: **NetworkError**, **AnonymousNetworkError**, och **SASNetworkError**. Dessa visas när lagringstjänsten uppstår ett fel när klienten begär lagring.

Den vanligaste orsaken till felet är en klient kopplar från innan tidsgränsen upphör att gälla i lagringstjänsten. Undersök koden i din klient att förstå varför och när klienten kopplas från storage-tjänst. Du kan också använda Wireshark, Microsoft Message Analyzer eller Tcping för att undersöka problem med nätverksanslutningen från klienten. Dessa verktyg beskrivs i den [tilläggen].

### <a name="the-client-is-receiving-403-messages"></a>Klienten tar emot HTTP 403 (förbjuden) meddelanden
Om klientprogrammet har egna HTTP 403 (förbjuden) fel är en trolig orsak att klienten använder en utgången delade signatur åtkomst (SAS) när den skickar en begäran om lagring (även om andra möjliga orsaker kan vara klockan skeva ogiltig nycklar och tom rubriker). Om en utgången SAS-nyckel är orsaken, visas inte några poster i lagring loggning loggdata för serversidan. I följande tabell visas ett exempel på klientsidan loggen genereras av Storage-klientbiblioteket som illustrerar det här problemet inträffar:

| Källa | Utförlighet | Utförlighet | ID för klientbegäran | Åtgärden text |
| --- | --- | --- | --- | --- |
| Microsoft.WindowsAzure.Storage |Information |3 |85d077ab-... |Starta åtgärden med platsen primära per plats läge PrimaryOnly. |
| Microsoft.WindowsAzure.Storage |Information |3 |85d077ab-... |Starta synkron begäran om att https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14 &amp;sr = c&amp;si = mypolicy&amp;sig = OFnd4Rd7z01fIvh % 2BmcR6zbudIH2F5Ikm % 2FyhNYZEmJNQ % 3D&amp;api-version = 2014-02-14. |
| Microsoft.WindowsAzure.Storage |Information |3 |85d077ab-... |Väntar på svar. |
| Microsoft.WindowsAzure.Storage |Varning |2 |85d077ab-... |Ett undantag uppstod under väntan på svar: fjärrservern returnerade ett fel: (403) förbjuden. |
| Microsoft.WindowsAzure.Storage |Information |3 |85d077ab-... |Svar mottogs. Statuskod = 403 begäran-ID = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = ETag =. |
| Microsoft.WindowsAzure.Storage |Varning |2 |85d077ab-... |Ett undantag uppstod under åtgärden: fjärrservern returnerade ett fel: (403) nekad... |
| Microsoft.WindowsAzure.Storage |Information |3 |85d077ab-... |Kontrollerar om åtgärden bör provas igen. Antal försök = 0, HTTP-statuskod = 403 undantag = fjärrservern returnerade ett fel: (403) nekad... |
| Microsoft.WindowsAzure.Storage |Information |3 |85d077ab-... |Nästa plats har angetts till primära, baserat på plats. |
| Microsoft.WindowsAzure.Storage |Fel |1 |85d077ab-... |Återförsöksprincipen tillät inte för ett nytt försök. Misslyckas med fjärrservern returnerade ett fel: (403) förbjuden. |

Du bör undersöka varför SAS-token upphör att gälla innan klienten skickar token till servern i det här scenariot:

* Normalt bör du inte ange en starttid som när du skapar en SAS för en klient att använda direkt. Om det finns liten klocka skillnader mellan värden generera SAS med hjälp av den aktuella tiden och storage-tjänst är det möjligt för storage-tjänsten för att få en SAS inte är giltigt ännu.
* Ange inte en mycket kort förfallotid på en SAS. Igen, liten klocka skillnaderna mellan värden som genererar SAS och storage-tjänst kan leda till en SAS uppenbarligen ut tidigare än förväntat.
* Har versionsparametern i SAS-nyckeln (till exempel **SA = 2015-04-05**) matchar versionen av Storage-klientbiblioteket som du använder? Vi rekommenderar att du alltid använder den senaste versionen av den [Storage-klientbibliotek](https://www.nuget.org/packages/WindowsAzure.Storage/).
* Om du återskapar dina åtkomstnycklar för lagring kan vara ogiltig eventuella befintliga SAS-token. Det här problemet kan uppstå om du skapar SAS-token med en lång förfallotiden för klientprogram som ska cachelagras.

Om du använder Storage-klientbiblioteket för att generera SAS-token, är det enkelt att skapa en giltig token. Men om du använder Storage REST-API och hur du skapar SAS-token manuellt, se [delegera åtkomst med en signatur för delad åtkomst](http://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-404-messages"></a>Klienten tar emot HTTP 404 (inget hittas) meddelanden
Om klientprogrammet får felmeddelandet HTTP 404 (inget hittas) från servern, innebär det att objektet som klienten försökte använda (till exempel en entitet, tabell, blob, behållare eller kön) inte finns i lagringstjänsten. Det finns ett antal möjliga orsaker till detta, exempelvis:

* [Klienten eller en annan process tidigare har tagit bort objektet]
* [Ett problem med auktorisering delade signatur åtkomst (SAS)]
* [Klientens JavaScript-kod har inte behörighet att komma åt objektet]
* [Nätverksfel]

#### <a name="client-previously-deleted-the-object"></a>Klienten eller en annan process tidigare har tagit bort objektet
I scenarier där klienten försöker läsa, uppdatera eller ta bort data i en storage-tjänst är det oftast lättare att identifiera i loggarna serversidan en tidigare åtgärd som tagit bort det aktuella objektet från storage-tjänst. Ofta visar loggdata att en annan användare eller process bort objektet. I loggen lagring loggning serversidan visar åtgärden typ och begärt Objektnyckel kolumner när en klient bort ett objekt.

I ett scenario där en klient försöker infoga ett objekt, kanske den inte visar sig omedelbart anledningen till detta resulterar i ett HTTP 404 (inget hittas) svar med tanke på att klienten är att skapa ett nytt objekt. Men måste om klienten skapar en blob som den ska kunna hitta blob-behållaren om klienten skapar ett meddelande som måste kunna hitta en kö, och om klienten är att lägga till en rad den kunna hitta tabellen.

Du kan använda klientsidan loggen från Storage-klientbiblioteket för att få en mer detaljerad förståelse för när klienten skickar specifika förfrågningar till lagringstjänsten.

Följande klientsidan loggen genereras av Storage-klientbiblioteket illustrerar problemet när klienten inte kan hitta behållaren för blob den skapar. Den här loggfilen innehåller information om följande lagringsåtgärder för:

| ID för förfrågan | Åtgärd |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** metod för att ta bort blob-behållaren. Observera att den här åtgärden innehåller en **HEAD** begäran att söka efter befintliga för behållaren. |
| e2d06d78… |**CreateIfNotExists** metoden för att skapa blob-behållaren. Observera att den här åtgärden innehåller en **HEAD** begäran som kontrollerar om behållaren finns. Den **HEAD** returnerar ett 404 meddelande men fortsätter. |
| de8b1c3c-... |**UploadFromStream** metoden för att skapa blob. Den **PLACERA** begäran misslyckas med ett 404-meddelande |

Loggposter:

| ID för förfrågan | Åtgärden Text |
| --- | --- |
| 07b26a5d-... |Starta synkron begäran till https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = HEAD...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 03 Jun 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Väntar på svar. |
| 07b26a5d-... |Svar mottogs. Statuskod = 200, ID för begäran = eeead849... Content-MD5 = ETag = &quot;0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |Svarshuvuden har bearbetats, fortsätter med resten av åtgärden. |
| 07b26a5d-... |Hämtar brödtext för svar. |
| 07b26a5d-... |Åtgärden har slutförts. |
| 07b26a5d-... |Starta synkron begäran till https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = DELETE...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 03 Jun 2014 12:10:33 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Väntar på svar. |
| 07b26a5d-... |Svar mottogs. Statuskod = 202, ID för begäran = 6ab2a4cf-..., Content-MD5 = ETag =. |
| 07b26a5d-... |Svarshuvuden har bearbetats, fortsätter med resten av åtgärden. |
| 07b26a5d-... |Hämtar brödtext för svar. |
| 07b26a5d-... |Åtgärden har slutförts. |
| e2d06d78-... |Starta asynkron begäran till https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td> |
| e2d06d78-... |StringToSign = HEAD...x-ms-client-request-id:e2d06d78-...x-ms-date:Tue, 03 Jun 2014 12:10:33 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Väntar på svar. |
| de8b1c3c-... |Starta synkron begäran till https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |StringToSign = PUT... 64.qCmF+TQLPhq/YYK50mP9ZQ==...x-MS-BLOB-Type:BlockBlob.x-MS-Client-Request-ID:de8b1c3c-...x-MS-Date:TUE 03 Jun 2014 12:10:33 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Förbereder att skriva data för begäran. |
| e2d06d78-... |Ett undantag uppstod under väntan på svar: fjärrservern returnerade ett fel: (404) gick inte att hitta... |
| e2d06d78-... |Svar mottogs. Statuskod = 404 begäran-ID = 353ae3bc-..., Content-MD5 = ETag =. |
| e2d06d78-... |Svarshuvuden har bearbetats, fortsätter med resten av åtgärden. |
| e2d06d78-... |Hämtar brödtext för svar. |
| e2d06d78-... |Åtgärden har slutförts. |
| e2d06d78-... |Starta asynkron begäran till https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| e2d06d78-... |StringToSign = PUT... 0...x-MS-Client-Request-ID:e2d06d78-...x-MS-Date:TUE 03 Jun 2014 12:10:33 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Väntar på svar. |
| de8b1c3c-... |Begäran när data skrevs. |
| de8b1c3c-... |Väntar på svar. |
| e2d06d78-... |Ett undantag uppstod under väntan på svar: fjärrservern returnerade ett fel: (409) konflikt... |
| e2d06d78-... |Svar mottogs. Statuskod = 409, ID för begäran = c27da20e-..., Content-MD5 = ETag =. |
| e2d06d78-... |Hämtar fel svarstexten. |
| de8b1c3c-... |Ett undantag uppstod under väntan på svar: fjärrservern returnerade ett fel: (404) gick inte att hitta... |
| de8b1c3c-... |Svar mottogs. Statuskod = 404 begäran-ID = 0eaeab3e-..., Content-MD5 = ETag =. |
| de8b1c3c-... |Ett undantag uppstod under åtgärden: fjärrservern returnerade ett fel: (404) gick inte att hitta... |
| de8b1c3c-... |Återförsöksprincipen tillät inte för ett nytt försök. Misslyckas med fjärrservern returnerade ett fel: (404) gick inte att hitta... |
| e2d06d78-... |Återförsöksprincipen tillät inte för ett nytt försök. Misslyckas med fjärrservern returnerade ett fel: (409) konflikt... |

I det här exemplet visar loggen att klienten interfoliering begäranden från den **CreateIfNotExists** metod (begäran-ID e2d06d78...) med begäranden från den **UploadFromStream** metod (de8b1c3c-...). Den här interfoliering beror på att klientprogram anropar asynkront dessa metoder. Ändra asynkron koden i klienten så att den skapar behållaren innan du försöker överföra data till en blobb i behållaren. Helst bör du skapa alla behållare i förväg.

#### <a name="SAS-authorization-issue"></a>Ett problem med auktorisering delade signatur åtkomst (SAS)
Om klientprogrammet försöker använda en SAS-nyckel som inte har tillräcklig behörighet för åtgärden, returnerar lagringstjänsten meddelandet HTTP 404 (inget hittas) till klienten. Samtidigt, du kan även se ett noll-värde för **SASAuthorizationError** i mätvärdena.

I följande tabell visas en serversidan loggmeddelande exempel från lagring loggning loggfilen:

| Namn | Värde |
| --- | --- |
| Starttid för begäran | 2014-05-30T06:17:48.4473697Z |
| Åtgärdstyp     | GetBlobProperties            |
| Status för begäran     | SASAuthorizationError        |
| HTTP-statuskod   | 404                          |
| Autentiseringstyp| SAS                          |
| Typ av tjänst       | Blob                         |
| Fråge-URL        | https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&sig=XXXXX&;api-version=2014-02-14 |
| Huvudet i begäran-ID  | a1f348d5-8032-4912-93EF-b393e5252a3b |
| ID för klientbegäran  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Undersök varför ditt klientprogram försöker utföra en åtgärd som den inte har beviljats behörighet.

#### <a name="JavaScript-code-does-not-have-permission"></a>Klientens JavaScript-kod har inte behörighet att komma åt objektet
Om du använder en JavaScript-klient och lagringstjänsten returnerar HTTP 404-meddelanden, Sök efter följande JavaScript-fel i webbläsaren:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Du kan använda F12 utvecklingsverktygen i Internet Explorer för att spåra de meddelanden som utbyts mellan webbläsaren och lagringstjänsten när du felsöker problem med klientens JavaScript.
> 
> 

Dessa fel uppstår eftersom webbläsaren implementerar den [samma ursprung princip](http://www.w3.org/Security/wiki/Same_Origin_Policy) säkerhetsbegränsningar som förhindrar att en webbsida anropar en API i en annan domän från domänen sidan kommer från.

Undvik problemet JavaScript, kan du konfigurera Cross Origin Resource Sharing (CORS) för lagringstjänsten klienten har åtkomst till. Mer information finns i [Cross-Origin Resource Sharing (CORS) stöd för Azure Storage-tjänster](http://msdn.microsoft.com/library/azure/dn535601.aspx).

Följande kodexempel visar hur du konfigurerar blob-tjänsten för att tillåta JavaScript körs i Contoso-domänen åtkomst till en blobb i ditt blob storage-tjänst:

```csharp
CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
// Set the service properties.
ServiceProperties sp = client.GetServiceProperties();
sp.DefaultServiceVersion = "2013-08-15";
CorsRule cr = new CorsRule();
cr.AllowedHeaders.Add("*");
cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
cr.AllowedOrigins.Add("http://www.contoso.com");
cr.ExposedHeaders.Add("x-ms-*");
cr.MaxAgeInSeconds = 5;
sp.Cors.CorsRules.Clear();
sp.Cors.CorsRules.Add(cr);
client.SetServiceProperties(sp);
```

#### <a name="network-failure"></a>Nätverksfel
I vissa fall kan förlorade nätverkspaket leda till lagringstjänsten returnera HTTP 404-meddelanden till klienten. Till exempel när klientprogrammet tas bort en entitet från tabelltjänsten Se utlösa en lagring undantag reporting-klienten ett ”HTTP 404 (inget hittas)” statusmeddelande från tabelltjänsten. När du undersöker tabellen i table storage-tjänsten Se du att tjänsten tog bort enheten enligt begäran.

Undantagsinformation i klienten inkluderar ID för begäran (7e84f12d...) som tilldelats av tabelltjänsten för begäran: du kan använda den här informationen för att hitta information för begäran i loggarna serversidan lagring genom att söka i den **huvud för begäran-id**  kolumn i loggfilen. Du kan också använda mätvärden för att identifiera när fel sådana här uppstå och sök sedan filerna baserat på tiden som mätvärdena registreras det här felet. Loggposten visar att borttagningen misslyckades med ett statusmeddelande för ”HTTP (404) klient fel”. Samma loggposten även det begärt-ID som genererades av klienten i den **client-request-id** kolumn (813ea74f...).

Serversidan loggen innehåller även en annan transaktion med samma **client-request-id** värde (813ea74f...) för en lyckad borttagningsåtgärden för samma entitet och från samma klient. Borttagningsåtgärden lyckas ta ägde rum precis innan det misslyckade ta bort begäran.

Den troligaste orsaken till det här scenariot är att klienten har skickat en begäran om borttagning för entiteten tjänsten tabell, som har slutförts men tog inte emot en bekräftelse från servern (kanske på grund av en tillfällig nätverksproblem). Klienten sedan automatiskt ett nytt försök för åtgärden (använder samma **client-request-id**), och den här nya försöket misslyckades eftersom enheten redan har tagits bort.

Om det här problemet uppstår ofta bör du undersöka varför klienten inte kan ta emot bekräftelser från tabelltjänsten. Om problemet är tillfälligt, bör du hantera felet ”Det gick inte att hitta HTTP (404)” och logga in på klienten, men tillåter klienten att fortsätta.

### <a name="the-client-is-receiving-409-messages"></a>Klienten tar emot HTTP 409 (konflikt) meddelanden
I följande tabell visas ett utdrag ur serversidan loggen för två Klientåtgärder: **DeleteIfExists** följt av omedelbart **CreateIfNotExists** med samma namn för blob-behållaren. Varje klientåtgärden resulterar i två förfrågningar som skickas till servern först en **GetContainerProperties** begäran om att kontrollera om behållaren finns, följt av den **DeleteContainer** eller  **CreateContainer** begäran.

| Tidsstämpel | Åtgärd | Resultat | Behållarens namn | ID för klientbegäran |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-... |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-... |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-... |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-... |

Koden i klientprogrammet tas bort och återskapar sedan omedelbart en blob-behållare med samma namn: den **CreateIfNotExists** metod (klienten begär ID bc881924-...) så småningom misslyckas med felmeddelandet HTTP 409 (konflikt). När en klient tar bort blob-behållare, tabeller eller köer som det finns en kort period före namnet blir tillgänglig igen.

Klientprogram ska använda unikt behållarnamn när du skapar den nya behållare om ta bort/återskapa mönstret är vanligt.

### <a name="metrics-show-low-percent-success"></a>Mätvärdena visar låg PercentSuccess eller analytics loggposter innehålla åtgärder med transaktionsstatus för ClientOtherErrors
Den **PercentSuccess** mått samlar in procentuella av åtgärder som har genomförts baserat på HTTP-statuskod. Åtgärder med statuskoder av 2XX räknas som slutförd medan åtgärder med statuskoder i 3XX, 4XX och 5XX intervall räknas som misslyckad och lägre den **PercentSucess** värde. I loggfilerna serversidan lagring åtgärderna registreras med transaktionsstatus **ClientOtherErrors**.

Det är viktigt att Observera att dessa åtgärder har slutförts och därför inte påverkar andra mått, till exempel tillgänglighet. Exempel på åtgärder som köras men som kan leda till misslyckade HTTP-statuskoder är:

* **ResourceNotFound** (inte att hitta 404), till exempel från en GET-begäran till en blob som inte finns.
* **ResouceAlreadyExists** (konflikt 409), till exempel från ett **CreateIfNotExist** åtgärden där resursen finns redan.
* **ConditionNotMet** (inte ändrade 304), till exempel från en villkorlig åtgärd, till exempel när en klient skickar en **ETag** värde och en HTTP- **If-None-Match** huvudet för att begära en bild endast om den har uppdaterats sedan den senaste åtgärden.

Du hittar en lista över vanliga felkoder för REST-API som lagringstjänsterna returnerar på sidan [vanliga felkoder för REST-API](http://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Kapacitetsdata visar en oväntad ökning i kapacitetsförbrukning för lagring
Om du ser plötslig oväntade ändringar i kapacitetsförbrukning i ditt lagringskonto, du kan undersöka skälen genom att studera din mått för tillgänglighet; till exempel en ökning av antalet misslyckade delete begäranden kan leda till en ökning av du använder som programspecifika rensningsåtgärder som du kan ha förväntas att frigöra utrymme inte kanske fungerar som förväntat (till exempel blob-lagring eftersom SAS-token som används för att frigöra utrymme har upphört att gälla).

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>Problemet uppstår från med hjälp av storage-emulatorn för utveckling eller testning
Du vanligtvis använda lagringsemulatorn under utveckling och test för att undvika kravet för ett Azure storage-konto. Vanliga problem som kan uppstå när du använder lagringsemulatorn är:

* [Funktionen ”X” fungerar inte i storage-emulatorn]
* [Fel ”värdet för en HTTP-huvuden är inte i rätt format” när du använder lagringsemulatorn]
* [Kör lagringsemulatorn kräver administratörsbehörighet]

#### <a name="feature-X-is-not-working"></a>Funktionen ”X” fungerar inte i storage-emulatorn
Storage-emulatorn stöder inte alla funktioner i Azure storage-tjänster som tjänsten. Mer information finns i [Använd Azure Storage-emulatorn för utveckling och testning](storage-use-emulator.md).

Använd Azure storage-tjänst i molnet för de funktioner som inte har stöd för storage-emulatorn.

#### <a name="error-HTTP-header-not-correct-format"></a>Fel ”värdet för en HTTP-huvuden är inte i rätt format” när du använder lagringsemulatorn
Du testar ditt program som använder Storage-klientbiblioteket mot lokal lagring emulatorn och metoden anrop som **CreateIfNotExists** misslyckas med felmeddelandet ”värdet för en HTTP-huvuden inte är i rätt format ”. Detta anger att versionen av storage-emulatorn som du använder inte stöder versionen av storage-klientbiblioteket som du använder. Storage-klientbiblioteket lägger till huvudet **x-ms-version** alla begäranden som gör det. Om storage-emulatorn inte känner igen värdet i den **x-ms-version** sidhuvud, avvisar begäran.

Du kan använda lagring Biblioteksklient loggarna för att visa värdet för den **x-ms-versionshuvud** som skickas. Du kan också se värdet för den **x-ms-versionshuvud** om du använder Fiddler för att spåra begäranden från klientprogrammet.

Detta inträffar vanligtvis om du installerar och använder den senaste versionen av Storage-klientbiblioteket utan att uppdatera storage-emulatorn. Du bör installera den senaste versionen av lagringsemulatorn eller använda molnlagring i stället för emulatorn för utveckling och testning.

#### <a name="storage-emulator-requires-administrative-privileges"></a>Kör lagringsemulatorn kräver administratörsbehörighet
Du tillfrågas om autentiseringsuppgifter när du kör storage-emulatorn. Det här inträffar bara när du initierar storage-emulatorn för första gången. När du har initierat storage-emulatorn, behöver inte administratörsbehörighet för att köra det igen.

Mer information finns i [Använd Azure Storage-emulatorn för utveckling och testning](storage-use-emulator.md). Du kan också initiera storage-emulatorn i Visual Studio, vilket även kräver administratörsbehörighet.

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Det uppstår problem med att installera Azure SDK för .NET
När du försöker installera SDK misslyckas med att installera storage-emulatorn på din lokala dator. Installationsloggen innehåller något av följande meddelanden:

* CAQuietExec: Fel: Det gick inte att komma åt SQL-instans
* CAQuietExec: Fel: Det gick inte att skapa databas

Orsaken är ett problem med en befintlig LocalDB-installation. Som standard använder lagringsemulatorn LocalDB för att bevara data när den simulerar Azure storage-tjänster. Du kan återställa din LocalDB-instans genom att köra följande kommandon i ett Kommandotolk fönster innan du försöker installera SDK.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

Den **ta bort** kommandot tar bort eventuella gamla databasfiler från en tidigare installation av storage-emulatorn.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Du har ett annat problem med en storage-tjänst
Om föregående felsökningsavsnitt innehåller inte inkluderar problem som du har med en storage-tjänst, bör du vidta följande metod för att diagnostisera och felsöka problemet.

* Kontrollera din mått för att se om det finns ändringar från din förväntade baslinjen beteende. Från statistik du kan avgöra om problemet är tillfälligt eller permanent och vilka lagringsåtgärder problemet påverkar.
* Du kan använda mått information som hjälper dig att söka efter loggdata serversidan för mer detaljerad information om fel som uppstår. Den här informationen kan hjälpa dig att felsöka och lösa problemet.
* Du kan använda Storage-klientbibliotek klientsidan loggar för att undersöka beteendet för ditt klientprogram och verktyg som Fiddler, Wireshark och Microsoft Message Analyzer för att undersöka nätverket om informationen i loggarna servern inte är tillräckligt för att felsöka problemet har.

Mer information om hur du använder Fiddler finns ”[bilaga 1: med Fiddler att samla in HTTP och HTTPS-trafik]”.

Mer information om hur du använder Wireshark finns ”[bilaga 2: använder Wireshark för att avbilda nätverkstrafik]”.

Mer information om hur du använder Microsoft Message Analyzer finns ”[tillägg 3: använda Microsoft Message Analyzer för att avbilda nätverkstrafik]”.

## <a name="appendices"></a>Tilläggen
Tilläggen beskrivs flera verktyg som kan vara praktiskt när du diagnostiserar och felsökning av problem med Azure Storage (och andra tjänster). Dessa verktyg är inte en del av Azure Storage och vissa är produkter från tredje part. De verktyg som beskrivs i de här tilläggen omfattas inte av några supportavtal som du kan ha med Microsoft Azure eller Azure Storage som sådana och därför som en del av din utvärdering av du bör undersöka licensiering och support alternativen från providers av dessa verktyg.

### <a name="appendix-1"></a>Bilaga 1: Med Fiddler att samla in HTTP och HTTPS-trafik
[Fiddler](http://www.telerik.com/fiddler) är användbart för att analysera HTTP och HTTPS-trafik mellan ditt klientprogram och Azure storage-tjänst som du använder.

> [!NOTE]
> Fiddler kan avkoda HTTPS-trafik. Du bör läsa dokumentationen Fiddler noggrant för att förstå hur detta sker och att förstå säkerhetsaspekterna.
> 
> 

Den här bilagan innehåller en kort genomgång av hur du konfigurerar Fiddler för att fånga in trafik mellan den lokala datorn där du har installerat Fiddler och Azure storage-tjänster.

När du har öppnat Fiddler börjar samla in HTTP och HTTPS-trafik på den lokala datorn. Följande är några användbara kommandon för att styra Fiddler:

* Stoppa och starta fånga in trafik. På huvudmenyn, gå till **filen** och klicka sedan på **fånga in trafik** att växla avbildning på och stänga av.
* Spara insamlade trafikinformation. På huvudmenyn, gå till **filen**, klickar du på **spara**, och klicka sedan på **alla sessioner**: Detta gör att du kan spara trafiken i en Session arkivfilen. Du kan läsa in ett Session-Arkiv senare för analys, eller skicka den begäran till Microsoft support.

Om du vill begränsa mängden trafik som samlar in Fiddler, du kan använda filter som du konfigurerar i den **filter** fliken. Följande skärmbild visar ett filter som samlar in endast trafik som skickas till den **contosoemaildist.table.core.windows.net** lagring slutpunkt:

![][5]

### <a name="appendix-2"></a>Bilaga 2: Använder Wireshark för att avbilda nätverkstrafik
[Wireshark](http://www.wireshark.org/) är en nätverksprotokollanalysator som gör att du kan visa detaljerad paketinformationen för en mängd olika nätverksprotokoll.

Följande procedur visar hur du kan få detaljerad paketinformationen för trafik från den lokala datorn där du installerade Wireshark till tabelltjänsten i ditt Azure storage-konto.

1. Starta Wireshark på din lokala dator.
2. I den **starta** väljer du det lokala nätverket eller flera gränssnitt som är anslutna till internet.
3. Klicka på **avbilda alternativ**.
4. Lägg till ett filter för att den **Capture Filter** textruta. Till exempel **värd contosoemaildist.table.core.windows.net** konfigurerar Wireshark för att avbilda endast de paket som skickas till eller från tabelltjänst i den **contosoemaildist** storage-konto. Kolla in den [fullständig lista över avbilda filter](http://wiki.wireshark.org/CaptureFilters).
   
   ![][6]
5. Klicka på **starta**. Wireshark nu avbildar alla paketen skickas till eller från tabellen tjänstslutpunkten som du använder klientprogrammet på den lokala datorn.
6. När du är klar, klicka på huvudmenyn **avbilda** och sedan **stoppa**.
7. Om du vill spara den fångade data i en Wireshark avbilda fil på huvudmenyn klickar du på **filen** och sedan **spara**.

WireShark markerar du eventuella fel som finns i den **packetlist** fönster. Du kan också använda den **Expert Info** fönstret (klicka på **analysera**, sedan **Expert Info**) att visa en sammanfattning av fel och varningar.

![][7]

Du kan också välja att visa TCP-data som programlager ser den genom att högerklicka på TCP-data och välja **följer TCP-ström**. Detta är användbart om du har hämtat dina dump utan ett filter för avbildning. Mer information finns i [följande TCP-strömmar](http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [!NOTE]
> Mer information om hur du använder Wireshark finns i [Wireshark användarhandboken](http://www.wireshark.org/docs/wsug_html_chunked).
> 
> 

### <a name="appendix-3"></a>Tillägg 3: Använda Microsoft Message Analyzer för att avbilda nätverkstrafik
Du kan använda Microsoft Message Analyzer att samla in HTTP och HTTPS-trafik på liknande sätt som i Fiddler och avbilda nätverkstrafik på ett liknande sätt att Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Konfigurera en session för spårning av webbprogram med hjälp av Microsoft Message Analyzer
Så här konfigurerar du en spårning webbsessionen för HTTP och HTTPS-trafik med hjälp av Microsoft Message Analyzer kör programmet Microsoft Message Analyzer och sedan på den **filen** -menyn klickar du på **avbilda/Trace**. Välj i listan över tillgängliga trace scenarier, **webbproxy**. I den **scenariot spårningskonfiguration** panelen i den **HostnameFilter** textruta lägga till namnen på dina slutpunkter för lagring (kan du söka efter dessa namn i den [Azure-portalen](https://portal.azure.com)). Om namnet på ditt Azure storage-konto är till exempel **contosodata**, bör du lägga till följande för att den **HostnameFilter** textruta:

```
contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
```

> [!NOTE]
> Blanksteg avgränsar värdnamnen.
> 
> 

När du är redo att börja samla in spårningsdata klickar du på den **starta med** knappen.

Mer information om Microsoft Message Analyzer **webbproxy** spåra, se [Provider för Microsoft-PEF-WebProxy](http://technet.microsoft.com/library/jj674814.aspx).

Inbyggt **webbproxy** spårning i Microsoft Message Analyzer baseras på Fiddler; den kan samla in klientsidans HTTPS-trafik och visa okrypterade HTTPS-meddelanden. Den **webbproxy** spåra fungerar genom att konfigurera en lokal proxyserver för alla HTTP och HTTPS-trafik som ger tillgång till okrypterade meddelanden.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnostisera problem med Microsoft Message Analyzer
Förutom att använda Microsoft Message Analyzer **webbproxy** spårning för att samla in information om HTTP/HTTPs-trafik mellan klientprogrammet och storage-tjänst, du kan också använda inbyggt **lokala länknivå** spårning för att samla in nätverksinformation för paketet. Det här kan du samla in data som liknar den som du kan avbilda med Wireshark och diagnostisera nätverk problem som tappade paket.

Följande skärmbild visar ett exempel **lokala länknivå** spårningen med några **informationsmeddelande** meddelanden i den **DiagnosisTypes** kolumn. Klicka på en ikon i den **DiagnosisTypes** kolumnen visar informationen för meddelandet. I det här exemplet skickats meddelandet #305 om servern eftersom den inte fick en bekräftelse från klienten:

![][9]

När du skapar spårningssessionen i Microsoft Message Analyzer kan du ange filter för att minska mängden brus i spårningen. På den **avbilda / spåra** sida där du definierar spårningen, klicka på den **konfigurera** länka bredvid **Microsoft-Windows-NDIS-PacketCapture**. Följande skärmbild visar en konfiguration som filtrerar TCP-trafik för IP-adresserna för tre storage-tjänster:

![][10]

Läs mer om Microsoft Message Analyzer lokala länknivå spårningen [Provider för Microsoft-PEF-NDIS-PacketCapture](http://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4"></a>Tillägg 4: Använda Excel för att visa mått och logga data
Många verktyg kan du ladda ned Storage-mätvärden data från Azure-tabellagring i formatet avgränsad som gör det enkelt att läsa in data till Excel för visning och analys. Lagring loggningsdata från Azure blob storage finns redan i en avgränsad format som du kan läsa in i Excel. Men du behöver lägga till lämpliga kolumnrubrikerna i informationen på [Storage Analytics loggformatet](http://msdn.microsoft.com/library/azure/hh343259.aspx) och [Storage Analytics mätvärden tabellschemat](http://msdn.microsoft.com/library/azure/hh343264.aspx).

Importera dina data för loggning av lagring till Excel när du har hämtat från blob storage:

* På den **Data** -menyn klickar du på **från Text**.
* Bläddra till den loggfil som du vill visa och klicka på **importera**.
* I steg 1 i den **guiden**väljer **avgränsad**.

I steg 1 i den **guiden**väljer **semikolon** som endast avgränsare och välj dubbla citattecken som den **textavgränsare**. Klicka på **Slutför** och välj var du ska placera data i arbetsboken.

### <a name="appendix-5"></a>Tillägg 5: Övervaka med Programinsikter för Visual Studio Team Services
Du kan också använda funktionen Programinsikter för Visual Studio Team Services som en del av prestanda och tillgänglighetsövervakning. Det här verktyget kan:

* Kontrollera att webbtjänsten är tillgänglig och svarstid. Om din app är en webbplats eller en enhetsapp som använder en webbtjänst kan det Testa URL: en med några minuters mellanrum från platser över hela världen och att du vet om det finns ett problem.
* Snabbt diagnostisera eventuella problem med prestanda eller undantag i webbtjänsten. Ta reda på om CPU eller andra resurser är att sträckas ut, hämta stackspår från undantag och enkelt söka igenom loggspårningar. Om appens prestanda sjunker under acceptabla gränser, kan Microsoft skicka ett e-postmeddelande. Du kan övervaka både .NET och Java-webbtjänster.

Du hittar mer information i [vad är Application Insights](../../application-insights/app-insights-overview.md).

<!--Anchors-->
[Introduktion]: #introduction
[Hur den här guiden är organiserat]: #how-this-guide-is-organized

[övervakning lagringstjänsten]: #monitoring-your-storage-service
[Övervakning av tjänstens hälsa]: #monitoring-service-health
[Övervakning av kapacitet]: #monitoring-capacity
[Övervaka tillgänglighet]: #monitoring-availability
[Övervaka prestanda]: #monitoring-performance

[diagnostisera problem med lagring]: #diagnosing-storage-issues
[Hälsotillståndsproblem med tjänsten för]: #service-health-issues
[prestandaproblem]: #performance-issues
[Diagnostisera fel]: #diagnosing-errors
[Storage-emulatorn problem]: #storage-emulator-issues
[Loggningsverktyg för lagring]: #storage-logging-tools
[Med hjälp av verktyg för loggning]: #using-network-logging-tools

[slutpunkt till slutpunkt spårning]: #end-to-end-tracing
[Korrelerar loggdata]: #correlating-log-data
[ID för klientbegäran]: #client-request-id
[Server begäran-ID]: #server-request-id
[Tidsstämplar]: #timestamps

[felsökningsanvisningar]: #troubleshooting-guidance
[mätvärdena visar AverageE2ELatency hög och låg AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Mätningar visar låga värden för AverageE2ELatency och AverageServerLatency, men klienten har ändå långa svarstider]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Mätningar visar ett högt värde för AverageServerLatency]: #metrics-show-high-AverageServerLatency
[Du upplever oväntade fördröjningar i en köad meddelandeleverans]: #you-are-experiencing-unexpected-delays-in-message-delivery

[mätvärdena visar en ökning i PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Tillfällig ökning PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Permanent ökning PercentThrottlingError fel]: #permanent-increase-in-PercentThrottlingError
[mätvärdena visar en ökning i PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Mätningar visar en ökning i PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[Klienten tar emot HTTP 403 (förbjuden) meddelanden]: #the-client-is-receiving-403-messages
[Klienten tar emot HTTP 404 (inget hittas) meddelanden]: #the-client-is-receiving-404-messages
[Klienten eller en annan process tidigare har tagit bort objektet]: #client-previously-deleted-the-object
[Ett problem med auktorisering delade signatur åtkomst (SAS)]: #SAS-authorization-issue
[Klientens JavaScript-kod har inte behörighet att komma åt objektet]: #JavaScript-code-does-not-have-permission
[Nätverksfel]: #network-failure
[Klienten tar emot HTTP 409 (konflikt) meddelanden]: #the-client-is-receiving-409-messages

[mätvärdena visar låg PercentSuccess eller analytics loggposter innehålla åtgärder med transaktionsstatus av ClientOtherErrors]: #metrics-show-low-percent-success
[Kapacitetsdata visar en oväntad ökning i kapacitetsförbrukning för lagring]: #capacity-metrics-show-an-unexpected-increase
[Problemet uppstår från med hjälp av storage-emulatorn för utveckling eller testning]: #your-issue-arises-from-using-the-storage-emulator
[Funktionen ”X” fungerar inte i storage-emulatorn]: #feature-X-is-not-working
[Fel ”värdet för en HTTP-huvuden är inte i rätt format” när du använder lagringsemulatorn]: #error-HTTP-header-not-correct-format
[Kör lagringsemulatorn kräver administratörsbehörighet]: #storage-emulator-requires-administrative-privileges
[Det uppstår problem med att installera Azure SDK för .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Du har ett annat problem med en storage-tjänst]: #you-have-a-different-issue-with-a-storage-service

[tilläggen]: #appendices
[bilaga 1: med Fiddler att samla in HTTP och HTTPS-trafik]: #appendix-1
[bilaga 2: använder Wireshark för att avbilda nätverkstrafik]: #appendix-2
[tillägg 3: använda Microsoft Message Analyzer för att avbilda nätverkstrafik]: #appendix-3
[Tillägg 4: Använda Excel för att visa mått och logga data]: #appendix-4
[Tillägg 5: Övervaka med Programinsikter för Visual Studio Team Services]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png
