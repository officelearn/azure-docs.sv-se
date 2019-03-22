---
title: Övervaka, diagnostisera och felsöka Azure Storage | Microsoft Docs
description: Använda funktioner som lagringsanalys, loggning på klientsidan och andra tredjepartsverktyg för att identifiera, diagnostisera och felsöka Azure Storage-relaterade problem.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 05/11/2017
ms.author: fhryo-msft
ms.subservice: common
ms.openlocfilehash: 51d8b9bad4bb4a3663b07a711f363b263f5d64db
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339694"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Övervaka, diagnostisera och felsök Microsoft Azure Storage
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Översikt
Diagnostisera och felsöka problem i ett distribuerat program i en molnmiljö kan vara mer komplex än i traditionella miljöer. Program kan distribueras i en PaaS eller IaaS-infrastruktur lokalt på en mobil enhet eller i en kombination av dessa miljöer. Normalt ditt programs trafik kan passera offentliga och privata nätverk och ditt program kan använda flera lagringstekniker, till exempel Microsoft Azure Storage-tabeller, Blobbar, köer eller filer förutom andra data lagras, till exempel som relationella och dokumentera databaser.

Om du vill hantera sådana program har bör du övervaka dem proaktivt och lär dig att diagnostisera och Felsök alla aspekter av dem och deras beroende tekniker. Som en användare av Azure Storage-tjänster du kontinuerligt övervaka Storage-tjänster som programmet använder för eventuella oväntade ändringar i beteende (till exempel långsammare än vanligt svarstider) och Använd loggning för att samla in mer detaljerade data och analysera ett problem i djup. Diagnostikinformation som du får från både övervakning och loggning hjälper dig att fastställa orsaken till problemet uppstod i ditt program. Sedan kan du felsöka problemet och fastställa lämpliga åtgärder du kan vidta för att lösa det aktuella problemet. Azure Storage är en grundläggande Azure-tjänsten och utgör en viktig del av flesta lösningar som kunder distribuerar till Azure-infrastrukturen. Azure Storage innehåller funktioner för att förenkla övervakning, diagnostisera och felsöka problem med lagring i dina molnbaserade program.

> [!NOTE]
> Azure Files stöder inte loggning just nu.
>

En praktisk guide till slutpunkt till slutpunkt felsökning i Azure Storage-program finns [slutpunkt till slutpunkt felsökning med hjälp av Azure Storage-mätvärden och loggning, AzCopy och Message Analyzer](../storage-e2e-troubleshooting.md).

* [Introduktion]
  * [Hur den här guiden är strukturerad]
* [Övervakning av storage-tjänst]
  * [Övervakar tjänsternas hälsa]
  * [Övervakningskapacitet]
  * [Övervakar tillgänglighet]
  * [Övervaka prestanda]
* [Diagnostisera problem med lagring]
  * [Tjänsten hälsoproblem]
  * [Prestandaproblem]
  * [Diagnostisera fel]
  * [Storage-emulatorn problem]
  * [Loggningsverktyg för lagring]
  * [Med hjälp av verktyg för loggning]
* [Slutpunkt till slutpunkt-spårning]
  * [Korrelera loggdata]
  * [ID för klientbegäran]
  * [Server-begäran-ID]
  * [Timestamps]
* [Felsökningsanvisningar]
  * [Mätningar visar ett högt värde för AverageE2ELatency och ett lågt värde för AverageServerLatency]
  * [Mätningar visar låga värden för AverageE2ELatency och AverageServerLatency, men klienten har ändå långa svarstider]
  * [Mätningar visar ett högt värde för AverageServerLatency]
  * [Du upplever oväntade fördröjningar i en köad meddelandeleverans]
  * [Mätningar visar en ökning i PercentThrottlingError]
  * [Mätningar visar en ökning i PercentTimeoutError]
  * [Mätningar visar en ökning i PercentNetworkError]
  * [Klienten får HTTP 403-meddelanden (Förbjudet)]
  * [Klienten får HTTP 404-meddelanden (Hittades inte)]
  * [Klienten tar emot HTTP 409 (konflikt) meddelanden]
  * [Mätvärdena visar låg PercentSuccess eller analytics loggposter innehålla åtgärder med transaktionsstatus för ClientOtherErrors]
  * [Kapacitetsdata visar en oväntad ökning i kapacitetsförbrukning för lagring]
  * [Problemet uppstår från med hjälp av storage-emulatorn för utveckling eller testning]
  * [Det uppstår problem med att installera Azure SDK för .NET]
  * [Du har ett annat problem med en storage-tjänst]
  * [Felsökning av virtuella hårddiskar på Windows-datorer](../../virtual-machines/windows/troubleshoot-vhds.md)   
  * [Felsökning av virtuella hårddiskar på virtuella Linux-datorer](../../virtual-machines/linux/troubleshoot-vhds.md)
  * [Felsökning av problem med Azure Files med Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Felsökning av problem med Azure Files med Linux](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Tilläggen]
  * [Bilaga 1: Med hjälp av Fiddler för att avbilda trafik över HTTP och HTTPS]
  * [Bilaga 2: Med hjälp av Wireshark för att avbilda nätverkstrafik]
  * [Tillägg 3: Använder Microsoft Message Analyzer för att avbilda nätverkstrafik]
  * [Tillägg 4: Använda Excel för att visa mätvärden och loggdata]
  * [Tillägg 5: Övervakning med Application Insights för Azure DevOps]

## <a name="introduction"></a>Introduktion
Den här guiden visar hur du använder funktioner, till exempel Azure Storage Analytics Klientloggning i Azure Storage-klientbiblioteket och andra verktyg från tredje part för att identifiera, diagnostisera och felsöka Azure Storage-relaterade problem.

![][1]

Den här guiden är avsedd att läsas främst av utvecklare av onlinetjänster som använder Azure Storage-tjänster och IT-proffs som ansvarar för att hantera sådana onlinetjänster. Målen för den här guiden är:

* För att du underhåller hälsotillstånd och prestanda för dina Azure Storage-konton.
* Att tillhandahålla nödvändiga processer och verktyg för att avgöra om en fråga eller ett problem i ett program som relaterar till Azure Storage.
* Att ge dig praktisk vägledning i hur du löser problem som rör Azure Storage.

### <a name="how-this-guide-is-organized"></a>Hur den här guiden är strukturerad
I avsnittet ”[Övervakning av storage-tjänst]” beskrivs hur du övervakar hälsa och prestanda för dina Azure Storage med hjälp av Azure Storage Analytics mätvärden (Lagringsmått).

I avsnittet ”[diagnostisera problem med lagring]” beskriver hur du diagnostiserar problem med hjälp av Azure Storage Analytics Logging (loggning av lagring). Det beskriver också hur du aktiverar loggning för klientsidan med verksamhet i någon av klientbiblioteken, till exempel Storage-klientbiblioteket för .NET och Azure SDK för Java.

I avsnittet ”[slutpunkt till slutpunkt-spårning]” beskriver hur du kan jämföra informationen i olika loggfiler och mätdata.

I avsnittet ”[felsökningsanvisningar]” ger felsökningsanvisningar för några vanliga storage-relaterade problem som du kan stöta på.

Den ”[tilläggen]” innehåller information om hur du använder andra verktyg, till exempel Wireshark och Netmon för analysera nätverk paketdata, Fiddler för att analysera HTTP/HTTPS-meddelanden och analysverktyg för att korrelera loggdata.

## <a name="monitoring-your-storage-service"></a>Övervakning av storage-tjänst
Om du är bekant med Windows prestandaövervakning av kan du se Lagringsmått som motsvarande räknare för prestandaövervakning för Windows Azure Storage. Du hittar en omfattande uppsättning mått (räknare i Windows Prestandaövervakaren terminologi), till exempel tjänstetillgänglighet, totalt antal begäranden till tjänsten eller procentandel av lyckade begäranden till tjänsten i Storage-mått. En fullständig lista över tillgängliga mått finns [Schema över Måttabeller i Storage Analytics](https://msdn.microsoft.com/library/azure/hh343264.aspx). Du kan ange om du vill samla in och sammanställa mätvärden varje timme eller varje minut lagringstjänst. Mer information om hur du aktiverar mått och övervaka dina lagringskonton finns i [aktiverar du lagringsmått och visar mätvärden](https://go.microsoft.com/fwlink/?LinkId=510865).

Du kan välja vilka per timme mått som du vill visa i den [Azure-portalen](https://portal.azure.com) och konfigurera regler som meddela administratörer via e-post när ett timvis mått överskrider ett visst tröskelvärde. Mer information finns i [ta emot varningsmeddelanden](/azure/monitoring-and-diagnostics/monitoring-overview-alerts).

Storage-tjänsten samlar in mått med en bästa prestanda, men kan inte registrera varje lagringsåtgärd.

Du kan visa mått, till exempel tillgänglighet, totalt antal begäranden och genomsnittlig svarstid-nummer för ett lagringskonto i Azure-portalen. En meddelanderegel har också ställts in för att varna en administratör om tillgänglighet sjunker under en viss nivå. Från att visa dessa data är ett möjligt området för undersökning slutförandeprocent för table service är lägre än 100% (Mer information finns i avsnittet ”[Mätvärdena visar låg PercentSuccess eller analytics loggposter innehålla åtgärder med transaktionsstatus för ClientOtherErrors]”).

Kontinuerligt bör du övervaka dina Azure-program är felfri och fungerar som förväntat genom att:

* Etablerar några jämförelsemått för program som gör att du kan jämföra aktuella data och identifiera eventuella betydande ändringar i beteendet för Azure storage och ditt program. Värdena för din jämförelsemått, i många fall blir beror på programmet och du bör fastställa dem när du är prestanda programtest.
* Spela in minutmått och använda dem för att övervaka aktivt efter oväntade fel och avvikelser, till exempel toppar i fel räknar eller begär betalning.
* Spela in per timme mått och använda dem för att övervaka genomsnittsvärden som genomsnittlig fel antal och begär betalning.
* Undersöka potentiella problem med hjälp av verktyg för diagnostik som beskrivs senare i avsnittet ”[diagnostisera problem med lagring]”.

Diagrammen i följande bild illustrerar hur medelvärdet som inträffar för varje timme mått kan dölja toppar i aktiviteten. Per timme mått visas en stadig andel begäranden, samtidigt som minut mått avslöja variationer som verkligen äger rum.

![][3]

Resten av det här avsnittet beskriver vilka mått som bör du övervaka och varför.

### <a name="monitoring-service-health"></a>Övervakar tjänsternas hälsa
Du kan använda den [Azure-portalen](https://portal.azure.com) att visa hälsotillståndet för Storage-tjänsten (och andra Azure-tjänster) i alla Azure-regioner runtom i världen. Övervakning kan påverkar du direkt se om ett problem utanför din kontroll lagringstjänst i den region som du använder för ditt program.

Den [Azure-portalen](https://portal.azure.com) kan också ge meddelanden om incidenter som påverkar olika Azure-tjänster.
Obs! Den här informationen fanns tidigare, tillsammans med historiska data på den [Azure instrumentpanel](https://status.azure.com).

Medan den [Azure-portalen](https://portal.azure.com) samlar in hälsoinformation från inuti Azure datacenter (inom ut övervakning), du kan också fundera på att börja använda en utifrån metod för att generera syntetiska transaktioner som regelbundet använder ditt Azure-värdbaserade webbprogram från flera platser. Tjänster som erbjuds av [Dynatrace](https://www.dynatrace.com/en/synthetic-monitoring) och Application Insights för Azure DevOps är exempel på den här metoden. Mer information om Application Insights för Azure DevOps finns i bilagan ”[tillägg 5: Övervakning med Application Insights för Azure DevOps](#appendix-5)”.

### <a name="monitoring-capacity"></a>Övervakningskapacitet
Mätvärden i Storage lagrar bara kapacitet för blob-tjänsten eftersom blobar vanligtvis hänsyn till största andelen av lagrade data (vid tidpunkten för skrivning, det går inte att använda Lagringsmått för att övervaka kapaciteten för dina tabeller och köer). Du hittar dessa data i den **$MetricsCapacityBlob** tabellen om du har aktiverat övervakning för Blob-tjänsten. Lagringsmått registrerar dessa data en gång per dag och du kan använda värdet för den **RowKey** att fastställa om raden innehåller en entitet som är kopplad till användarens data (värdet **data**) eller analytics-data (värde **analytics**). Innehåller information om mängden lagringsutrymme som används för varje lagrad entitet (**kapacitet** mätt i byte) och det aktuella antalet behållare (**ContainerCount**) och blobar (**ObjectCount** ) används i lagringskontot. Mer information om kapacitetsmåtten lagras i den **$MetricsCapacityBlob** tabellen, se [Schema över Måttabeller i Storage Analytics](https://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> Du bör övervaka dessa värden för en tidig varning att du närmar kapacitetsbegränsningen för lagringskontot. Du kan lägga till Varningsregler så att du meddelas om användning av sammanställda minne överskrider eller understiger tröskelvärden som du anger i Azure-portalen.
>
>

Om du behöver hjälp storleken på olika lagringsobjekt som blobbar, finns i bloggposten [förstå Azure Storage-fakturering – bandbredd, transaktioner och kapacitet](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

### <a name="monitoring-availability"></a>Övervakar tillgänglighet
Bör du övervaka tillgängligheten för storage-tjänster i ditt storage-konto genom att övervaka värdet i den **tillgänglighet** kolumn i tabellerna per timme eller minut mått – **$MetricsHourPrimaryTransactionsBlob** , **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob** , **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. Den **tillgänglighet** kolumnen innehåller ett procentvärde som anger tillgången till tjänsten eller API-åtgärden som representeras av raden (den **RowKey** visar om raden innehåller mått för den tjänsten som helhet eller för en viss API-åtgärd).

Alla värdet som är mindre än 100% anger att vissa lagringsbegäranden misslyckas. Du kan se varför de fungerar genom att undersöka de andra kolumnerna i mätvärden som visar hur många begäranden med olika feltyper som **ServerTimeoutError**. Du bör förvänta dig att se **tillgänglighet** fall tillfälligt under 100% skäl, till exempel tillfälligt server-timeout när tjänsten flyttar partitioner till bättre belastningsutjämna begäran; bör omprövningslogiken i klientprogrammet Hantera tillfälliga förhållanden. Artikeln [Storage Analytics loggade åtgärder och statusmeddelanden](https://msdn.microsoft.com/library/azure/hh343260.aspx) visar en lista över de transaktionstyper Lagringsmått innehåller i sin **tillgänglighet** beräkning.

I den [Azure-portalen](https://portal.azure.com), du kan lägga till aviseringsregler för att meddela dig om **tillgänglighet** för en tjänst sjunker under ett tröskelvärde som du anger.

Den ”[felsökningsanvisningar]” i den här guiden beskriver några vanliga storage service problem som rör tillgängligheten.

### <a name="monitoring-performance"></a>Övervaka prestanda
Du kan använda följande mått från tabellerna mått per timme och minut för att övervaka prestanda för storage-tjänster.

* Värdena i den **AverageE2ELatency** och **AverageServerLatency** kolumner visar den genomsnittliga tid på att lagringstjänsten eller API åtgärdstypen tar att bearbeta begäranden. **AverageE2ELatency** är ett mått på svarstiden för slutpunkt till slutpunkt som innehåller den tid det tar att läsa begäran och skicka svar förutom den tid det tar att bearbeta begäran (därför innehåller Nätverksfördröjningen när begäran når lagring tjänst). **AverageServerLatency** är ett mått på bara bearbetningstiden och därför utesluter alla Nätverksfördröjningen som rör kommunicerar med klienten. Se avsnittet ”[Mätningar visar ett högt värde för AverageE2ELatency och ett lågt värde för AverageServerLatency]” senare i den här guiden en beskrivning av varför det kan finnas en viktig skillnad mellan dessa två värden.
* Värdena i den **TotalIngress** och **TotalEgress** kolumner visar den totala mängden data, i byte, kommer till och går ut från lagringstjänsten eller via en viss typ för API-åtgärden.
* Värdena i den **TotalRequests** kolumnen visar det totala antalet begäranden som tar emot lagringstjänsten av API-åtgärden. **TotalRequests** är det totala antalet begäranden som tar emot lagringstjänsten.

Normalt ska du övervaka för oväntade ändringar i någon av dessa värden som indikator på att du har ett problem som kräver en undersökning.

I den [Azure-portalen](https://portal.azure.com), du kan lägga till aviseringsregler för att meddela dig om något av prestandamåtten för den här tjänsten understiger eller överskrider ett tröskelvärde som du anger.

Den ”[felsökningsanvisningar]” i den här guiden beskriver några vanliga storage service problem som rör prestanda.

## <a name="diagnosing-storage-issues"></a>Diagnostisera problem med lagring
Det finns ett antal sätt att du kan bli medvetna om problem och frågor i ditt program, inklusive:

* Ett allvarligt fel inträffar som gör att programmet kraschar eller slutar att fungera.
* Betydande förändringar från baslinjevärdena i de mått som du övervakar enligt beskrivningen i föregående avsnitt ”[Övervakning av storage-tjänst]”.
* Rapporter från användare av programmet som en viss åtgärd inte slutförde som förväntat eller att vissa funktioner inte fungerar.
* Fel som har genererats i ditt program som visas i loggfiler eller via någon annan metod för meddelandet.

Normalt problem relaterade till Azure storage-tjänster delas in i fyra olika kategorier:

* Ditt program har prestandaproblem, antingen rapporteras av användarna eller visas med ändringar i prestandavärden.
* Det finns ett problem med Azure Storage-infrastrukturen i en eller flera regioner.
* Programmet har uppstått ett fel rapporteras av användarna eller visas med en ökning av något av de fel antal mått som du övervakar.
* Under utveckling och testning kan du använda den lokala lagringsemulatorn; Du kan stöta på några problem som rör särskilt användningen av storage-emulatorn.

I följande avsnitt beskrivs de steg som du bör följa att diagnostisera och felsöka problem i var och en av dessa fyra kategorier. I avsnittet ”[felsökningsanvisningar]” senare i den här guiden ger fler detaljer för några vanliga problem som kan uppstå.

### <a name="service-health-issues"></a>Tjänsten hälsoproblem
Tjänsten hälsorelaterade problem är vanligtvis utanför din kontroll. Den [Azure-portalen](https://portal.azure.com) innehåller information om pågående problem med Azure-tjänster som storage-tjänster. Om du har valt Read-Access Geo-Redundant lagring när du skapade ditt lagringskonto, kan sedan om dina data inte är tillgänglig på den primära platsen, ditt program tillfälligt växla till skrivskyddad kopia på den sekundära platsen. Om du vill läsa från sekundärt måste programmet kunna växla mellan primära och sekundära lagringsplatser och fungerar i begränsat läge med skrivskyddade data. Azure Storage-klientbibliotek kan du definiera en återförsöksprincip som kan läsa från sekundär lagring om det inte går att utföra en läsning från primär lagring. Programmet måste också vara medveten om att data på den sekundära platsen är konsekvent. Mer information finns i bloggposten [redundansalternativ för Azure Storage och Read Access Geo Redundant Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a>Prestandaproblem
Upplevelsen av programprestanda kan vara högst subjektiv, särskilt från ett användarperspektiv. Därför är det viktigt att ha tillgång till jämförelsemått, så att du tydligt ser när det blir problem med prestandan. Många faktorer kan påverka prestanda för en Azure storage-tjänst från programmet klientperspektiv. De här faktorerna kan fungera i lagringstjänsten, klienten eller nätverksinfrastruktur. Därför är det viktigt att ha en strategi för att identifiera prestandaproblemet ursprung.

När du har identifierat sannolikt platsen för orsaken till prestandaproblemet från mått som använda du sedan loggfilerna för att hitta detaljerad information för att diagnostisera och Felsök problemet.

I avsnittet ”[felsökningsanvisningar]” senare i den här guiden innehåller mer information om några vanliga prestanda-relaterade problem kan uppstå.

### <a name="diagnosing-errors"></a>Diagnostisera fel
Användare av ditt program kan meddela dig om felen som rapporteras av klientprogrammet. Lagringsmått även registrerar antalet olika feltyper från storage-tjänster som **NetworkError**, **ClientTimeoutError**, eller **AuthorizationError**. Även om mätvärden i Storage poster endast antalet olika feltyper, hittar du mer information om enskilda förfrågningar genom att undersöka serversidan och klientsidan nätverksloggar. Vanligtvis ger HTTP-statuskoden som returneras av lagringstjänsten en indikation om varför begäran misslyckades.

> [!NOTE]
> Kom ihåg att du bör förvänta dig att se vissa tillfälliga fel: till exempel fel på grund av tillfälliga nätverksproblem eller programfel.
>
>

Följande resurser är användbara om du vill förstå lagringsrelaterade statusar och felkoder:

* [Vanliga REST API-felkoder](https://msdn.microsoft.com/library/azure/dd179357.aspx)
* [Felkoder för Blob Service](https://msdn.microsoft.com/library/azure/dd179439.aspx)
* [Felkoder för kö](https://msdn.microsoft.com/library/azure/dd179446.aspx)
* [Felkoder för tabell](https://msdn.microsoft.com/library/azure/dd179438.aspx)
* [Felkoder för fil](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a>Storage-emulatorn problem
Azure SDK innehåller ett storage-emulatorn som du kan köra på en arbetsstation för utveckling. Den här emulatorn simulerar de flesta av beteendet för Azure storage-tjänster och är användbart vid utveckling och testning, så att du kan köra program som använder Azure storage-tjänster utan att behöva en Azure-prenumeration och ett Azure storage-konto.

Den ”[felsökningsanvisningar]” i den här guiden beskriver vissa vanliga problem som kan uppstå med lagringsemulatorn.

### <a name="storage-logging-tools"></a>Loggningsverktyg för lagring
Med loggningen i Storage tillhandahåller serversidan loggningen av lagring i Azure storage-kontot. Läs mer om hur du aktiverar loggning för serversidan och komma åt loggdata [aktivera loggning för lagring och åtkomst till loggdata](https://go.microsoft.com/fwlink/?LinkId=510867).

Storage-klientbiblioteket för .NET kan du samla in loggdata för klientsidan som relaterar till storage-åtgärder som utförs av ditt program. Mer information finns i artikeln om [klientloggning med .NET Storage Client Library](https://go.microsoft.com/fwlink/?LinkId=510868).

> [!NOTE]
> I vissa fall (t.ex SAS auktoriseringsfel), kan en användare rapporterar ett fel som du hittar inga data om begäran i Storage-loggar från serversidan. Du kan använda funktioner för loggning av Storage-klientbiblioteket för att undersöka om orsaken till problemet finns på klienten eller använda verktyg för nätverksövervakning för att undersöka nätverket.
>
>

### <a name="using-network-logging-tools"></a>Med hjälp av verktyg för loggning
Du kan fånga in trafik mellan klienten och servern som ger detaljerad information om de data som klienten och servern utbyter och underliggande nätverksförhållanden. Användbara verktyg för loggning är:

* [Fiddler](https://www.telerik.com/fiddler) är en kostnadsfri web proxy som hjälper dig att granska rubriker och nyttolasten för HTTP och HTTPS-begäran och svar-meddelanden för felsökning. Mer information finns i [bilaga 1: Med hjälp av Fiddler för att avbilda trafik över HTTP och HTTPS](#appendix-1).
* [Microsoft Network Monitor (Netmon)](https://www.microsoft.com/download/details.aspx?id=4865) och [Wireshark](https://www.wireshark.org/) är kostnadsfria network protocol analysverktyg som hjälper dig att visa Paketinformation om detaljerade för en mängd olika nätverksprotokoll. Mer information om Wireshark finns i ”[bilaga 2: Med hjälp av Wireshark för att avbilda nätverkstrafik](#appendix-2)”.
* Microsoft Message Analyzer är ett verktyg från Microsoft som ersätter Netmon och att hämta paket nätverksdata, hjälper dig att visa och analysera loggdata från andra verktyg. Mer information finns i ”[tillägg 3: Använder Microsoft Message Analyzer för att avbilda nätverkstrafik](#appendix-3)”.
* Om du vill utföra en grundläggande anslutningstest för att kontrollera att klientdatorn kan ansluta till Azure storage-tjänsten över nätverket, du kan inte göra detta med standarden **ping** verktyget på klienten. Men du kan använda den [ **tcping** verktyget](https://www.elifulkerson.com/projects/tcping.php) att kontrollera anslutningen.

Loggdata från Storage loggning och Storage-klientbiblioteket är tillräckligt för att diagnostisera problem i många fall, men i vissa fall kan du behöva mer detaljerad information som kan ge dessa verktyg för loggning. Med hjälp av Fiddler för att visa meddelanden för HTTP och HTTPS kan du visa huvud och nyttolast data som skickas till och från lagringstjänster, vilket gör att du kan granska hur ett klientprogram försöker lagringsåtgärder. Protokollet analysverktyg, till exempel Wireshark fungerar på paketnivå så att du kan visa data för TCP, vilket gör att du kan felsöka förlorade paket och problem med nätverksanslutningen. Message Analyzer kan fungera både HTTP och TCP-lager.

## <a name="end-to-end-tracing"></a>Slutpunkt till slutpunkt-spårning
Slutpunkt till slutpunkt-spårning med en mängd olika loggfiler är en användbar teknik för att undersöka potentiella problem. Du kan använda datum/tid-informationen från din måttdata som en indikation på var du vill börja titta i loggfilerna för detaljerad information som hjälper dig att felsöka problemet.

### <a name="correlating-log-data"></a>Korrelera loggdata
När du visar loggar från klientprogram nätverk spårar och serversidan lagringsloggningen som det är viktigt att kunna korrelera begär mellan de olika loggfilerna. Loggfilerna omfattar ett antal olika fält som är användbara Korrelations-ID: n. Klientens begärans-ID är mest användbara fältet som används för att korrelera poster i olika loggarna. Men ibland kan det vara praktiskt att använda server begäran-ID eller tidsstämplar. Följande avsnitt innehåller mer information om dessa alternativ.

### <a name="client-request-id"></a>ID för klientbegäran
Storage-klientbiblioteket genererar automatiskt ett unikt klient-ID för begäran för varje begäran.

* I loggen för klientsidan som skapar Storage-klientbiblioteket, klientens begärans-ID som visas i den **ID för klientbegäran** i varje loggpost som rör begäran.
* Klientens begärans-ID är en nätverksspårning, till exempel en avbildas av Fiddler synligt i meddelanden som den **x-ms-client-request-id** http-huvudets värde.
* Klientens begärans-ID visas i kolumnen klient-ID för begäran i loggen Storage Logging serversidan.

> [!NOTE]
> Det är möjligt för flera begäranden att dela samma begäran-ID för klienten eftersom klienten kan tilldela det här värdet (även om Storage-klientbiblioteket tilldelar automatiskt ett nytt värde). När klienten försöker delar alla försök samma klient-begäran-ID. När det gäller en batch som skickats från klienten har batch en enskild klient begärande-ID.
>
>

### <a name="server-request-id"></a>Server-begäran-ID
Storage-tjänsten genererar automatiskt server begäran-ID: n.

* Log Storage Logging serversidan server begäran-ID visas den **huvud för begäran-ID** kolumn.
* I en nätverksspårning, till exempel en avbildas av Fiddler server begäran-ID som visas i svarsmeddelanden som den **x-ms-request-id** http-huvudets värde.
* I loggen för klientsidan som skapar Storage-klientbiblioteket, server begäran-ID som visas i den **åtgärden Text** kolumn för loggposten som visar information om svaret från servern.

> [!NOTE]
> Lagringstjänsten tilldelar alltid en unik server-ID för förfrågan till varje begäran som den tar emot så att varje återförsök från klienten och varje åtgärd som ingår i en batch har ett unikt begäran-ID.
>
>

Om Storage-klientbiblioteket genererar en **StorageException** i klienten, den **RequestInformation** egenskapen innehåller en **RequestResult** objekt som innehåller en  **ServiceRequestID** egenskapen. Du kan också komma åt en **RequestResult** objekt från en **OperationContext** instans.

Kodexemplet nedan visar hur du ställer in en anpassad **ClientRequestId** värde genom att koppla en **OperationContext** objekt-begäran till lagringstjänsten. Den visar även hur du hämtar den **ServerRequestId** värdet från svarsmeddelandet.

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

### <a name="timestamps"></a>Timestamps
Du kan också använda tidsstämplar för att hitta relaterade poster, men tänk på alla klockan skeva mellan klienten och servern som kan finnas. Sök plus eller minus 15 minuter för matchande serversidan poster baserat på tidsstämpeln på klienten. Kom ihåg att blob-metadata för objekt som innehåller mått visar tidsintervall för de mått som lagras i blob. Det här tidsintervallet är användbart om du har många mått blobar för samma minut eller timme.

## <a name="troubleshooting-guidance"></a>Felsökningsinformation
Det här avsnittet hjälper dig med diagnos och felsökning av program på några av de vanligaste problemen som kan uppstå när du använder Azure storage-tjänster. Använd listan nedan för att hitta informationen som är relevant för problemet.

**Felsöka beslutsträd**

---
Ditt problem som relaterar till prestanda hos en storage-tjänster?

* [Mätningar visar ett högt värde för AverageE2ELatency och ett lågt värde för AverageServerLatency]
* [Mätningar visar låga värden för AverageE2ELatency och AverageServerLatency, men klienten har ändå långa svarstider]
* [Mätningar visar ett högt värde för AverageServerLatency]
* [Du upplever oväntade fördröjningar i en köad meddelandeleverans]

---
Ditt problem som rör tillgängligheten för ett storage-tjänster?

* [Mätningar visar en ökning i PercentThrottlingError]
* [Mätningar visar en ökning i PercentTimeoutError]
* [Mätningar visar en ökning i PercentNetworkError]

---
 Ditt klientprogram tar emot en HTTP 4XX (till exempel 404)-svar från en lagringstjänst?

* [Klienten får HTTP 403-meddelanden (Förbjudet)]
* [Klienten får HTTP 404-meddelanden (Hittades inte)]
* [Klienten tar emot HTTP 409 (konflikt) meddelanden]

---
[Mätvärdena visar låg PercentSuccess eller analytics loggposter innehålla åtgärder med transaktionsstatus för ClientOtherErrors]

---
[Kapacitetsdata visar en oväntad ökning i kapacitetsförbrukning för lagring]

---
[Du upplever oväntade omstarter av virtuella datorer som har ett stort antal anslutna virtuella hårddiskar]

---
[Problemet uppstår från med hjälp av storage-emulatorn för utveckling eller testning]

---
[Det uppstår problem med att installera Azure SDK för .NET]

---
[Du har ett annat problem med en storage-tjänst]

---
### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Mätningar visar hög AverageE2ELatency och AverageServerLatency
Bilden nedan från den [Azure-portalen](https://portal.azure.com) övervakning verktyget visar ett exempel där den **AverageE2ELatency** är betydligt högre än den **AverageServerLatency**.

![][4]

Lagringstjänsten beräknar endast måttet **AverageE2ELatency** för lyckade begäranden och till skillnad från **AverageServerLatency**, innefattar den tid det tar klienten för att skicka data och ta emot bekräftelse från storage-tjänsten. Därför skillnad mellan **AverageE2ELatency** och **AverageServerLatency** kan vara antingen på grund av klientprogrammet som svarar långsamt eller på grund av villkor i nätverket.

> [!NOTE]
> Du kan också visa **E2ELatency** och **ServerLatency** logga data för enskilda lagringsåtgärder i loggning av lagring.
>
>

#### <a name="investigating-client-performance-issues"></a>Undersöka prestandaproblem för klienten
Möjliga orsaker till klienten svarar långsamt är att ha ett begränsat antal tillgängliga anslutningar eller trådar eller som ont om resurser, till exempel processor, minne eller bandbredd. Du kanske kan lösa problemet genom att ändra klientkod för att vara mer effektivt (till exempel med hjälp av asynkrona anrop till lagringstjänsten) eller genom att använda en större virtuell dator (med fler kärnor och mer minne).

För tjänsterna tabell och kö Nagle algoritmen kan också orsakas av hög **AverageE2ELatency** jämfört med **AverageServerLatency**: Mer information finns i inlägget [Nagle's Algoritmen är inte eget för små begäranden](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx). Du kan inaktivera Nagle algoritmen i koden med hjälp av den **ServicePointManager** klassen i den **System.Net** namnområde. Du bör göra detta innan du gör några anrop till tabellen eller queue Service i ditt program eftersom det inte påverkar anslutningar som redan är öppna. I följande exempel kommer från den **Application_Start** -metod i en arbetsroll.

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
tableServicePoint.UseNagleAlgorithm = false;
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

Du bör kolla på klientsidan-loggarna och se hur många begäranden klientprogrammet skickar in och Sök efter allmänna .NET relaterade flaskhalsar i prestanda i din klient, till exempel CPU, .NET skräpinsamling, belastningen på nätverket eller minne. Som en startpunkt för felsökning av .NET-klientprogram, se [felsökning och spårning av Profiling](https://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Undersöka problem med nätverkssvarstiden
Hög svarstiden för slutpunkt till slutpunkt som orsakas av nätverket är vanligtvis bero på tillfälliga. Du kan undersöka både tillfälliga och permanenta nätverksproblem, till exempel avbrutna paket med hjälp av verktyg som Wireshark eller Microsoft Message Analyzer.

Mer information om hur du använder Wireshark felsökning av nätverksproblem med finns i ”[bilaga 2: Med hjälp av Wireshark för att avbilda nätverkstrafik]”.

Mer information om hur du använder Microsoft Message Analyzer för att felsöka nätverksproblem finns i ”[tillägg 3: Använder Microsoft Message Analyzer för att avbilda nätverkstrafik]”.

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Mätningar visar låga AverageE2ELatency och AverageServerLatency, men klienten har hög latens
I det här scenariot är den mest troliga orsaken en fördröjning i lagringsbegäranden når lagringstjänsten. Du bör undersöka varför förfrågningar från klienten inte gör det via blob-tjänsten.

En möjlig orsak för klienten att skicka förfrågningar är att det finns ett begränsat antal tillgängliga anslutningar eller trådar.

Också kontrollera om klienten utför flera återförsök och ta reda på vad om det är. För att avgöra om klienten utför flera återförsök, kan du:

* Undersök i Storage Analytics-loggarna. Om flera återförsök sker, visas flera åtgärder med samma ID för klientens begäran men med olika serverbegäran ID: N.
* Granska loggarna för klienten. Utförlig loggning indikerar att ett nytt försök har inträffat.
* Felsöka din kod och kontrollera egenskaperna för den **OperationContext** objektet som är associerat med begäran. Om åtgärden försök har gjorts, den **RequestResults** egenskapen innehåller flera unika serverbegäran ID: N. Du kan också kontrollera start- och sluttider för varje begäran. Mer information finns i kodexemplet i avsnittet [Server-begäran-ID].

Om det finns inga problem i klienten, bör du undersöka potentiella problem i nätverket, till exempel paketförlust. Du kan använda verktyg som Wireshark eller Microsoft Message Analyzer för att undersöka nätverksproblem.

Mer information om hur du använder Wireshark felsökning av nätverksproblem med finns i ”[bilaga 2: Med hjälp av Wireshark för att avbilda nätverkstrafik]”.

Mer information om hur du använder Microsoft Message Analyzer för att felsöka nätverksproblem finns i ”[tillägg 3: Använder Microsoft Message Analyzer för att avbilda nätverkstrafik]”.

### <a name="metrics-show-high-AverageServerLatency"></a>Mätningar visar hög AverageServerLatency
När det gäller hög **AverageServerLatency** för blob-hämtningsbegäranden, bör du använda loggningen i Storage-loggar för att se om det finns upprepade begäranden för samma blob (eller uppsättning blobbar). För förfrågningar om blob överför bör du undersöka vilka blockera klienten är använder (till exempel blockeras som mindre än 64 kB i storlek kan resultera i omkostnader, såvida inte läsningar finns också i mindre än 64 kB segmenterar) och om flera klienter laddar upp block till samma blob i stycke llellt. Du bör också kontrollera mått per minut för toppar i antalet begäranden som resulterar i mer än den per sekund skalbarhetsmål: Se även ”[mätningar visar en ökning i PercentTimeoutError]”.

Om du ser högt **AverageServerLatency** begäranden när det ska upprepas för att ladda ned blob begär samma blob eller uppsättning blobar och du bör överväga att cachelagring av dessa blobar med hjälp av Azure Cache eller Azure Content Delivery Network (CDN). Du kan förbättra dataflödet genom att använda en större blockstorlek för förfrågningar om överföringen. För frågor till tabeller är det också möjligt att implementera klientcachelagring på klienter som utför åtgärder för samma fråga och där data inte ändras ofta.

Hög **AverageServerLatency** värden kan också vara ett symtom på dåligt utformat tabeller eller att leder till att genomsökningen eller som följer mönstret Lägg till/åtkomstgruppen anti-frågor. Mer information finns i ”[mätningar visar en ökning i PercentThrottlingError]”.

> [!NOTE]
> Du hittar en omfattande checklista prestanda checklista här: [Microsoft Azure Storage-prestanda och Skalbarhetschecklista](storage-performance-checklist.md).
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Du upplever oväntade fördröjningar i en köad meddelandeleverans
Om du upplever en fördröjning mellan den tid som ett program lägger till ett meddelande till en kö och den tid som blir den tillgänglig för att läsa från kö, ska du utföra följande steg för att diagnostisera problemet:

* Kontrollera att programmet har lägger till meddelanden i kön. Kontrollera att programmet inte försöker på nytt den **AddMessage** metoden flera gånger innan du lyckas. Storage-klientbiblioteket loggarna visar alla upprepade försök av lagringsåtgärder.
* Kontrollera det finns inga klockan skeva mellan den arbetsroll som lägger tillför meddelandet i kön och den arbetsroll som läser meddelandet från kön som gör det visas som om det uppstår en fördröjning i bearbetningen.
* Kontrollera om den arbetsroll som läser meddelanden från kön skadad. Om en kö klient anropar den **GetMessage** metoden men misslyckas att svara med en bekräftelse, meddelandet förblir osynliga för kön tills den **invisibilityTimeout** period har löpt ut. Nu blir meddelandet tillgängligt för bearbetning igen.
* Kontrollera om kölängden växer med tiden. Detta kan inträffa om du inte har tillräckligt med arbetare som är tillgängliga för att bearbeta alla meddelanden som kräver andra arbetena i kön. Kontrollera också mått för att se om ta bort begäranden som misslyckas och ta räkna med meddelanden som kan tyda på upprepade misslyckade försök att ta bort meddelandet.
* Granska loggarna Storage loggning för att utföra åtgärder i kö som har högre än förväntat **E2ELatency** och **ServerLatency** värden under en längre tid än vanligt.

### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Mätningar visar en ökning i PercentThrottlingError
Begränsningsfel inträffa när du överskrider det för skalbarhetsmål för en lagringstjänst. Begränsningar för storage-tjänsten att säkerställa att ingen enskild klient eller -klient kan använda tjänsten på bekostnad av andra. Mer information finns i [skalbarhet för lagring av Azure- och prestandamål](storage-scalability-targets.md) mer information om skalbarhetsmål för storage-konton och prestandamål för partitioner i storage-konton.

Om den **PercentThrottlingError** mått visar en ökning i procent av begäranden som misslyckas med felet begränsning, måste du undersöka en av två scenarier:

* [Tillfällig ökning i PercentThrottlingError]
* [Permanent ökning i PercentThrottlingError fel]

En ökning av **PercentThrottlingError** ofta inträffar samtidigt som en ökning av antalet lagringsbegäranden eller när du är inledningsvis belastningstester ditt program. Detta kan också visa sig i klienten som ”503 Server upptagen” eller ”tidsgräns för 500 åtgärd” HTTP statusmeddelanden från lagringsåtgärder.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Tillfällig ökning i PercentThrottlingError
Om du ser toppar i värdet för **PercentThrottlingError** som sammanfaller med perioder med hög aktivitet för programmet, du implementerar en exponentiell (inte linjär) backoff-strategi för återförsök i din klient. Backoff försök minska omedelbar belastningen på partitionen och hjälpa till att jämna ut trafiktoppar i programmet. Mer information om hur du implementerar återförsöksprinciper med Storage-klientbiblioteket finns i [Microsoft.windowsazure.Storage.retrypolicies när Namespace](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx).

> [!NOTE]
> Du kan också se toppar i värdet för **PercentThrottlingError** som inte krockar med perioder med hög aktivitet för programmet: den mest troliga orsaken är lagringstjänsten flytta partitioner för att förbättra belastningsutjämningen.
>
>

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Permanent ökning i PercentThrottlingError fel
Om du ser ett konsekvent högt värde för **PercentThrottlingError** följa ökningen är permanent i din transaktionsvolymer eller när du utför den inledande inläsningen testar på ditt program, måste du utvärdera hur programmet använder partitioner för lagring och om den närmar sig det för skalbarhetsmål för ett lagringskonto. Till exempel om du råkar ut för begränsningsfel i en kö (som räknas som en enda partition), bör sedan du använda ytterligare köer för att sprida transaktioner över flera partitioner. Om du råkar ut för begränsningsfel i en tabell, måste du använda ett annat partitioneringsschema för sprida transaktioner över flera plattformar med hjälp av ett brett spektrum av partitionsnyckelvärdena. En vanlig orsak till detta problem är det prepend/lägga till ett mönster där du väljer datumet som partitionsnyckel och sedan alla data på en viss dag skrivs till en partition: under belastning, kan detta resultera i en flaskhals för skrivning. Antingen du välja en annan partitionering design eller utvärdera om med blob storage kan vara en bättre lösning. Också kontrollera om begränsning sker till följd av toppar i trafiken och undersöka sätt av Utjämning mönstret av begäranden.

Om du distribuerar dina transaktioner över flera plattformar, måste du fortfarande vara medveten skalbarhetsgränserna för lagringskontot. Till exempel om du använde tio köer bearbetning av högst 2 000 1KB-meddelanden per sekund, vara på den totala gränsen på 20 000 meddelanden per sekund för storage-kontot. Om du vill bearbeta fler än 20 000 enheter per sekund, bör du överväga att använda flera lagringskonton. Du bör också ha i åtanke att storleken på dina förfrågningar och entiteter har en inverkan på när lagringstjänsten begränsar klienter: Om du har större begäranden och entiteter kan du kanske att begränsas snabbare.

Ineffektiv frågans design kan även orsaka att träffa skalbarhetsgränserna för tabellpartitioner. Exempelvis kan måste en fråga med ett filter som endast väljer en procent av entiteterna i en partition men som söker igenom alla entiteter i en partition komma åt varje entitet. Varje entitet läsa räknas in i det totala antalet transaktioner i den partitionen. Därför kan nå du enkelt kan det för skalbarhetsmål.

> [!NOTE]
> Din prestandatestning bör Visa inga ineffektiv fråga Designer i ditt program.
>
>

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Mätningar visar en ökning i PercentTimeoutError
Din mätningar visar en ökning i **PercentTimeoutError** för en storage-tjänster. Klienten får ett stort antal statusmeddelanden som ”tidsgräns för 500 åtgärd” HTTP på samma gång, från lagringsåtgärder.

> [!NOTE]
> Du kan se timeout-fel tillfälligt som lagringstjänsten läsa in saldon begäranden genom att flytta en partition till en ny server.
>
>

Den **PercentTimeoutError** mått är en sammanställning av följande mått: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**,  **AnonymousServerTimeoutError**, och **SASServerTimeoutError**.

Server-timeout orsakas av ett fel på servern. Klient-timeout inträffar eftersom en åtgärd på servern har överskridit tidsgränsen som angetts av klienten. en klient som använder Storage-klientbiblioteket kan till exempel ange en tidsgräns för en åtgärd med hjälp av den **ServerTimeout** egenskapen för den **QueueRequestOptions** klass.

Server-timeout tyda på ett problem med storage-tjänsten som kräver ytterligare undersökning. Du kan använda mått för att se om du stöter på skalbarhetsgränserna för tjänsten och för att identifiera eventuella toppar i trafiken som gör att det här problemet. Om problemet är tillfälligt, kan det bero belastningsutjämning aktivitet i tjänsten. Om problemet är permanent och inte orsakas av ditt program når skalbarhetsgränserna för tjänsten, bör du öka ett supportärende. För klient-timeout måste du bestämma om timeout-värdet är inställt på ett lämpligt värde i klienten och antingen ändra timeout-värdet som angetts i klienten eller undersöka hur du kan förbättra prestanda för åtgärder i storage-tjänsten, till exempel genom att optimera tabell-frågor eller minska storleken på dina meddelanden.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>Mätningar visar en ökning i PercentNetworkError
Din mätningar visar en ökning i **PercentNetworkError** för en storage-tjänster. Den **PercentNetworkError** mått är en sammanställning av följande mått: **NetworkError**, **AnonymousNetworkError**, och **SASNetworkError**. Dessa visas när lagringstjänsten uppstår ett fel när klienten skickar en begäran om lagring.

Den vanligaste orsaken till felet är en klient kopplar från innan tidsgränsen upphör att gälla i lagringstjänsten. Undersöka koden i din klient för att förstå varför och när klienten kopplas från storage-tjänsten. Du kan också använda Wireshark, Microsoft Message Analyzer eller Tcping för att undersöka problem med nätverksanslutningen från klienten. Dessa verktyg beskrivs i den [tilläggen].

### <a name="the-client-is-receiving-403-messages"></a>Klienten tar emot HTTP 403 (förbjudet) meddelanden
Om klientprogrammet utfärdar HTTP 403-fel (förbjudet) beror det förmodligen på att klienten använder en SAS (signatur för delad åtkomst) som har upphört att gälla när den skickar förfrågningar om lagring (även om det finns andra orsaker, som klockförskjutning, ogiltiga nycklar eller tomma rubriker). Om orsaken är en SAS-nyckel som har upphört att gälla visas inte några poster i Storage Logging-loggdata på serversidan. I följande tabell visar ett exempel från klientsidan loggen genereras av Storage-klientbiblioteket som illustrerar det här problemet inträffar:

| Källa | Utförlighet | Utförlighet | ID för klientförfrågan | Åtgärden text |
| --- | --- | --- | --- | --- |
| Microsoft.WindowsAzure.Storage |Information |3 |85d077ab-... |Startar åtgärden med platsen primära per platsläget PrimaryOnly. |
| Microsoft.WindowsAzure.Storage |Information |3 |85d077ab-... |Startar synkron begäran till <https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&sr=c&si=mypolicy&sig=OFnd4Rd7z01fIvh%2BmcR6zbudIH2F5Ikm%2FyhNYZEmJNQ%3D&api-version=2014-02-14> |
| Microsoft.WindowsAzure.Storage |Information |3 |85d077ab-... |Väntar på svar. |
| Microsoft.WindowsAzure.Storage |Varning |2 |85d077ab-... |Ett undantag uppstod under väntan på svar: Fjärrservern returnerade ett fel: (403) Förbjuden. |
| Microsoft.WindowsAzure.Storage |Information |3 |85d077ab-... |Svaret togs emot. Status code = 403, Request ID = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = , ETag = . |
| Microsoft.WindowsAzure.Storage |Varning |2 |85d077ab-... |Ett undantag uppstod under åtgärden: Fjärrservern returnerade ett fel: (403) Forbidden.. |
| Microsoft.WindowsAzure.Storage |Information |3 |85d077ab-... |Kontrollerar om åtgärden ska göras. Antal nya försök = 0, HTTP-statuskod = 403, undantag = fjärrservern returnerade ett fel: (403) Forbidden.. |
| Microsoft.WindowsAzure.Storage |Information |3 |85d077ab-... |Nästa plats har angetts till primär, beroende på plats. |
| Microsoft.WindowsAzure.Storage |Fel |1 |85d077ab-... |Återförsöksprincipen tillät inte för ett nytt försök. Misslyckas med fjärrservern returnerade ett fel: (403) Förbjuden. |

Du bör undersöka varför SAS-token upphör att gälla innan klienten skickar token till servern i det här scenariot:

* Normalt bör du inte ange någon starttid när du skapar en SAS som klienten ska använda direkt. Om det förekommer små klockskillnader mellan värden som genererar SAS och lagringstjänsten kan tjänsten ta emot en SAS som inte har börjat gälla ännu.
* Ange inte en mycket kort giltighetstid för en SAS. Återigen kan små klockskillnader mellan värden som genererar SAS och lagringstjänsten göra att SAS tycks upphöra tidigare än förväntat.
* Har du versionsparametern i SAS-nyckeln (till exempel **SA = 2015-04-05**) matchar versionen av Storage-klientbiblioteket som du använder? Vi rekommenderar att du alltid använder den senaste versionen av den [Lagringsklientbiblioteket](https://www.nuget.org/packages/WindowsAzure.Storage/).
* Om du genererar om dina lagringsåtkomstnycklar kan befintliga SAS-token bli ogiltiga. Det här problemet kan uppstå om du genererar SAS-token med lång förfallotid som klientappar ska cachelagra.

Om du använder Storage Client Library till att generera SAS-token är det enkelt att skapa en giltig token. Men om du använder Storage REST API och konstruera SAS-token manuellt, se [delegera åtkomst med en signatur för delad åtkomst](https://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-404-messages"></a>Klienten tar emot meddelanden HTTP 404 (hittades inte)
Om klientprogrammet tar emot ett HTTP 404-meddelande (hittades inte) från servern innebär det att objektet som klienten försökte använda (till exempel en entitet, tabell, blob, container eller kö) inte finns i lagringstjänsten. Här är några av de möjliga orsakerna:

* [Klienten eller en annan process har tagit bort objektet]
* [Ett problem med auktorisering delade signatur åtkomst (SAS)]
* [Klientens JavaScript-kod har inte behörighet att komma åt objektet]
* [Nätverksfel]

#### <a name="client-previously-deleted-the-object"></a>Klienten eller en annan process tidigare bort objektet
I scenarier där klienten försöker läsa, uppdatera eller ta bort data i en lagringstjänst är det vanligtvis lätt att identifiera i loggarna för serversidan en tidigare åtgärd som tagits bort det aktuella objektet från lagringstjänsten. Ofta visar loggdata att en annan användare eller process bort objektet. I loggen Storage Logging serversidan Visar åtgärdstypen och begärt Objektnyckel kolumner när en klient bort ett objekt.

I scenariot där en klient försöker infoga ett objekt, kanske den inte omedelbart uppenbar varför detta resulterar i ett HTTP 404 (hittades inte)-svar med hänsyn till att klienten skapar ett nytt objekt. Om klienten skapar en blob som den måste kunna hitta blob-behållare om klienten skapar ett meddelande som måste kunna hitta en kö, och om klienten är att lägga till en rad att det dock kunna hitta tabellen.

Du kan använda loggen på klientsidan från Storage-klientbiblioteket för att få en mer detaljerad förståelse för när klienten skickar specifika begäranden till storage-tjänsten.

Följande klientsidan loggen genereras av Storage-klientbiblioteket illustrerar problemet när klienten inte kan hitta behållaren för blob skapas. Den här loggfilen innehåller information om följande lagringsåtgärder:

| ID för förfrågan | Åtgärd |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** metod för att ta bort blob-behållaren. Observera att den här åtgärden innehåller en **HEAD** begäran om att kontrollera om finns i behållaren. |
| e2d06d78… |**CreateIfNotExists** metod för att skapa blob-behållaren. Observera att den här åtgärden innehåller en **HEAD** begäran som kontrollerar om finns i behållaren. Den **HEAD** returnerar ett 404 meddelande men fortsätter. |
| de8b1c3c-... |**UploadFromStream** metod för att skapa bloben. Den **PLACERA** begäran misslyckas med ett 404 meddelande |

Loggposter:

| ID för förfrågan | Åtgärden Text |
| --- | --- |
| 07b26a5d-... |Startar synkron begäran till https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = HEAD............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Väntar på svar. |
| 07b26a5d-... |Svaret togs emot. Statuskod = 200, ID för begäran = eeead849... Innehåll MD5 = ETag = &quot;0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |Svarshuvuden bearbetades, fortsätter med resten av åtgärden. |
| 07b26a5d-... |Hämtar svarstexten. |
| 07b26a5d-... |Åtgärden har slutförts. |
| 07b26a5d-... |Startar synkron begäran till https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = DELETE...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Väntar på svar. |
| 07b26a5d-... |Svaret togs emot. Statuskod = 202, ID för begäran = 6ab2a4cf..., innehåll MD5 = ETag =. |
| 07b26a5d-... |Svarshuvuden bearbetades, fortsätter med resten av åtgärden. |
| 07b26a5d-... |Hämtar svarstexten. |
| 07b26a5d-... |Åtgärden har slutförts. |
| e2d06d78-... |Startar asynkron begäran till https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td> |
| e2d06d78-... |StringToSign = HEAD...x-ms-client-request-id:e2d06d78-...x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Väntar på svar. |
| de8b1c3c-... |Startar synkron begäran till https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |StringToSign = LÄGG till... 64.qCmF+TQLPhq/YYK50mP9ZQ==...x-MS-BLOB-Type:BlockBlob.x-MS-Client-Request-ID:de8b1c3c-...x-MS-Date:TUE 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Förbereder att skriva data för begäran. |
| e2d06d78-... |Ett undantag uppstod under väntan på svar: Fjärrservern returnerade ett fel: (404) gick inte att hitta... |
| e2d06d78-... |Svaret togs emot. Statuskod = 404, ID för begäran = 353ae3bc..., innehåll MD5 = ETag =. |
| e2d06d78-... |Svarshuvuden bearbetades, fortsätter med resten av åtgärden. |
| e2d06d78-... |Hämtar svarstexten. |
| e2d06d78-... |Åtgärden har slutförts. |
| e2d06d78-... |Startar asynkron begäran till https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| e2d06d78-... |StringToSign = LÄGG till... 0...x-MS-Client-Request-ID:e2d06d78-...x-MS-Date:TUE 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Väntar på svar. |
| de8b1c3c-... |Begäran-skrivdata. |
| de8b1c3c-... |Väntar på svar. |
| e2d06d78-... |Ett undantag uppstod under väntan på svar: Fjärrservern returnerade ett fel: (409) konflikt... |
| e2d06d78-... |Svaret togs emot. Statuskod = 409, ID för begäran = c27da20e..., innehåll MD5 = ETag =. |
| e2d06d78-... |Hämtar fel svarstexten. |
| de8b1c3c-... |Ett undantag uppstod under väntan på svar: Fjärrservern returnerade ett fel: (404) gick inte att hitta... |
| de8b1c3c-... |Svaret togs emot. Statuskod = 404, ID för begäran = 0eaeab3e..., innehåll MD5 = ETag =. |
| de8b1c3c-... |Ett undantag uppstod under åtgärden: Fjärrservern returnerade ett fel: (404) gick inte att hitta... |
| de8b1c3c-... |Återförsöksprincipen tillät inte för ett nytt försök. Misslyckas med fjärrservern returnerade ett fel: (404) gick inte att hitta... |
| e2d06d78-... |Återförsöksprincipen tillät inte för ett nytt försök. Misslyckas med fjärrservern returnerade ett fel: (409) konflikt... |

I det här exemplet visar loggen att klienten interleaving begäranden från den **CreateIfNotExists** metod (begäran-ID e2d06d78...) med begäranden från den **UploadFromStream** metod (de8b1c3c-...). Den här interleaving inträffar eftersom klientprogrammet aktiverar de här metoderna asynkront. Ändra asynkrona koden i klienten för att se till att den skapar behållaren innan du försöker överföra data till en blob i behållaren. Helst bör du skapa alla behållare i förväg.

#### <a name="SAS-authorization-issue"></a>Ett problem för signatur för delad åtkomst (SAS)-auktorisering
Om klientprogrammet försöker använda en SAS-nyckel som inte innehåller behörigheterna som krävs för åtgärden, returnerar lagringstjänsten ett HTTP 404 (hittades inte)-meddelande till klienten. På samma gång, kan du även se ett annat värde än noll för **SASAuthorizationError** i mått.

I följande tabell visas ett exempel från serversidan loggmeddelande från loggfilen Storage loggning:

| Namn | Värde |
| --- | --- |
| Starttid för begäran | 2014-05-30T06:17:48.4473697Z |
| Åtgärdstyp     | GetBlobProperties            |
| Status för begäran     | SASAuthorizationError        |
| HTTP-statuskod   | 404                          |
| Autentiseringstyp| SAS                          |
| Typ av tjänst       | Blob                         |
| URL för begäran        | https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&sig=XXXXX&;api-version=2014-02-14 |
| Rubrik för begäran-ID  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| ID för klientförfrågan  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Undersök varför ditt klientprogram försöker utföra en åtgärd som den inte har beviljats behörighet.

#### <a name="JavaScript-code-does-not-have-permission"></a>Klientens JavaScript-kod har inte behörighet att komma åt objektet
Om du använder en JavaScript-klient och lagringstjänsten returnerar HTTP 404-meddelanden, Sök efter följande JavaScript-fel i webbläsaren:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Du kan använda F12-utvecklarverktygen i Internet Explorer för att spåra de meddelanden som utbyts mellan webbläsaren och storage-tjänsten när du felsöker problem för klientsidan JavaScript.
>
>

Dessa fel eftersom webbläsaren implementerar den [princip om samma ursprung](https://www.w3.org/Security/wiki/Same_Origin_Policy) säkerhetsbegränsning som förhindrar att en webbsida anropar en API i en annan domän från domänen sidan kommer från.

Undvik problemet JavaScript, kan du konfigurera Cross Origin Resource Sharing (CORS) för klienten har åtkomst till storage-tjänsten. Mer information finns i [Cross-Origin Resource Sharing (CORS) Support för Azure Storage-tjänster](https://msdn.microsoft.com/library/azure/dn535601.aspx).

Följande kodexempel visar hur du konfigurerar din blob-tjänst för att tillåta JavaScript som körs i Contoso-domänen att få åtkomst till en blob i blob storage-tjänsten:

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

#### <a name="network-failure"></a>Ett nätverksfel
I vissa fall, förlorade nätverkspaket kan leda till lagringstjänsten returnera HTTP 404-meddelanden till klienten. Till exempel när klientprogrammet tar bort en entitet från tabelltjänsten Se utlöser en storage-undantag reporting-klienten en ”HTTP 404 (hittades inte)” statusmeddelande från tabelltjänsten. När du undersöker tabellen i table storage-tjänsten Se du att tjänsten tog bort entiteten som begärs.

Undantagsinformation i klienten innehåller ID för förfrågan (7e84f12d...) som tilldelats av table service för begäran: du kan använda den här informationen för att hitta mer information i loggarna för serversidan lagring genom att söka i den **huvud för begäran-id**  kolumn i loggfilen. Du kan också använda mått för att identifiera när fel som detta inträffar och sök sedan filerna baserat på tiden som måtten registreras det här felet. Den här loggpost visar att ta bort misslyckades med meddelandet ”HTTP (404) klient fel” status. Samma loggposten innehåller också begäran-ID som genereras av klienten i den **client-request-id** kolumn (813ea74f...).

Serversidan loggen innehåller också en annan post med samma **client-request-id** värde (813ea74f...) för ett lyckat borttagningsåtgärd för samma entitet och från samma klient. Den här åtgärden lyckas ta bort ägde rum precis innan de tar bort begäran.

Den mest troliga orsaken till det här scenariot är att klienten har skickat en delete-begäran för entiteten till table service, som är klar, men fick inte en bekräftelse från servern (kanske på grund av ett tillfälligt problem). Klienten sedan automatiskt göras igen (med samma **client-request-id**), och den här nya försöket misslyckades eftersom entiteten har redan tagits bort.

Om detta inträffar ofta bör du undersöka varför klienten kan inte ta emot bekräftelser från tabelltjänsten. Om problemet är tillfälligt, bör du hantera felet ”Det gick inte att hitta HTTP (404)” och logga den i klienten, men tillåta att klienten fortsätter.

### <a name="the-client-is-receiving-409-messages"></a>Klienten tar emot HTTP 409 (konflikt) meddelanden
I följande tabell visas ett utdrag ur serversidan loggen för två Klientåtgärder: **DeleteIfExists** följt omedelbart av **CreateIfNotExists** med hjälp av samma blobbehållarens namn. Varje klientåtgärden resulterar i två begäranden som skickas till servern, först en **GetContainerProperties** begäran om att kontrollera om behållaren finns, följt av den **DeleteContainer** eller  **CreateContainer** begäran.

| Tidsstämpel | Åtgärd | Resultat | Containerns namn | ID för klientförfrågan |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-... |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-... |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-... |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-... |

Koden i klientprogrammet bort och återskapar sedan omedelbart en blobbehållare med samma namn: den **CreateIfNotExists** metod (klienten begär ID bc881924-...) så småningom misslyckas med felet HTTP 409 (konflikt). När en klient tar bort containrar, tabeller eller köer tar det en liten stund innan namnet blir tillgängligt igen.

Klientappen bör använda unika containernamn när nya containrar skapas om det är ett vanligt mönster att ta bort/återskapa dem.

### <a name="metrics-show-low-percent-success"></a>Mätningar visar låga PercentSuccess eller analytics loggposter har åtgärder med transaktionsstatus av ClientOtherErrors
Den **PercentSuccess** mått samlar in procentuella av åtgärder som har genomförts baserat på deras HTTP-statuskod. Åtgärder med statuskoder för 2XX räknas som slutförd, medan åtgärder med statuskoder i 3XX, 4XX och 5XX intervall räknas som misslyckad och lägre den **PercentSuccess** måttvärde. I loggfilerna från serversidan storage de här åtgärderna registreras med transaktionsstatus **ClientOtherErrors**.

Det är viktigt att Observera att dessa åtgärder har slutförts och därför inte påverkar andra mått, till exempel tillgänglighet. Några exempel på åtgärder som köras men som kan leda till misslyckade HTTP-statuskoder är:

* **ResourceNotFound** (inte gick att hitta 404), till exempel från en GET-begäran till en blob som inte finns.
* **ResourceAlreadyExists** (konflikt 409), till exempel från en **CreateIfNotExist** åtgärden där resursen finns redan.
* **ConditionNotMet** (inte ändra 304), till exempel från en villkorlig åtgärd, till exempel när en klient skickar en **ETag** värde och en HTTP- **If-None-Match** huvudet för att begära en bild endast om den har uppdaterats sedan den senaste åtgärden.

Du hittar en lista över vanliga felkoder för REST-API som lagringstjänsterna returnerar på sidan [vanliga felkoder för REST-API: et](https://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Kapacitetsmåtten visar en oväntat ökning i kapacitet lagringsanvändning
Om du ser plötsliga oväntade ändringar i kapacitetsförbrukning i ditt storage-konto, du kan undersöka skälen genom att först granska din tillgänglighetsmått; Exempelvis kan en ökning i antalet misslyckade förfrågningar kan leda till en ökning av blob-lagring som du använder som programspecifika rensningsåtgärder som du kanske har förväntas att frigöra utrymme inte kanske fungerar som förväntat (till exempel eftersom SAS-token som används för att frigöra utrymme har upphört att gälla).

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>Problemet uppstår från att använda storage-emulatorn för utveckling eller testning
Du vanligtvis använda lagringsemulatorn under utvecklingen och testa för att undvika behovet av ett Azure storage-konto. Vanliga problem som kan uppstå när du använder lagringsemulatorn finns:

* [Funktionen ”X” fungerar inte i storage-emulatorn]
* [Fel ”värdet för en av HTTP-huvuden är inte i rätt format” när du använder lagringsemulatorn]
* [Kör lagringsemulatorn kräver administrativa privilegier]

#### <a name="feature-X-is-not-working"></a>Funktionen ”X” fungerar inte i storage-emulatorn
Storage-emulatorn stöder inte alla funktioner i Azure storage-tjänster, till exempel tjänsten file. Mer information finns i [Använd Azure Storage-emulatorn för utveckling och testning](storage-use-emulator.md).

För dessa funktioner som storage-emulatorn inte har stöd för, använder du Azure storage-tjänst i molnet.

#### <a name="error-HTTP-header-not-correct-format"></a>Fel ”värdet för en av HTTP-huvuden är inte i rätt format” när du använder lagringsemulatorn
Du testar dina program som använder Storage-klientbiblioteket mot lokal lagring emulatorn och metoden anrop som **CreateIfNotExists** misslyckas med felmeddelandet ”värdet för en HTTP-huvuden inte är i rätt format ”. Detta anger att versionen av storage-emulatorn som du använder inte stöder versionen av storage-klientbiblioteket som du använder. Storage-klientbiblioteket lägger till rubriken **x-ms-version** alla begäranden som gör det. Om storage-emulatorn är okänt värde i den **x-ms-version** rubrik som är det avvisar begäran.

Du kan använda Storage Biblioteksklient loggarna för att visa värdet för den **x-ms-version-huvud** som skickas. Du kan också se värdet för den **x-ms-version-huvud** om du använder Fiddler för att spåra begäranden från klientprogrammet.

Det här scenariot inträffar vanligtvis om du installerar och använder den senaste versionen av Storage-klientbiblioteket utan uppdatering av storage-emulatorn. Du bör installera den senaste versionen av storage-emulatorn eller använda molnlagring i stället för emulatorn för utveckling och test.

#### <a name="storage-emulator-requires-administrative-privileges"></a>Kör lagringsemulatorn kräver administrativa privilegier
Du tillfrågas om administratörsautentiseringsuppgifter när du kör storage-emulatorn. Det här inträffar bara när du initieras av storage-emulatorn för första gången. När du har initierats av storage-emulatorn kan behöver du inte administrativ behörighet att köra den igen.

Mer information finns i [Använd Azure Storage-emulatorn för utveckling och testning](storage-use-emulator.md). Du kan också initiera lagringsemulatorn i Visual Studio, som även kräver administrativa privilegier.

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Det uppstår några problem med att installera Azure SDK för .NET
När du försöker installera SDK: N misslyckas försök att installera storage-emulatorn på den lokala datorn. Installationsloggen innehåller något av följande meddelanden:

* CAQuietExec:  Fel: Det går inte att komma åt SQL-instansen
* CAQuietExec:  Fel: Det går inte att skapa databas

Orsaken är ett problem med befintlig LocalDB-installation. Som standard använder lagringsemulatorn LocalDB kan bevara data när den simulerar Azure storage-tjänster. Du kan återställa LocalDB-instansen genom att köra följande kommandon i ett kommandotolksfönster innan du försöker installera SDK.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

Den **ta bort** kommandot tar bort eventuella gamla databasfiler från en tidigare installation av storage-emulatorn.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Du har ett annat problem med ett storage-tjänsten
Om föregående felsökningsavsnitt inte inkluderar det problem du har med en lagringstjänst, bör du anta följande metod för att diagnostisera och felsöka ditt problem.

* Kontrollera din mått för att se om det finns ändringar från din förväntade baslinjen beteende. Du kanske kan avgöra om problemet är tillfälligt eller permanent och vilka lagringsåtgärder problemet påverkar från mått.
* Du kan använda informationen om mått på hur du söker serversidan loggdata för mer detaljerad information om eventuella fel som uppstår. Den här informationen kan hjälpa dig att felsöka och lösa problemet.
* Om informationen i loggarna för serversidan inte är tillräckligt för att felsöka problemet har kan du använda Lagringsklientbiblioteket klientside-loggar för att undersöka beteendet för dina klientprogram och verktyg som Fiddler, Wireshark och Microsoft Analysverktyg för att undersöka ditt nätverk.

Mer information om hur du använder Fiddler finns i ”[bilaga 1: Med hjälp av Fiddler för att avbilda trafik över HTTP och HTTPS]”.

Mer information om hur du använder Wireshark finns i ”[bilaga 2: Med hjälp av Wireshark för att avbilda nätverkstrafik]”.

Mer information om hur du använder Microsoft Message Analyzer finns i ”[tillägg 3: Använder Microsoft Message Analyzer för att avbilda nätverkstrafik]”.

## <a name="appendices"></a>Tilläggen
Tilläggen beskrivs flera verktyg som kan vara användbart när du diagnostiserar och felsökning av problem med Azure Storage (och andra tjänster). Dessa verktyg är inte en del av Azure Storage och vissa är produkter från tredje part. Därför måste de verktyg som beskrivs i de här tilläggen inte omfattas av alla supportavtal som du kan ha med Microsoft Azure eller Azure Storage och därför som en del av din utvärdering du bör undersöka alternativen licensiering och support tillgänglig från den leverantörer av dessa verktyg.

### <a name="appendix-1"></a>Bilaga 1: Med hjälp av Fiddler för att avbilda trafik över HTTP och HTTPS
[Fiddler](https://www.telerik.com/fiddler) är användbart för att analysera HTTP och HTTPS-trafik mellan klientprogrammet och Azure storage-tjänst som du använder.

> [!NOTE]
> Fiddler kan avkoda HTTPS-trafik. Läs i dokumentationen för Fiddler noggrant för att förstå hur detta sker och förstå säkerhetsriskerna.
>
>

Den här bilagan innehåller en kort genomgång av hur du konfigurerar Fiddler för att fånga in trafik mellan den lokala datorn där du har installerat Fiddler och Azure storage-tjänster.

När du har öppnat Fiddler, börjar den samla in HTTP och HTTPS-trafik på den lokala datorn. Här följer några användbara kommandon för att styra Fiddler:

* Stoppa och starta fånga in trafik. På huvudmenyn går du till **filen** och klicka sedan på **Capture Traffic** att växla samla in och inaktivera.
* Spara avbildade trafikdata. På huvudmenyn går du till **filen**, klickar du på **spara**, och klicka sedan på **alla sessioner**: Detta gör att du kan spara trafiken i en Session arkivfil. Du kan läsa in ett Session-Arkiv senare för analys eller skicka det om en begäran om att Microsoft-supporten.

Om du vill begränsa mängden trafik som Fiddler samlar in, kan du använda filter som du konfigurerar i den **filter** fliken. Följande skärmbild visar ett filter som samlar in endast trafik som skickas till den **contosoemaildist.table.core.windows.net** slutpunkt för lagring:

![][5]

### <a name="appendix-2"></a>Bilaga 2: Med hjälp av Wireshark för att avbilda nätverkstrafik
[Wireshark](https://www.wireshark.org/) är en nätverksprotokollanalysator där du kan visa detaljerad paketinformationen för en mängd olika nätverksprotokoll.

Följande procedur visar hur du kan få detaljerad paketinformationen för trafik från den lokala datorn där du installerade Wireshark till tabelltjänsten igen i ditt Azure storage-konto.

1. Starta Wireshark på den lokala datorn.
2. I den **starta** väljer du det lokala nätverket eller flera gränssnitt som är anslutna till internet.
3. Klicka på **avbilda alternativ**.
4. Lägg till ett filter för att den **Capture Filter** textrutan. Till exempel **värd contosoemaildist.table.core.windows.net** konfigurerar Wireshark för att samla in endast paket som skickas till eller från table service-slutpunkt i den **contosoemaildist** storage-konto. Kolla in den [fullständig lista över avbilda filter](https://wiki.wireshark.org/CaptureFilters).

   ![][6]
5. Klicka på **Starta**. Wireshark avbildar nu alla paketen skickar till eller från table service-slutpunkt som du använder klientprogrammet på den lokala datorn.
6. När du är klar, klicka på huvudmenyn **avbilda** och sedan **stoppa**.
7. Om du vill spara insamlade data i en Wireshark avbilda fil på huvudmenyn klickar du på **filen** och sedan **spara**.

WireShark att fokusera på eventuella fel som finns i den **packetlist** fönster. Du kan också använda den **Expert Info** fönstret (klicka på **analysera**, sedan **Expert Info**) att visa en sammanfattning av fel och varningar.

![][7]

Du kan också välja att visa TCP-data som programnivån ser det genom att högerklicka på TCP-data och välja **följer TCP Stream**. Detta är användbart om du har hämtat dina dump utan ett filter för avbildning. Mer information finns i [följande TCP-strömmar](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [!NOTE]
> Mer information om hur du använder Wireshark finns i den [Wireshark användarhandboken](https://www.wireshark.org/docs/wsug_html_chunked).
>
>

### <a name="appendix-3"></a>Tillägg 3: Använder Microsoft Message Analyzer för att avbilda nätverkstrafik
Du kan använda Microsoft Message Analyzer för att avbilda HTTP och HTTPS-trafik på ett liknande sätt att Fiddler och avbilda nätverkstrafik på ett liknande sätt att Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Konfigurera spårning av en webbsession med hjälp av Microsoft Message Analyzer
Konfigurera en spårning webbsession för HTTP och HTTPS-trafik med hjälp av Microsoft Message Analyzer, kör programmet Microsoft Message Analyzer och sedan på den **filen** -menyn klickar du på **Capture/Trace**. Välj i listan över tillgängliga trace scenarier, **webbproxy**. I den **scenariot spårningskonfigurationen** panelen den **HostnameFilter** textrutan Lägg till namnen på dina slutpunkter för lagring (du kan söka efter dessa namn i den [Azure-portalen](https://portal.azure.com)). Om namnet på ditt Azure storage-konto är till exempel **contosodata**, bör du lägga till följande för att den **HostnameFilter** textrutan:

```
contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
```

> [!NOTE]
> Ett blanksteg avgränsar värdnamnen.
>
>

När du är redo att börja samla in spårningsdata, klickar du på den **Start With** knappen.

Mer information om Microsoft Message Analyzer **webbproxy** spåra, se [Microsoft – PEF-WebProxy providern](https://technet.microsoft.com/library/jj674814.aspx).

Inbyggt **webbproxy** spår i Microsoft Message Analyzer är baserat på Fiddler, den kan samla in klientsidan HTTPS-trafik och visa okrypterade HTTPS-meddelanden. Den **webbproxy** spåra fungerar genom att konfigurera en lokal proxy för alla HTTP och HTTPS-trafik som ger åtkomst till okrypterade meddelanden.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnostisera nätverksproblem som använder Microsoft Message Analyzer
Förutom att använda Microsoft Message Analyzer **webbproxy** spåra för att samla in information om HTTP/HTTPs-trafik mellan klientprogrammet och storage-tjänsten, du kan också använda inbyggt **lokala länknivå**  spårning för att samla in nätverksinformation för paketet. I det här kan du samla in data som liknar den som du kan samla in med Wireshark och diagnostisera nätverksproblem som avbrutna paket.

Följande skärmbild visar ett exempel **lokala länknivå** spårningen med några **endast i informationssyfte** meddelanden i den **DiagnosisTypes** kolumn. När du klickar på en ikon i den **DiagnosisTypes** kolumnen visar information om meddelandet. I det här exemplet skickats meddelande #305 om servern eftersom den inte fick en bekräftelse från klienten:

![][9]

När du skapar spårningssessionen i Microsoft Message Analyzer, kan du ange filter för att minska bruset i spårningen. På den **avbilda / spåra** sidan där du kan definiera spårning, klicka på den **konfigurera** länka bredvid **Microsoft-Windows-NDIS-PacketCapture**. Följande skärmbild visar en konfiguration som filtrerar TCP-trafik för IP-adresserna för tre lagringstjänster:

![][10]

Läs mer om Microsoft Message Analyzer lokala länknivå spårningen [Provider för Microsoft-PEF-NDIS-PacketCapture](https://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4"></a>Tillägg 4: Använda Excel för att visa mätvärden och loggdata
Många verktyg kan du ladda ned Storage Metrics data från Azure table storage i en avgränsad format som gör det enkelt att läsa in data till Excel för visning och analys. Storage in data från Azure blob storage finns redan i en avgränsad format som du kan läsa in i Excel. Men du måste lägga till lämpliga kolumnrubrikerna i informationen på [Storage Analytics loggformat](https://msdn.microsoft.com/library/azure/hh343259.aspx) och [Schema över Måttabeller i Storage Analytics](https://msdn.microsoft.com/library/azure/hh343264.aspx).

Importera dina data för loggning av lagring till Excel när du har hämtat från blob storage:

* På den **Data** -menyn klickar du på **från Text**.
* Bläddra till den loggfil som du vill visa och klicka på **Import**.
* I steg 1 i den **Text i guiden Importera**väljer **avgränsad**.

I steg 1 i den **Text i guiden Importera**väljer **semikolon** som endast avgränsare och välj double-offert som den **textavgränsare**. Klicka sedan på **Slutför** och välj var du vill placera data i din arbetsbok.

### <a name="appendix-5"></a>Tillägg 5: Övervakning med Application Insights för Azure DevOps
Du kan också använda funktionen Application Insights för Azure DevOps som en del av din tillgänglighetsövervakning av prestanda och. Det här verktyget kan:

* Kontrollera att din webbtjänst är tillgänglig och svarar. Om din app är en webbplats eller en app för enheter som använder en webbtjänst, kan den Testa URL några minuters mellanrum från platser runtom i världen och att du vet om det finns ett problem.
* Diagnostisera snabbt eventuella problem med prestanda eller undantag i din webbtjänst. Lär dig om CPU- eller andra resurser är sträcks, få stackspårningar från undantag och enkelt söka igenom loggspårningar. Om appens prestanda sjunker under rimliga gränser, kan Microsoft skicka ett e-postmeddelande. Du kan övervaka både .NET och Java-webbtjänster.

Du hittar mer information på [vad är Application Insights](../../azure-monitor/app/app-insights-overview.md).

## <a name="next-steps"></a>Nästa steg

Mer information om analytics i Azure Storage finns i följande källor:

* [Övervaka ett lagringskonto i Azure portal](storage-monitor-storage-account.md)
* [Lagringsanalys](storage-analytics.md)
* [Mätvärden i Storage analytics](storage-analytics-metrics.md)
* [Schema över Måttabeller i Storage analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema.md)
* [Storage analytics-loggar](storage-analytics-logging.md)
* [Loggformatet för Storage analytics](/rest/api/storageservices/storage-analytics-log-format.md)

<!--Anchors-->
[Introduktion]: #introduction
[Hur den här guiden är strukturerad]: #how-this-guide-is-organized

[Övervakning av storage-tjänst]: #monitoring-your-storage-service
[Övervakar tjänsternas hälsa]: #monitoring-service-health
[Övervakningskapacitet]: #monitoring-capacity
[Övervakar tillgänglighet]: #monitoring-availability
[Övervaka prestanda]: #monitoring-performance

[Diagnostisera problem med lagring]: #diagnosing-storage-issues
[Tjänsten hälsoproblem]: #service-health-issues
[Prestandaproblem]: #performance-issues
[Diagnostisera fel]: #diagnosing-errors
[Storage-emulatorn problem]: #storage-emulator-issues
[Loggningsverktyg för lagring]: #storage-logging-tools
[Med hjälp av verktyg för loggning]: #using-network-logging-tools

[Slutpunkt till slutpunkt-spårning]: #end-to-end-tracing
[Korrelera loggdata]: #correlating-log-data
[ID för klientbegäran]: #client-request-id
[Server-begäran-ID]: #server-request-id
[Timestamps]: #timestamps

[Felsökningsanvisningar]: #troubleshooting-guidance
[Mätningar visar ett högt värde för AverageE2ELatency och ett lågt värde för AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Mätningar visar låga värden för AverageE2ELatency och AverageServerLatency, men klienten har ändå långa svarstider]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Mätningar visar ett högt värde för AverageServerLatency]: #metrics-show-high-AverageServerLatency
[Du upplever oväntade fördröjningar i en köad meddelandeleverans]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Mätningar visar en ökning i PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Tillfällig ökning i PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Permanent ökning i PercentThrottlingError fel]: #permanent-increase-in-PercentThrottlingError
[Mätningar visar en ökning i PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Mätningar visar en ökning i PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[Klienten får HTTP 403-meddelanden (Förbjudet)]: #the-client-is-receiving-403-messages
[Klienten får HTTP 404-meddelanden (Hittades inte)]: #the-client-is-receiving-404-messages
[Klienten eller en annan process har tagit bort objektet]: #client-previously-deleted-the-object
[Ett problem med auktorisering delade signatur åtkomst (SAS)]: #SAS-authorization-issue
[Klientens JavaScript-kod har inte behörighet att komma åt objektet]: #JavaScript-code-does-not-have-permission
[Nätverksfel]: #network-failure
[Klienten tar emot HTTP 409 (konflikt) meddelanden]: #the-client-is-receiving-409-messages

[Mätvärdena visar låg PercentSuccess eller analytics loggposter innehålla åtgärder med transaktionsstatus för ClientOtherErrors]: #metrics-show-low-percent-success
[Kapacitetsdata visar en oväntad ökning i kapacitetsförbrukning för lagring]: #capacity-metrics-show-an-unexpected-increase
[Problemet uppstår från med hjälp av storage-emulatorn för utveckling eller testning]: #your-issue-arises-from-using-the-storage-emulator
[Funktionen ”X” fungerar inte i storage-emulatorn]: #feature-X-is-not-working
[Fel ”värdet för en av HTTP-huvuden är inte i rätt format” när du använder lagringsemulatorn]: #error-HTTP-header-not-correct-format
[Kör lagringsemulatorn kräver administrativa privilegier]: #storage-emulator-requires-administrative-privileges
[Det uppstår problem med att installera Azure SDK för .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Du har ett annat problem med en storage-tjänst]: #you-have-a-different-issue-with-a-storage-service

[Tilläggen]: #appendices
[Bilaga 1: Med hjälp av Fiddler för att avbilda trafik över HTTP och HTTPS]: #appendix-1
[Bilaga 2: Med hjälp av Wireshark för att avbilda nätverkstrafik]: #appendix-2
[Tillägg 3: Använder Microsoft Message Analyzer för att avbilda nätverkstrafik]: #appendix-3
[Tillägg 4: Använda Excel för att visa mätvärden och loggdata]: #appendix-4
[Tillägg 5: Övervakning med Application Insights för Azure DevOps]: #appendix-5

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
