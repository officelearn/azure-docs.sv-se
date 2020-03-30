---
title: Övervaka, diagnostisera och felsöka Azure Storage | Microsoft-dokument
description: Använd funktioner som lagringsanalys, loggning på klientsidan och andra verktyg från tredje part för att identifiera, diagnostisera och felsöka Azure Storage-relaterade problem.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 3d5f3ade3ef3b79ddb3996b5bf2d609b11aff8a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255969"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Övervaka, diagnostisera och felsök Microsoft Azure Storage
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Översikt
Diagnostisera och felsöka problem i ett distribuerat program som finns i en molnmiljö kan vara mer komplext än i traditionella miljöer. Program kan distribueras i en PaaS- eller IaaS-infrastruktur, lokalt, på en mobil enhet eller i någon kombination av dessa miljöer. Vanligtvis kan programmets nätverkstrafik passera offentliga och privata nätverk och ditt program kan använda flera lagringstekniker, till exempel Microsoft Azure Storage Tables, Blobbar, Queues eller Files utöver andra datalager, till exempel relationella och dokumentdatabaser.

För att hantera sådana program framgångsrikt bör du övervaka dem proaktivt och förstå hur du diagnostiserar och felsöker alla aspekter av dem och deras beroende teknik. Som användare av Azure Storage-tjänster bör du kontinuerligt övervaka de lagringstjänster som ditt program använder för oväntade ändringar i beteende (t.ex. långsammare svarstider än vanligt) och använda loggning för att samla in mer detaljerade data och analysera ett problem i Djup. Diagnostikinformationen som du får från både övervakning och loggning hjälper dig att fastställa orsaken till problemet som programmet påträffades. Sedan kan du felsöka problemet och bestämma lämpliga åtgärder du kan vidta för att åtgärda det. Azure Storage är en grundläggande Azure-tjänst och utgör en viktig del av de flesta lösningar som kunder distribuerar till Azure-infrastrukturen. Azure Storage innehåller funktioner för att förenkla övervakning, diagnos och felsökning av lagringsproblem i dina molnbaserade program.

> [!NOTE]
> Azure Files stöder inte loggning just nu.
>

En praktisk guide till felsökning från på för alla i Azure Storage-program finns i Felsökning från [slut till på sluten tid med Azure Storage Metrics and Logging, AzCopy och Message Analyzer](../storage-e2e-troubleshooting.md).

* [Introduktion]
  * [Hur den här guiden är organiserad]
* [Övervaka din lagringstjänst]
  * [Övervakning av tjänstens hälsa]
  * [Övervakningskapacitet]
  * [Övervaka tillgänglighet]
  * [Övervaka prestanda]
* [Diagnostisera lagringsproblem]
  * [Hälsofrågor för tjänster]
  * [Prestandaproblem]
  * [Diagnostisera fel]
  * [Problem med lagringsemulatorn]
  * [Verktyg för lagringsloggning]
  * [Använda verktyg för nätverksloggning]
* [Spårning från på slut- till-slut]
  * [Korrelera loggdata]
  * [ID för klientförfrågan]
  * [ID för serverbegäran]
  * [Tidsstämplar]
* [Felsökningsvägledning]
  * [Mätningar visar ett högt värde för AverageE2ELatency och ett lågt värde för AverageServerLatency]
  * [Mätningar visar låga värden för AverageE2ELatency och AverageServerLatency, men klienten har ändå långa svarstider]
  * [Mätningar visar ett högt värde för AverageServerLatency]
  * [Du upplever oväntade fördröjningar i en köad meddelandeleverans]
  * [Mätningar visar en ökning i PercentThrottlingError]
  * [Mätningar visar en ökning i PercentTimeoutError]
  * [Mätningar visar en ökning i PercentNetworkError]
  * [Klienten får HTTP 403-meddelanden (Förbjudet)]
  * [Klienten får HTTP 404-meddelanden (Hittades inte)]
  * [Klienten får HTTP 409-meddelanden (Konflikt)]
  * [Mått visar låga procentkonsuccess eller analysloggposter har operationer med transaktionsstatus för ClientOtherErrors]
  * [Kapacitetsmått visar en oväntad ökning av användningen av lagringskapacitet]
  * [Ditt problem uppstår från att använda lagringsemulatorn för utveckling eller test]
  * [Du stöter på problem med att installera Azure SDK för .NET]
  * [Du har ett annat problem med en lagringstjänst]
  * [Felsöka virtuella hårddiskar på virtuella Windows-datorer](../../virtual-machines/windows/troubleshoot-vhds.md)   
  * [Felsöka virtuella hårddiskar på virtuella Linux-datorer](../../virtual-machines/linux/troubleshoot-vhds.md)
  * [Felsöka Problem med Azure Files med Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Felsöka Problem med Azure Files med Linux](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Bilagor]
  * [Tillägg 1: Använda Fiddler för att fånga HTTP- och HTTPS-trafik]
  * [Tillägg 2: Använda Wireshark för att fånga nätverkstrafik]
  * [Tillägg 3: Använda Microsoft Message Analyzer för att fånga nätverkstrafik]
  * [Tillägg 4: Använda Excel för att visa mått och logga data]
  * [Tillägg 5: Övervakning med programinsikter för Azure DevOps]

## <a name="introduction"></a><a name="introduction"></a>Introduktion
Den här guiden visar hur du använder funktioner som Azure Storage Analytics, klientbaserad loggning i Azure Storage Client Library och andra verktyg från tredje part för att identifiera, diagnostisera och felsöka Azure Storage-relaterade problem.

![][1]

Den här guiden är endast avsedd att läsas av utvecklare av onlinetjänster som använder Azure Storage Services och IT-proffs som ansvarar för att hantera sådana onlinetjänster. Målen för den här guiden är:

* För att hjälpa dig att upprätthålla hälso- och prestanda för dina Azure Storage-konton.
* För att ge dig nödvändiga processer och verktyg som hjälper dig att avgöra om ett problem eller problem i ett program gäller Azure Storage.
* För att ge dig användbar vägledning för att lösa problem relaterade till Azure Storage.

### <a name="how-this-guide-is-organized"></a><a name="how-this-guide-is-organized"></a>Hur den här guiden är organiserad
I avsnittet "[Övervakning av din lagringstjänst]" beskrivs hur du övervakar hälsotillståndet och prestanda för dina Azure Storage-tjänster med hjälp av Azure Storage Analytics Metrics (Lagringsmått).

I avsnittet "[Diagnostisera lagringsproblem]" beskrivs hur du diagnostiserar problem med Azure Storage Analytics Loggning (Lagringsloggning). Den beskriver också hur du aktiverar loggning på klientsidan med hjälp av anläggningarna i ett av klientbiblioteken, till exempel Storage Client Library för .NET eller Azure SDK för Java.

Avsnittet "[End-to-end tracing]" beskriver hur du kan korrelera informationen i olika loggfiler och mätdata.

Avsnittet "[Felsökningsvägledning]" innehåller felsökningsvägledning för några av de vanliga lagringsrelaterade problem som kan uppstå.

Den "[Tillägg]" innehåller information om hur du använder andra verktyg som Wireshark och Netmon för att analysera nätverkspaketdata, Fiddler för att analysera HTTP / HTTPS-meddelanden och Microsoft Message Analyzer för korrelera loggdata.

## <a name="monitoring-your-storage-service"></a><a name="monitoring-your-storage-service"></a>Övervaka din lagringstjänst
Om du är bekant med Windows prestandaövervakning kan du tänka dig lagringsmått som en Azure Storage-motsvarighet till Windows Performance Monitor-räknare. I Lagringsmått hittar du en omfattande uppsättning mått (räknare i Windows Performance Monitor-terminologi) till exempel tjänsttillgänglighet, totalt antal begäranden till tjänsten eller procentandel av lyckade begäranden till tjänsten. En fullständig lista över tillgängliga mått finns i [Tabellschema för lagringsanalysmått](https://msdn.microsoft.com/library/azure/hh343264.aspx). Du kan ange om du vill att lagringstjänsten ska samla in och aggregera mått varje timme eller varje minut. Mer information om hur du aktiverar mått och övervakar dina lagringskonton finns i [Aktivera lagringsmått och visa måttdata](https://go.microsoft.com/fwlink/?LinkId=510865).

Du kan välja vilka timmått som du vill visa i [Azure-portalen](https://portal.azure.com) och konfigurera regler som meddelar administratörer via e-post när ett timmått överskrider ett visst tröskelvärde. Mer information finns i [Ta emot aviseringar](/azure/monitoring-and-diagnostics/monitoring-overview-alerts).

Vi rekommenderar att du granskar [Azure Monitor for Storage](../../azure-monitor/insights/storage-insights-overview.md) (förhandsversion). Det är en funktion i Azure Monitor som erbjuder omfattande övervakning av dina Azure Storage-konton genom att leverera en enhetlig vy över dina Azure Storage-tjänsters prestanda, kapacitet och tillgänglighet. Det kräver inte att du aktiverar eller konfigurerar något, och du kan omedelbart visa dessa mått från de fördefinierade interaktiva diagrammen och andra visualiseringar som ingår.

Lagringstjänsten samlar in mått med hjälp av bästa möjliga ansträngningar, men kanske inte registrerar alla lagringsoperationer.

I Azure-portalen kan du visa mått som tillgänglighet, totala begäranden och genomsnittliga svarstidsnummer för ett lagringskonto. En meddelanderegel har också ställts in för att varna en administratör om tillgängligheten sjunker under en viss nivå. Från att visa dessa data är ett möjligt område för undersökning att andelen lyckades med tabelltjänsten under 100 % (för mer information finns i avsnittet "[Mått visar låg percentsuccess eller analysloggposter har operationer med transaktionsstatus för ClientOtherErrors]").

Du bör kontinuerligt övervaka dina Azure-program för att säkerställa att de är felfria och fungerar som förväntat av:

* Upprätta några baslinjemått för program som gör att du kan jämföra aktuella data och identifiera eventuella betydande ändringar i beteendet för Azure-lagring och ditt program. Värdena för baslinjemåtten är i många fall programspecifika och du bör fastställa dem när du testar programmet.
* Registrera minutmått och använda dem för att aktivt övervaka oväntade fel och avvikelser, till exempel toppar i felantal eller begäranden.
* Registrera timmått och använda dem för att övervaka genomsnittliga värden som genomsnittliga felantal och begäranden.
* Undersöka potentiella problem med hjälp av diagnostikverktyg som diskuteras senare i avsnittet "[Diagnostisera lagringsproblem .",]

Diagrammen i följande bild illustrerar hur medelvärdet som inträffar för timmått kan dölja toppar i aktivitet. Timmåtten verkar visa en stadig ökning av begäranden, medan minutmåtten visar de fluktuationer som verkligen äger rum.

![][3]

Resten av det här avsnittet beskriver vilka mått du bör övervaka och varför.

### <a name="monitoring-service-health"></a><a name="monitoring-service-health"></a>Övervakning av tjänstens hälsa
Du kan använda [Azure-portalen](https://portal.azure.com) för att visa hälsotillståndet för lagringstjänsten (och andra Azure-tjänster) i alla Azure-regioner runt om i världen. Övervakning gör att du omedelbart kan se om ett problem utanför kontrollen påverkar lagringstjänsten i den region som du använder för ditt program.

[Azure-portalen](https://portal.azure.com) kan också tillhandahålla meddelanden om incidenter som påverkar de olika Azure-tjänsterna.
Den här informationen var tidigare tillgänglig, tillsammans med historiska data, på [Azure Service Dashboard](https://status.azure.com).

[Azure-portalen](https://portal.azure.com) samlar in hälsoinformation inifrån Azure-datacenter (inside-out-övervakning), men du kan också överväga att använda en extern metod för att generera syntetiska transaktioner som regelbundet kommer åt ditt Azure-värdbaserade webbprogram från flera platser. De tjänster som erbjuds av [Dynatrace](https://www.dynatrace.com/en/synthetic-monitoring) och Application Insights för Azure DevOps är exempel på den här metoden. Mer information om Programinsikter för Azure DevOps finns i bilagan "[Bilaga 5: Övervakning med Programinsikter för Azure DevOps](#appendix-5)."

### <a name="monitoring-capacity"></a><a name="monitoring-capacity"></a>Övervakningskapacitet
Lagringsmått lagrar endast kapacitetsmått för blob-tjänsten eftersom blobbar vanligtvis står för den största andelen lagrade data (i skrivande stund är det inte möjligt att använda lagringsmått för att övervaka kapaciteten för dina tabeller och köer). Du hittar dessa data i **tabellen $MetricsCapacityBlob** om du har aktiverat övervakning för Blob-tjänsten. Lagringsmått registrerar dessa data en gång per dag och du kan använda värdet för **RowKey** för att avgöra om raden innehåller en entitet som relaterar till användardata **(värdedata)** eller analysdata **(värdeanalys**). Varje lagrad entitet innehåller information om hur mycket lagringsutrymme som används (**Kapacitet** mätt i byte) och det aktuella antalet behållare (**ContainerCount**) och blobbar (**ObjectCount**) som används i lagringskontot. Mer information om kapacitetsmått som lagras i **tabellen $MetricsCapacityBlob** finns i [Tabellschema för lagringsanalysmått](https://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> Du bör övervaka dessa värden för en tidig varning om att du närmar dig kapacitetsgränserna för ditt lagringskonto. I Azure-portalen kan du lägga till varningsregler för att meddela dig om den sammanlagda lagringsanvändningen överskrider eller faller under tröskelvärden som du anger.
>
>

Mer information om hur du uppskattar storleken på olika lagringsobjekt, till exempel blobbar, finns i blogginlägget [Understanding Azure Storage Billing – Bandwidth, Transactions och Capacity](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

### <a name="monitoring-availability"></a><a name="monitoring-availability"></a>Övervaka tillgänglighet
Du bör övervaka tillgängligheten för lagringstjänsterna i ditt lagringskonto genom att övervaka värdet i kolumnen **Tillgänglighet** i tabellerna för tim- eller minutmått – **$MetricsHourPrimaryTransactionsBlob** **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$ MetricsCapacityBlob**. Kolumnen **Tillgänglighet** innehåller ett procentvärde som anger tjänstens tillgänglighet eller API-åtgärden som representeras av raden **(RowKey** visar om raden innehåller mått för tjänsten som helhet eller för en viss API-åtgärd).

Ett värde som är mindre än 100 % anger att vissa lagringsbegäranden misslyckas. Du kan se varför de misslyckas genom att undersöka de andra kolumnerna i måttdata som visar antalet begäranden med olika feltyper, till exempel **ServerTimeoutError**. Du bör förvänta dig att **tillgänglighet tillfälligt** understiger 100 % av skäl som tillfälliga tidsutgångar på servern medan tjänsten flyttar partitioner till bättre begäran om belastningsbalans. Logiken för återförsök i klientprogrammet bör hantera sådana intermittenta villkor. I artikeln [Storage Analytics Logged Operations and Status Messages](https://msdn.microsoft.com/library/azure/hh343260.aspx) visas de transaktionstyper som lagringsmått innehåller i beräkningen **av tillgänglighet.**

I [Azure-portalen](https://portal.azure.com)kan du lägga till varningsregler för att meddela dig om **tillgänglighet** för en tjänst faller under ett tröskelvärde som du anger.

Avsnittet "[Felsökningsvägledning]" i den här guiden beskriver några vanliga problem med lagringstjänster som rör tillgänglighet.

### <a name="monitoring-performance"></a><a name="monitoring-performance"></a>Övervaka prestanda
Om du vill övervaka lagringstjänsternas prestanda kan du använda följande mått från tabellerna för tim- och minutmått.

* Värdena i kolumnerna **AverageE2ELatency** och **AverageServerLatency** visar den genomsnittliga tiden som lagringstjänsten eller API-åtgärdstypen tar för att bearbeta begäranden. **AverageE2ELatency** är ett mått på svarstid från till på sluten tid som inkluderar den tid det tar att läsa begäran och skicka svaret utöver den tid det tar att bearbeta begäran (inkluderar därför nätverksfördröjning när begäran når lagringstjänsten). **AverageServerLatency** är ett mått på bara bearbetningstiden och utesluter därför alla nätverksfördröjningar som är relaterade till kommunikation med klienten. Se avsnittet "[Metrics show high AverageE2ELatency and low AverageServerLatency]" later in this guide for a discussion of why there might be a significant difference between these two values.
* Värdena i kolumnerna **TotalIngress** och **TotalEgress** visar den totala mängden data, i byte, som kommer in till och går ut ur lagringstjänsten eller via en specifik API-åtgärdstyp.
* Värdena i kolumnen **TotalRequests** visar det totala antalet begäranden som lagringstjänsten för API-åtgärden tar emot. **TotalRequests** är det totala antalet begäranden som lagringstjänsten tar emot.

Vanligtvis övervakar du oväntade ändringar i något av dessa värden som en indikator på att du har ett problem som kräver undersökning.

I [Azure-portalen](https://portal.azure.com)kan du lägga till aviseringsregler för att meddela dig om något av prestandamåtten för den här tjänsten faller under eller överskrider ett tröskelvärde som du anger.

Avsnittet "[Felsökningsvägledning]" i den här guiden beskriver några vanliga problem med lagringstjänster som rör prestanda.

## <a name="diagnosing-storage-issues"></a><a name="diagnosing-storage-issues"></a>Diagnostisera lagringsproblem
Det finns flera sätt som du kan bli medveten om ett problem eller problem i ditt program, inklusive:

* Ett stort fel som gör att programmet kraschar eller slutar fungera.
* Betydande ändringar från baslinjevärden i de mått som du övervakar enligt beskrivningen i föregående avsnitt "[Övervaka lagringstjänsten .",]
* Rapporter från användare av ditt program som någon viss åtgärd inte slutfördes som förväntat eller att vissa funktioner inte fungerar.
* Fel som genereras i ditt program som visas i loggfiler eller via någon annan meddelandemetod.

Vanligtvis kan problem relaterade till Azure-lagringstjänster delas in i en av fyra breda kategorier:

* Ditt program har ett prestandaproblem, antingen rapporterat av användarna eller som visas av ändringar i prestandamåtten.
* Det finns ett problem med Azure Storage-infrastrukturen i en eller flera regioner.
* Ditt program stöter på ett fel, antingen rapporteras av dina användare, eller avslöjas av en ökning av en av de fel antal mått du övervakar.
* Under utveckling och test kan du använda den lokala lagringsemulatorn; du kan stöta på vissa problem som specifikt rör användningen av lagringsemulatorn.

I följande avsnitt beskrivs de steg du bör följa för att diagnostisera och felsöka problem i var och en av dessa fyra kategorier. Avsnittet "[Felsökningsvägledning]" senare i den här guiden innehåller mer information om några vanliga problem som kan uppstå.

### <a name="service-health-issues"></a><a name="service-health-issues"></a>Hälsofrågor för tjänster
Hälsofrågor för tjänster ligger vanligtvis utanför kontrollen. [Azure-portalen](https://portal.azure.com) innehåller information om eventuella pågående problem med Azure-tjänster, inklusive lagringstjänster. Om du valde Read-Access Geo-Redundant Storage när du skapade ditt lagringskonto kan programmet tillfälligt växla till den skrivskyddade kopian på den sekundära platsen om dina data blir otillgängliga på den primära platsen. För att läsa från den sekundära måste ditt program kunna växla mellan att använda de primära och sekundära lagringsplatserna och kunna arbeta i ett läget nedsatt funktionalitet med skrivskyddade data. Azure Storage-klientbiblioteken kan du definiera en återförsöksprincip som kan läsa från sekundär lagring om en läsning från primär lagring misslyckas. Ditt program måste också vara medveten om att data på den sekundära platsen så småningom är konsekventa. Mer information finns i blogginlägget [Azure Storage Redundansalternativ och Read Access Geo Redundant Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a><a name="performance-issues"></a>Prestandaproblem
Upplevelsen av programprestanda kan vara högst subjektiv, särskilt från ett användarperspektiv. Därför är det viktigt att ha tillgång till jämförelsemått, så att du tydligt ser när det blir problem med prestandan. Många faktorer kan påverka prestanda för en Azure-lagringstjänst från klientprogramperspektivet. Dessa faktorer kan fungera i lagringstjänsten, i klienten eller i nätverksinfrastrukturen. Därför är det viktigt att ha en strategi för att identifiera resultatfrågans ursprung.

När du har identifierat den troliga platsen för orsaken till prestandaproblemet från måtten kan du sedan använda loggfilerna för att hitta detaljerad information för att diagnostisera och felsöka problemet ytterligare.

Avsnittet "[Felsökningsvägledning]" senare i den här guiden innehåller mer information om några vanliga prestandarelaterade problem som du kan stöta på.

### <a name="diagnosing-errors"></a><a name="diagnosing-errors"></a>Diagnostisera fel
Användare av ditt program kan meddela dig om fel som rapporterats av klientprogrammet. Lagringsmått registrerar också antal olika feltyper än dina lagringstjänster, till exempel **NetworkError,** **ClientTimeoutError**eller **AuthorizationError**. Lagringsmått endast registrerar antal olika feltyper, men du kan få mer information om enskilda begäranden genom att undersöka server-, klient-side och nätverksloggar. Vanligtvis ger HTTP-statuskoden som returneras av lagringstjänsten en indikation på varför begäran misslyckades.

> [!NOTE]
> Kom ihåg att du bör förvänta dig att se några intermittenta fel: till exempel fel på grund av tillfälliga nätverksförhållanden eller programfel.
>
>

Följande resurser är användbara om du vill förstå lagringsrelaterade statusar och felkoder:

* [Vanliga REST API-felkoder](https://msdn.microsoft.com/library/azure/dd179357.aspx)
* [Felkoder för Blob Service](https://msdn.microsoft.com/library/azure/dd179439.aspx)
* [Felkoder för kötjänst](https://msdn.microsoft.com/library/azure/dd179446.aspx)
* [Felkoder för tabelltjänst](https://msdn.microsoft.com/library/azure/dd179438.aspx)
* [Felkoder för filtjänst](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a><a name="storage-emulator-issues"></a>Problem med lagringsemulatorn
Azure SDK innehåller en lagringsemmulator som du kan köra på en utvecklingsarbetsstation. Den här emulatorn simulerar det mesta av problemet med Azure-lagringstjänster och är användbar under utveckling och testning, så att du kan köra program som använder Azure-lagringstjänster utan att behöva en Azure-prenumeration och ett Azure-lagringskonto.

Avsnittet "[Felsökningsvägledning]" i den här guiden beskriver några vanliga problem som uppstått med lagringsemmulatorn.

### <a name="storage-logging-tools"></a><a name="storage-logging-tools"></a>Verktyg för lagringsloggning
Lagringsloggning ger loggning på serversidan av lagringsbegäranden i ditt Azure-lagringskonto. Mer information om hur du aktiverar loggning och åtkomst till loggdata på serversidan finns i [Aktivera loggning och åtkomst till loggdata](https://go.microsoft.com/fwlink/?LinkId=510867).

Med Storage Client Library for .NET kan du samla in loggdata på klientsidan som relaterar till lagringsåtgärder som utförs av ditt program. Mer information finns i artikeln om [klientloggning med .NET Storage Client Library](https://go.microsoft.com/fwlink/?LinkId=510868).

> [!NOTE]
> Under vissa omständigheter (t.ex. sas-auktoriseringsfel) kan en användare rapportera ett fel som du inte kan hitta några begärandedata för i lagringsloggarna på serversidan. Du kan använda loggningsfunktionerna i lagringsklientbiblioteket för att undersöka om orsaken till problemet finns i klienten eller använda nätverksövervakningsverktyg för att undersöka nätverket.
>
>

### <a name="using-network-logging-tools"></a><a name="using-network-logging-tools"></a>Använda verktyg för nätverksloggning
Du kan samla in trafiken mellan klienten och servern för att ge detaljerad information om de data som klienten och servern utbyter och de underliggande nätverksvillkoren. Användbara verktyg för nätverksloggning är:

* [Fiddler](https://www.telerik.com/fiddler) är en gratis webbfelsökning proxy som gör att du kan undersöka rubriker och nyttolastdata för HTTP och HTTPS begäran och svar meddelanden. Mer information finns i [tillägg 1: Använda Fiddler för att samla in HTTP- och HTTPS-trafik](#appendix-1).
* [Microsoft Network Monitor (Netmon)](https://www.microsoft.com/download/details.aspx?id=4865) och [Wireshark](https://www.wireshark.org/) är kostnadsfria nätverksprotokollanalysatorer som gör att du kan visa detaljerad paketinformation för ett brett spektrum av nätverksprotokoll. Mer information om Wireshark finns i "[Tillägg 2: Använda Wireshark för att fånga nätverkstrafik](#appendix-2)".
* Microsoft Message Analyzer är ett verktyg från Microsoft som ersätter Netmon och som förutom att fånga nätverkspaketdata hjälper dig att visa och analysera loggdata som hämtas från andra verktyg. Mer information finns i "[Bilaga 3: Använda Microsoft Message Analyzer för att samla in nätverkstrafik](#appendix-3)".
* Om du vill utföra ett grundläggande anslutningstest för att kontrollera att klientdatorn kan ansluta till Azure-lagringstjänsten via nätverket kan du inte göra detta med hjälp av **standardformuläret** för ping på klienten. Du kan dock använda [ **tcping-verktyget** ](https://www.elifulkerson.com/projects/tcping.php) för att kontrollera anslutningen.

I många fall räcker loggdata från lagringsloggning och lagringsklientbiblioteket för att diagnostisera ett problem, men i vissa fall kan du behöva den mer detaljerade information som dessa nätverksloggningsverktyg kan tillhandahålla. Med Fiddler för att visa HTTP- och HTTPS-meddelanden kan du till exempel visa huvud- och nyttolastdata som skickas till och från lagringstjänsterna, vilket gör att du kan undersöka hur ett klientprogram försöker återuppta lagringsåtgärder. Protokollanalysatorer som Wireshark arbetar på paketnivå så att du kan visa TCP-data, vilket gör att du kan felsöka förlorade paket och anslutningsproblem. Message Analyzer kan fungera på både HTTP- och TCP-lager.

## <a name="end-to-end-tracing"></a><a name="end-to-end-tracing"></a>Spårning från på slut- till-slut
Spårning från slutna till slutna händer med hjälp av en mängd olika loggfiler är en användbar teknik för att undersöka potentiella problem. Du kan använda datum-/tidsinformationen från dina mätdata som en indikation på var du ska börja leta i loggfilerna efter detaljerad information som hjälper dig att felsöka problemet.

### <a name="correlating-log-data"></a><a name="correlating-log-data"></a>Korrelera loggdata
När du visar loggar från klientprogram, nätverksspårningar och lagringsloggning på serversidan är det viktigt att kunna korrelera begäranden mellan de olika loggfilerna. Loggfilerna innehåller ett antal olika fält som är användbara som korrelationsidentifierare. Klientbegärande-ID är det mest användbara fältet som ska användas för att korrelera transaktioner i de olika loggarna. Ibland kan det dock vara användbart att använda antingen serverbegärande-ID eller tidsstämplar. I följande avsnitt finns mer information om dessa alternativ.

### <a name="client-request-id"></a><a name="client-request-id"></a>ID för klientförfrågan
Storage Client Library genererar automatiskt ett unikt klientbegärande-ID för varje begäran.

* I den klientlogg som lagringsklientbiblioteket skapar visas klientbegärande-ID i fältet **Klientbegärande-ID för** varje loggpost som relaterar till begäran.
* I en nätverksspårning, till exempel en som fångas av Fiddler, visas klientbegäran-ID i begärandemeddelanden som http-huvudvärdet x-ms-client-request.In a network trace such as one captured by Fiddler, the client request ID is visible in request messages as the **x-ms-client-request-id** HTTP header value.
* I loggen för lagringsloggning på serversidan visas klientbegärande-ID i kolumnen Klientbegärande ID.

> [!NOTE]
> Det är möjligt för flera begäranden att dela samma klientbegärande-ID eftersom klienten kan tilldela det här värdet (även om lagringsklientbiblioteket tilldelar ett nytt värde automatiskt). När klienten försöker igen delar alla försök samma klientbegärande-ID. När det gäller en batch som skickas från klienten har batchen ett enda klientfråkomst-ID.
>
>

### <a name="server-request-id"></a><a name="server-request-id"></a>ID för serverbegäran
Lagringstjänsten genererar automatiskt serverbegärande-ID:n.

* I loggen för lagringsloggning på serversidan visas kolumnen För begäran om **begäran om begäran id:et.**
* I en nätverksspårning, till exempel en som fångas av Fiddler, visas serverbegärande-ID i svarsmeddelanden som **http-huvudvärdet x-ms-request.**
* I den klientlogg som lagringsklientbiblioteket skapar visas serverbegärande-ID:t i kolumnen **Åtgärdstext** för loggposten som visar information om serversvaret.

> [!NOTE]
> Lagringstjänsten tilldelar alltid ett unikt serverbegärande-ID till varje begäran som den tar emot, så varje försök att försöka igen från klienten och varje åtgärd som ingår i en batch har ett unikt serverbegärande-ID.
>
>

Om Storage Client Library genererar ett **StorageException** i klienten innehåller egenskapen **RequestInformation** ett **RequestResult-objekt** som innehåller en **ServiceRequestID-egenskap.** Du kan också komma åt ett **RequestResult-objekt** från en **OperationContext-instans.**

Kodexemplet nedan visar hur du anger ett anpassat **ClientRequestId-värde** genom att koppla ett **OperationContext-objekt** begäran till lagringstjänsten. Det visar också hur du hämtar **ServerRequestId-värdet** från svarsmeddelandet.

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

### <a name="timestamps"></a><a name="timestamps"></a>Tidsstämplar
Du kan också använda tidsstämplar för att hitta relaterade loggposter, men var försiktig med eventuella klocksnedställning mellan klienten och servern som kan finnas. Sök plus eller minus 15 minuter efter matchande serverposter baserat på tidsstämpeln på klienten. Kom ihåg att blobmetadata för blobbar som innehåller mått anger tidsintervallet för de mått som lagras i blobben. Det här tidsintervallet är användbart om du har många måttblobbar för samma minut eller timme.

## <a name="troubleshooting-guidance"></a><a name="troubleshooting-guidance"></a>Felsökningsvägledning
Det här avsnittet hjälper dig med diagnos och felsökning av några av de vanliga problem som ditt program kan stöta på när du använder Azure-lagringstjänster. Använd listan nedan för att hitta den information som är relevant för ditt specifika problem.

**Felsöka beslutsträd**

---
Gäller problemet prestanda för en av lagringstjänsterna?

* [Mätningar visar ett högt värde för AverageE2ELatency och ett lågt värde för AverageServerLatency]
* [Mätningar visar låga värden för AverageE2ELatency och AverageServerLatency, men klienten har ändå långa svarstider]
* [Mätningar visar ett högt värde för AverageServerLatency]
* [Du upplever oväntade fördröjningar i en köad meddelandeleverans]

---
Gäller problemet tillgängligheten för en av lagringstjänsterna?

* [Mätningar visar en ökning i PercentThrottlingError]
* [Mätningar visar en ökning i PercentTimeoutError]
* [Mätningar visar en ökning i PercentNetworkError]

---
 Tar klientprogrammet emot ett HTTP 4XX-svar (till exempel 404) från en lagringstjänst?

* [Klienten får HTTP 403-meddelanden (Förbjudet)]
* [Klienten får HTTP 404-meddelanden (Hittades inte)]
* [Klienten får HTTP 409-meddelanden (Konflikt)]

---
[Mått visar låga procentkonsuccess eller analysloggposter har operationer med transaktionsstatus för ClientOtherErrors]

---
[Kapacitetsmått visar en oväntad ökning av användningen av lagringskapacitet]

---
[Du upplever oväntade omstarter av virtuella datorer som har ett stort antal anslutna virtuella hårddiskar]

---
[Ditt problem uppstår från att använda lagringsemulatorn för utveckling eller test]

---
[Du stöter på problem med att installera Azure SDK för .NET]

---
[Du har ett annat problem med en lagringstjänst]

---
### <a name="metrics-show-high-averagee2elatency-and-low-averageserverlatency"></a><a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Mätningar visar ett högt värde för AverageE2ELatency och ett lågt värde för AverageServerLatency
Bilden nedan från [övervakningsverktyget för Azure Portal](https://portal.azure.com) visar ett exempel där **AverageE2ELatency** är betydligt högre än **AverageServerLatency**.

![][4]

Lagringstjänsten beräknar bara måttet **AverageE2ELatency** för lyckade begäranden och innehåller, till skillnad från **AverageServerLatency**, den tid klienten tar för att skicka data och ta emot bekräftelse från lagringstjänsten. Därför kan en skillnad mellan **AverageE2ELatency** och **AverageServerLatency** bero antingen på att klientprogrammet är långsamt att svara eller på grund av förhållanden i nätverket.

> [!NOTE]
> Du kan också visa **E2ELatency** och **ServerLatency** för enskilda lagringsåtgärder i loggdata för lagringsloggning.
>
>

#### <a name="investigating-client-performance-issues"></a>Undersöka problem med klientens prestanda
Möjliga orsaker till att klienten svarar långsamt är att ha ett begränsat antal tillgängliga anslutningar eller trådar, eller att ha ont om resurser som CPU, minne eller nätverksbandbredd. Du kanske kan lösa problemet genom att ändra klientkoden så att den blir effektivare (till exempel genom att använda asynkrona anrop till lagringstjänsten) eller genom att använda en större virtuell dator (med fler kärnor och mer minne).

För tabell- och kötjänster kan Nagle-algoritmen också orsaka hög **AverageE2ELatency** jämfört med **AverageServerLatency:** för mer information finns i inlägget [Nagles algoritm är inte vänlig mot små begäranden](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx). Du kan inaktivera Nagle-algoritmen i kod med klassen **ServicePointManager** i **namnområdet System.Net.** Du bör göra detta innan du ringer några samtal till tabellen eller kötjänster i ditt program eftersom detta inte påverkar anslutningar som redan är öppna. Följande exempel kommer från **metoden Application_Start** i en arbetarroll.

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
tableServicePoint.UseNagleAlgorithm = false;
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

Du bör kontrollera loggarna på klientsidan för att se hur många begäranden klientprogrammet skickar och kontrollera om det finns allmänna .NET-relaterade flaskhalsar i klienten, till exempel CPU, .NET-skräpinsamling, nätverksanvändning eller minne. Som utgångspunkt för felsökning av .NET-klientprogram finns i [Felsökning, spårning och profilering](https://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Undersöka problem med nätverksfördröjning
Vanligtvis beror hög end-to-end-svarstid som orsakas av nätverket på tillfälliga förhållanden. Du kan undersöka både tillfälliga och beständiga nätverksproblem, till exempel ignorerade paket med hjälp av verktyg som Wireshark eller Microsoft Message Analyzer.

Mer information om hur du använder Wireshark för att felsöka nätverksproblem finns i "[Bilaga 2: Använda Wireshark för att fånga nätverkstrafik]."

Mer information om hur du använder Microsoft Message Analyzer för att felsöka nätverksproblem finns i "[Bilaga 3: Använda Microsoft Message Analyzer för att samla in nätverkstrafik]."

### <a name="metrics-show-low-averagee2elatency-and-low-averageserverlatency-but-the-client-is-experiencing-high-latency"></a><a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Mätningar visar låga värden för AverageE2ELatency och AverageServerLatency, men klienten har ändå långa svarstider
I det här fallet är den mest sannolika orsaken en fördröjning i lagringsbegäranden som når lagringstjänsten. Du bör undersöka varför begäranden från klienten inte går vidare till blob-tjänsten.

En möjlig orsak till att klienten försenar skicka förfrågningar är att det finns ett begränsat antal tillgängliga anslutningar eller trådar.

Kontrollera också om klienten utför flera försök och undersök orsaken om det är det. Om du vill ta reda på om klienten utför flera försök kan du:

* Undersök lagringsanalysloggarna. Om flera försök sker visas flera åtgärder med samma klientbegärande-ID men med olika serverbegärande-ID:n.
* Undersök klientloggarna. Utförlig loggning indikerar att ett nytt försök har inträffat.
* Felsöka koden och kontrollera egenskaperna för **Det OperationContext-objekt** som är associerat med begäran. Om åtgärden har gjorts igen innehåller egenskapen **RequestResults** flera unika serverbegärande-ID:er. Du kan också kontrollera start- och sluttider för varje begäran. Mer information finns i kodexemplet i avsnittet [Serverbegärans-ID].

Om det inte finns några problem i klienten bör du undersöka potentiella nätverksproblem, till exempel paketförlust. Du kan använda verktyg som Wireshark eller Microsoft Message Analyzer för att undersöka nätverksproblem.

Mer information om hur du använder Wireshark för att felsöka nätverksproblem finns i "[Bilaga 2: Använda Wireshark för att fånga nätverkstrafik]."

Mer information om hur du använder Microsoft Message Analyzer för att felsöka nätverksproblem finns i "[Bilaga 3: Använda Microsoft Message Analyzer för att samla in nätverkstrafik]."

### <a name="metrics-show-high-averageserverlatency"></a><a name="metrics-show-high-AverageServerLatency"></a>Mätningar visar ett högt värde för AverageServerLatency
När det gäller hög **Genomsnittligserverlatency** för blob-hämtningsbegäranden bör du använda lagringsloggningsloggarna för att se om det finns upprepade begäranden om samma blob (eller uppsättning blobbar). För blob-uppladdningsbegäranden bör du undersöka vilken blockstorlek klienten använder (till exempel kan block som är mindre än 64 K i storlek resultera i omkostnader om inte läsningarna också finns i mindre än 64 K-segment) och om flera klienter laddar upp block till samma blob i Parallell. Du bör också kontrollera måtten per minut för toppar i antalet begäranden som resulterar i att minska skalbarhetsmålen per sekund: se även "[Mått visar en ökning av PercentTimeoutError]."

Om du ser hög **GenomsnittligServerLatency** för blob-hämtningsbegäranden när det finns upprepade begäranden om samma blob eller uppsättning blobbar, bör du överväga att cachelagra dessa blobbar med Azure Cache eller Azure Content Delivery Network (CDN). För överföringsbegäranden kan du förbättra dataflödet med hjälp av en större blockstorlek. För frågor till tabeller är det också möjligt att implementera cachelagring på klientsidan på klienter som utför samma frågeåtgärder och där data inte ändras ofta.

Högt **genomsnittServerLatency-värden** kan också vara ett symptom på dåligt utformade tabeller eller frågor som resulterar i skanningsåtgärder eller som följer tilläggs-/prepend-anti-mönster. Mer information finns i "[Mått visar en ökning av PercentThrottlingError]".

> [!NOTE]
> Du hittar en omfattande checklista för checklista för prestanda här: [Checklista för Microsoft Azure-lagringsprestanda och skalbarhet](storage-performance-checklist.md).
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery-on-a-queue"></a><a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Du upplever oväntade fördröjningar i en köad meddelandeleverans
Om du upplever en fördröjning mellan den tidpunkt då ett program lägger till ett meddelande i en kö och den tid det blir tillgängligt att läsa från kön, bör du vidta följande åtgärder för att diagnostisera problemet:

* Kontrollera att programmet har lagt till meddelandena i kön. Kontrollera att programmet inte försöker igen metoden **AddMessage** flera gånger innan det lyckas. Loggarna för lagringsklientbibliotek visar alla upprepade återförsök av lagringsåtgärder.
* Kontrollera att det inte finns någon klocksnedställning mellan arbetarrollen som lägger till meddelandet i kön och arbetarrollen som läser meddelandet från kön som gör att det ser ut som om bearbetningen försenas.
* Kontrollera om arbetarrollen som läser meddelandena från kön misslyckas. Om en köklient **anropar GetMessage-metoden** men inte svarar med en bekräftelse förblir meddelandet osynligt i kön tills **utcheckningsperioden för invisibilityTimeout** löper ut. Nu blir meddelandet tillgängligt för bearbetning igen.
* Kontrollera om kölängden växer med tiden. Detta kan inträffa om du inte har tillräckligt med arbetare tillgängliga för att bearbeta alla meddelanden som andra arbetare placerar i kön. Kontrollera också måtten för att se om ta bort begäranden misslyckas och dequeue räkna med meddelanden, vilket kan tyda på upprepade misslyckade försök att ta bort meddelandet.
* Undersök lagringsloggningsloggarna för alla köåtgärder som har högre värden än förväntat **E2ELatency** och **ServerLatency** under en längre tidsperiod än vanligt.

### <a name="metrics-show-an-increase-in-percentthrottlingerror"></a><a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Mätningar visar en ökning i PercentThrottlingError
Begränsningsfel uppstår när du överskrider skalbarhetsmålen för en lagringstjänst. Lagringstjänsten begränsar för att säkerställa att ingen enskild klient eller klient kan använda tjänsten på bekostnad av andra. Mer information finns i [Skalbarhets- och prestandamål för standardlagringskonton](scalability-targets-standard-account.md) för information om skalbarhetsmål för lagringskonton och prestandamål för partitioner i lagringskonton.

Om måttet **PercentThrottlingError** visar en ökning av procentandelen begäranden som misslyckas med ett begränsningsfel måste du undersöka ett av två scenarier:

* [Övergående ökning av PercentThrottlingError]
* [Permanent ökning av procenttorlingerrorsfel]

En ökning av **PercentThrottlingError** sker ofta samtidigt som antalet lagringsbegäranden ökar eller när du först läser in testningen av ditt program. Detta kan också visa sig i klienten som "503 Server Busy" eller "500 Operation Timeout" HTTP-statusmeddelanden från lagringsåtgärder.

#### <a name="transient-increase-in-percentthrottlingerror"></a><a name="transient-increase-in-PercentThrottlingError"></a>Övergående ökning av PercentThrottlingError
Om du ser toppar i värdet **för PercentThrottlingError** som sammanfaller med perioder med hög aktivitet för programmet implementerar du en exponentiell (inte linjär) back-off-strategi för återförsök i klienten. Back-off-återförsök minskar den omedelbara belastningen på partitionen och hjälper ditt program att jämna ut toppar i trafiken. Mer information om hur du implementerar principer för återförsök med lagringsklientbiblioteket finns i [namnområdet Microsoft.Azure.Storage.RetryPolicies](/dotnet/api/microsoft.azure.storage.retrypolicies).

> [!NOTE]
> Du kan också se toppar i värdet **för PercentThrottlingError** som inte sammanfaller med perioder med hög aktivitet för programmet: den mest sannolika orsaken här är lagringstjänsten flytta partitioner för att förbättra belastningsutjämning.
>
>

#### <a name="permanent-increase-in-percentthrottlingerror-error"></a><a name="permanent-increase-in-PercentThrottlingError"></a>Permanent ökning av procenttorlingerrorsfel
Om du ser ett genomgående högt värde för **PercentThrottlingError** efter en permanent ökning av dina transaktionsvolymer, eller när du utför dina första belastningstester på ditt program, måste du utvärdera hur ditt program använder lagringspartitioner och om det närmar sig skalbarhetsmålen för ett lagringskonto. Om du till exempel ser begränsningsfel i en kö (som räknas som en enda partition) bör du överväga att använda ytterligare köer för att sprida transaktionerna över flera partitioner. Om du ser begränsningsfel i en tabell måste du överväga att använda ett annat partitioneringsschema för att sprida dina transaktioner över flera partitioner med hjälp av ett bredare utbud av partitionsnyckelvärden. En vanlig orsak till det här problemet är prepend/tillägg anti-pattern där du väljer datumet som partitionsnyckel och sedan alla data på en viss dag skrivs till en partition: under belastning kan detta resultera i en skrivflaskhals. Tänk antingen på en annan partitioneringsdesign eller utvärdera om det kan vara en bättre lösning att använda blob-lagring. Kontrollera också om begränsning sker som ett resultat av toppar i trafiken och undersöka olika sätt att jämna ut ditt mönster av begäranden.

Om du distribuerar dina transaktioner över flera partitioner måste du fortfarande vara medveten om de skalbarhetsgränser som angetts för lagringskontot. Om du till exempel använde tio köer varje bearbetning av högst 2 000 1KB-meddelanden per sekund, kommer du att ha den totala gränsen på 20 000 meddelanden per sekund för lagringskontot. Om du behöver bearbeta mer än 20 000 entiteter per sekund bör du överväga att använda flera lagringskonton. Du bör också komma ihåg att storleken på dina begäranden och entiteter påverkar när lagringstjänsten begränsar dina klienter: om du har större begäranden och entiteter kan du begränsas tidigare.

Ineffektiv frågedesign kan också leda till att du når skalbarhetsgränserna för tabellpartitioner. En fråga med ett filter som bara väljer en procent av entiteterna i en partition, men som genomsöker alla entiteter i en partition måste till exempel komma åt varje entitet. Varje enhet som läss in räknas in i det totala antalet transaktioner i den partitionen. Därför kan du enkelt nå skalbarhetsmålen.

> [!NOTE]
> Dina prestandatester bör avslöja alla ineffektiva frågedesigner i ditt program.
>
>

### <a name="metrics-show-an-increase-in-percenttimeouterror"></a><a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Mätningar visar en ökning i PercentTimeoutError
Dina mått visar en ökning av **PercentTimeoutError** för en av dina lagringstjänster. Samtidigt får klienten en hög volym av HTTP-statusmeddelanden för "500 Operation Timeout" från lagringsåtgärder.

> [!NOTE]
> Tidsgränsen kan tillfälligt visas när belastningsutjämningsbegäranden för lagringstjänstern balanserar genom att flytta en partition till en ny server.
>
>

**Måttet PercentTimeoutError** är en aggregering av följande mått: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutServerror**, **AnonymousServerTimeoutError**och **SASTimeoutError**.

Servertidsutseringarna orsakas av ett fel på servern. Klienttidsgränsen inträffar eftersom en åtgärd på servern har överskridit den timeout som anges av klienten. En klient som använder storage-klientbiblioteket kan till exempel ange en timeout för en åtgärd med hjälp av egenskapen **ServerTimeout** för klassen **QueueRequestOptions.**

Servertidsutgångar indikerar ett problem med lagringstjänsten som kräver ytterligare undersökning. Du kan använda mått för att se om du når skalbarhetsgränserna för tjänsten och för att identifiera eventuella toppar i trafiken som kan orsaka det här problemet. Om problemet är intermittent kan det bero på belastningsutjämningsaktivitet i tjänsten. Om problemet är beständigt och inte orsakas av att ditt program når tjänstens skalbarhetsgränser bör du ta upp ett supportproblem. För klienttidsutgångar måste du bestämma om timeouten är inställd på ett lämpligt värde i klienten och antingen ändra tidsgränsen som angetts i klienten eller undersöka hur du kan förbättra prestanda för åtgärderna i lagringstjänsten, till exempel genom att optimera tabellfrågor eller minska storleken på dina meddelanden.

### <a name="metrics-show-an-increase-in-percentnetworkerror"></a><a name="metrics-show-an-increase-in-PercentNetworkError"></a>Mätningar visar en ökning i PercentNetworkError
Dina mått visar en ökning av **PercentNetworkError** för en av dina lagringstjänster. **Måttet PercentNetworkError** är en aggregering av följande mått: **NetworkError**, **AnonymousNetworkError**och **SASNetworkError**. Dessa inträffar när lagringstjänsten upptäcker ett nätverksfel när klienten gör en lagringsbegäran.

Den vanligaste orsaken till det här felet är att en klient kopplar från innan en timeout upphör att gälla i lagringstjänsten. Undersök koden i klienten för att förstå varför och när klienten kopplar från lagringstjänsten. Du kan också använda Wireshark, Microsoft Message Analyzer eller Tcping för att undersöka problem med nätverksanslutningen från klienten. Dessa verktyg beskrivs i [bilagorna].

### <a name="the-client-is-receiving-http-403-forbidden-messages"></a><a name="the-client-is-receiving-403-messages"></a>Klienten får HTTP 403-meddelanden (Förbjudet)
Om klientprogrammet utfärdar HTTP 403-fel (förbjudet) beror det förmodligen på att klienten använder en SAS (signatur för delad åtkomst) som har upphört att gälla när den skickar förfrågningar om lagring (även om det finns andra orsaker, som klockförskjutning, ogiltiga nycklar eller tomma rubriker). Om orsaken är en SAS-nyckel som har upphört att gälla visas inte några poster i Storage Logging-loggdata på serversidan. I följande tabell visas ett exempel från loggen på klientsidan som genereras av storage client library som illustrerar det här problemet:

| Källa | Utförlighet | Utförlighet | ID för klientförfrågan | Åtgärdstext |
| --- | --- | --- | --- | --- |
| Microsoft.Azure.Storage |Information |3 |85d077ab-... |Startar åtgärden med plats Primär per platsläge Primärt. |
| Microsoft.Azure.Storage |Information |3 |85d077ab -... |Starta synkron begäran till<https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&sr=c&si=mypolicy&sig=OFnd4Rd7z01fIvh%2BmcR6zbudIH2F5Ikm%2FyhNYZEmJNQ%3D&api-version=2014-02-14> |
| Microsoft.Azure.Storage |Information |3 |85d077ab -... |Väntar på svar. |
| Microsoft.Azure.Storage |Varning |2 |85d077ab -... |Undantag som genereras i väntan på svar: Fjärrservern returnerade ett fel: (403) Förbjudet. |
| Microsoft.Azure.Storage |Information |3 |85d077ab -... |Svar mottaget. Statuskod = 403, Begärande-ID = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = , ETag = . |
| Microsoft.Azure.Storage |Varning |2 |85d077ab -... |Undantag som genereras under åtgärden: Fjärrservern returnerade ett fel: (403) Förbjudet.. |
| Microsoft.Azure.Storage |Information |3 |85d077ab -... |Kontrollera om åtgärden ska göras på nytt. Antal försök igen = 0, HTTP-statuskod = 403, Undantag = Fjärrservern returnerade ett fel: (403) Förbjudet.. |
| Microsoft.Azure.Storage |Information |3 |85d077ab -... |Nästa plats har ställts in på Primär, baserat på platsläget. |
| Microsoft.Azure.Storage |Fel |1 |85d077ab -... |Återförsöksprincipen tillät inte ett nytt försök. Misslyckas med Fjärrservern returnerade ett fel: (403) Förbjudet. |

I det här fallet bör du undersöka varför SAS-token löper ut innan klienten skickar token till servern:

* Normalt bör du inte ange någon starttid när du skapar en SAS som klienten ska använda direkt. Om det förekommer små klockskillnader mellan värden som genererar SAS och lagringstjänsten kan tjänsten ta emot en SAS som inte har börjat gälla ännu.
* Ange inte en mycket kort giltighetstid för en SAS. Återigen kan små klockskillnader mellan värden som genererar SAS och lagringstjänsten göra att SAS tycks upphöra tidigare än förväntat.
* Matchar versionsparametern i SAS-nyckeln (till exempel **sv=2015-04-05)** den version av lagringsklientbiblioteket som du använder? Vi rekommenderar att du alltid använder den senaste versionen av [storage client library](https://www.nuget.org/packages/WindowsAzure.Storage/).
* Om du genererar om dina lagringsåtkomstnycklar kan befintliga SAS-token bli ogiltiga. Det här problemet kan uppstå om du genererar SAS-token med lång förfallotid som klientappar ska cachelagra.

Om du använder Storage Client Library till att generera SAS-token är det enkelt att skapa en giltig token. Om du använder REST-API:et för lagring och konstruerar SAS-token för hand läser du [Delegera åtkomst med en signature för delad åtkomst](https://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-http-404-not-found-messages"></a><a name="the-client-is-receiving-404-messages"></a>Klienten får HTTP 404-meddelanden (Hittades inte)
Om klientprogrammet tar emot ett HTTP 404-meddelande (hittades inte) från servern innebär det att objektet som klienten försökte använda (till exempel en entitet, tabell, blob, container eller kö) inte finns i lagringstjänsten. Här är några av de möjliga orsakerna:

* [Klienten eller en annan process har tagit bort objektet]
* [Det är problem med SAS-autentiseringen (signatur för delad åtkomst)]
* [JavaScript-koden på klientsidan har inte behörighet att komma åt objektet]
* [Nätverksfel]

#### <a name="the-client-or-another-process-previously-deleted-the-object"></a><a name="client-previously-deleted-the-object"></a>Klienten eller en annan process har tagit bort objektet
I scenarier där klienten försöker läsa, uppdatera eller ta bort data i en lagringstjänst är det vanligtvis lätt att identifiera i serverloggarna en tidigare åtgärd som tog bort objektet i fråga från lagringstjänsten. Loggdata visar ofta att en annan användare eller process har tagit bort objektet. I loggen för lagringsloggning på serversidan visas kolumnerna för åtgärdstyp och begärd objektnyckel när en klient har tagit bort ett objekt.

I det scenario där en klient försöker infoga ett objekt kanske det inte är omedelbart uppenbart varför detta resulterar i ett HTTP 404-svar (hittades inte) med tanke på att klienten skapar ett nytt objekt. Men om klienten skapar en blob måste den kunna hitta blob-behållaren, om klienten skapar ett meddelande måste den kunna hitta en kö och om klienten lägger till en rad måste den kunna hitta tabellen.

Du kan använda loggen på klientsidan från storage-klientbiblioteket för att få en mer detaljerad förståelse för när klienten skickar specifika begäranden till lagringstjänsten.

Följande klientlogg som genereras av storage client-biblioteket illustrerar problemet när klienten inte kan hitta behållaren för den blob som den skapar. Den här loggen innehåller information om följande lagringsåtgärder:

| Begär ID | Åtgärd |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** metod för att ta bort blob-behållaren. Observera att den här åtgärden innehåller en **HEAD-begäran** om att kontrollera om behållaren finns. |
| e2d06d78... |**CreateIfNotExists** metod för att skapa blob-behållaren. Observera att den här åtgärden innehåller en **HEAD-begäran** som kontrollerar förekomsten av behållaren. **HEAD** returnerar ett 404-meddelande men fortsätter. |
| de8b1c3c-... |**UploadFromStream-metoden** för att skapa blobben. **PUT-begäran** misslyckas med ett 404-meddelande |

Loggposter:

| Begär ID | Åtgärdstext |
| --- | --- |
| 07b26a5d-... |Startar synkron begäran https://domemaildist.blob.core.windows.net/azuremmblobcontainertill . |
| 07b26a5d-... |StringToSign = HEAD............ x-ms-client-request-id:07b26a5d-.... x-ms-date:tis, 03 jun 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Väntar på svar. |
| 07b26a5d-... |Svar mottaget. Statuskod = 200, begärande-ID = eeead849-... Innehåll-MD5 = , &quot;ETag = 0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |Svarshuvuden har bearbetats och fortsätter med resten av åtgärden. |
| 07b26a5d-... |Ladda ner svarstext. |
| 07b26a5d-... |Åtgärden har slutförts. |
| 07b26a5d-... |Startar synkron begäran https://domemaildist.blob.core.windows.net/azuremmblobcontainertill . |
| 07b26a5d-... |StringToSign = DELETE............ x-ms-client-request-id:07b26a5d-.... x-ms-date:tis, 03 jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Väntar på svar. |
| 07b26a5d-... |Svar mottaget. Statuskod = 202, Begäran ID = 6ab2a4cf-..., Content-MD5 = , ETag = . |
| 07b26a5d-... |Svarshuvuden har bearbetats och fortsätter med resten av åtgärden. |
| 07b26a5d-... |Ladda ner svarstext. |
| 07b26a5d-... |Åtgärden har slutförts. |
| e2d06d78-... |Startar asynkron begäran https://domemaildist.blob.core.windows.net/azuremmblobcontainertill .</td> |
| e2d06d78-... |StringToSign = HEAD............ x-ms-client-request-id:e2d06d78-.... x-ms-date:tis, 03 jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Väntar på svar. |
| de8b1c3c-... |Startar synkron begäran https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txttill . |
| de8b1c3c-... |StringToSign = PUT... 64.qCmF+TQLPhq/YYK50mP9ZQ==........ x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-.... x-ms-date:tis, 03 jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Förbereder att skriva begärandedata. |
| e2d06d78-... |Undantag som genereras i väntan på svar: Fjärrservern returnerade ett fel: (404) Hittades inte.. |
| e2d06d78-... |Svar mottaget. Statuskod = 404, Begäran ID = 353ae3bc-..., Content-MD5 = , ETag = . |
| e2d06d78-... |Svarshuvuden har bearbetats och fortsätter med resten av åtgärden. |
| e2d06d78-... |Ladda ner svarstext. |
| e2d06d78-... |Åtgärden har slutförts. |
| e2d06d78-... |Startar asynkron begäran https://domemaildist.blob.core.windows.net/azuremmblobcontainertill . |
| e2d06d78-... |StringToSign = PUT... 0.........x-ms-client-request-id:e2d06d78-.... x-ms-date:tis, 03 jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Väntar på svar. |
| de8b1c3c-... |Skriva förfråde uppgifter. |
| de8b1c3c-... |Väntar på svar. |
| e2d06d78-... |Undantag som genereras i väntan på svar: Fjärrservern returnerade ett fel: (409) Konflikt.. |
| e2d06d78-... |Svar mottaget. Statuskod = 409, Begäran ID = c27da20e-..., Content-MD5 = , ETag = . |
| e2d06d78-... |Hämta felsvarstexten. |
| de8b1c3c-... |Undantag som genereras i väntan på svar: Fjärrservern returnerade ett fel: (404) Hittades inte.. |
| de8b1c3c-... |Svar mottaget. Statuskod = 404, Begäran ID = 0eaeab3e-..., Content-MD5 = , ETag = . |
| de8b1c3c-... |Undantag som genereras under åtgärden: Fjärrservern returnerade ett fel: (404) Hittades inte.. |
| de8b1c3c-... |Återförsöksprincipen tillät inte ett nytt försök. Misslyckas med Fjärrservern returnerade ett fel: (404) Hittades inte.. |
| e2d06d78-... |Återförsöksprincipen tillät inte ett nytt försök. Misslyckas med Fjärrservern returnerade ett fel: (409) Conflict.. |

I det här exemplet visar loggen att klienten är interfolierande begäranden från metoden **CreateIfNotExists** (request ID e2d06d78...) med begäranden från **Metoden UploadFromStream** (de8b1c3c-...). Det här interfolierandet beror på att klientprogrammet anropar dessa metoder asynkront. Ändra den asynkrona koden i klienten för att säkerställa att den skapar behållaren innan du försöker överföra data till en blob i den behållaren. Helst bör du skapa alla dina behållare i förväg.

#### <a name="a-shared-access-signature-sas-authorization-issue"></a><a name="SAS-authorization-issue"></a>Det är problem med SAS-autentiseringen (signatur för delad åtkomst)
Om klientprogrammet försöker använda en SAS-nyckel som inte innehåller nödvändiga behörigheter för åtgärden returnerar lagringstjänsten ett HTTP 404-meddelande (hittades inte) till klienten. Samtidigt ser du också ett värde som inte är noll för **SASAuthorizationError** i måtten.

I följande tabell visas ett exempel på serverserverns loggmeddelande från loggfilen För lagringsloggning:

| Namn | Värde |
| --- | --- |
| Begär starttid | 2014-05-30T06:17:48.4473697Z |
| Typ av åtgärd     | GetBlobProperties            |
| Status för begäran     | SASAuthorizationError        |
| HTTP-statuskod   | 404                          |
| Autentiseringstyp| Sas                          |
| Typ av tjänst       | Blob                         |
| URL för begäran        | https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&sig=XXXXX&;api-version=2014-02-14 |
| Begäran id-huvud  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| ID för klientförfrågan  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Undersök varför klientprogrammet försöker utföra en åtgärd som det inte har beviljats behörighet för.

#### <a name="client-side-javascript-code-does-not-have-permission-to-access-the-object"></a><a name="JavaScript-code-does-not-have-permission"></a>JavaScript-koden på klientsidan har inte behörighet att komma åt objektet
Om du använder en JavaScript-klient och lagringstjänsten returnerar HTTP 404-meddelanden söker du efter följande JavaScript-fel i webbläsaren:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Du kan använda F12 Developer Tools i Internet Explorer för att spåra de meddelanden som utbyts mellan webbläsaren och lagringstjänsten när du felsöker JavaScript-problem på klientsidan.
>
>

Dessa fel uppstår eftersom webbläsaren implementerar [samma säkerhetsbegränsning](https://www.w3.org/Security/wiki/Same_Origin_Policy) för ursprungsprincipen som förhindrar att en webbsida anropar ett API i en annan domän än den domän som sidan kommer från.

För att komma runt JavaScript-problemet kan du konfigurera CORS (Cross Origin Resource Sharing) för den lagringstjänst som klienten har åtkomst till. Mer information finns i [CORS-stöd (Cross-Origin Resource Sharing) för Azure Storage Services](https://msdn.microsoft.com/library/azure/dn535601.aspx).

Följande kodexempel visar hur du konfigurerar blob-tjänsten så att JavaScript som körs i Contoso-domänen kan komma åt en blob i blob-lagringstjänsten:

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

#### <a name="network-failure"></a><a name="network-failure"></a>Nätverksfel
I vissa fall kan förlorade nätverkspaket leda till att lagringstjänsten returnerar HTTP 404-meddelanden till klienten. Till exempel när klientprogrammet tar bort en entitet från tabelltjänsten ser du att klienten genererar ett lagringsundantag som rapporterar statusmeddelandet "HTTP 404 (Hittades inte)" från tabelltjänsten. När du undersöker tabellen i table storage-tjänsten ser du att tjänsten tog bort entiteten som begärts.

Undantagsinformationen i klienten inkluderar begärande-ID (7e84f12d...) som tilldelats av tabelltjänsten för begäran: du kan använda den här informationen för att hitta information om begäran i lagringsloggarna på serversidan genom att söka i kolumnen **för begäran-id-header** i loggfilen. Du kan också använda måtten för att identifiera när fel som detta inträffar och sedan söka i loggfilerna baserat på den tid då måtten registrerade det här felet. Den här loggposten visar att borttagningen misslyckades med statusmeddelandet "HTTP (404) Klient annat fel". Samma loggpost innehåller även begärande-ID som genereras av klienten i kolumnen **klient-request-id** (813ea74f...).

Serverloggen innehåller också en annan post med samma **klient-request-id-värde** (813ea74f...) för en lyckad borttagningsåtgärd för samma entitet och från samma klient. Den här lyckade borttagningsåtgärden ägde rum mycket kort innan den misslyckade borttagningsbegäran.

Den mest sannolika orsaken till det här scenariot är att klienten skickade en borttagningsbegäran för entiteten till tabelltjänsten, som lyckades, men inte fick någon bekräftelse från servern (kanske på grund av ett tillfälligt nätverksproblem). Klienten försökte sedan automatiskt åtgärden (med samma **klient-begäran-id),** och det här återförsöket misslyckades eftersom entiteten redan hade tagits bort.

Om det här problemet uppstår ofta bör du undersöka varför klienten inte tar emot bekräftelser från tabelltjänsten. Om problemet är intermittent bör du svälla felet "HTTP (404) Hittades inte" och logga in klienten, men låta klienten fortsätta.

### <a name="the-client-is-receiving-http-409-conflict-messages"></a><a name="the-client-is-receiving-409-messages"></a>Klienten får HTTP 409-meddelanden (Konflikt)
I följande tabell visas ett utdrag från serverloggen för två klientåtgärder: **DeleteIfExists** följt omedelbart av **CreateIfNotExists** med samma blob-behållarnamn. Varje klientåtgärd resulterar i två begäranden som skickas till servern, först en **GetContainerProperties-begäran** för att kontrollera om behållaren finns, följt av **Begäran om DeleteContainer** eller **CreateContainer.**

| Tidsstämpel | Åtgärd | Resultat | Containerns namn | ID för klientförfrågan |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |FåContainerProperties |200 |mmcont (mmcont) |c9f52c89-... |
| 05:10:13.8167325 |Ta bortcontainer |202 |mmcont (mmcont) |c9f52c89-... |
| 05:10:13.8987407 |FåContainerProperties |404 |mmcont (mmcont) |bc881924-... |
| 05:10:14.2147723 |Skapacontainer |409 |mmcont (mmcont) |bc881924-... |

Koden i klientprogrammet tar bort och återskapar sedan omedelbart en blob-behållare med samma namn: **metoden CreateIfNotExists** (Client request ID bc881924-...) misslyckas så småningom med felet HTTP 409 (Conflict). När en klient tar bort containrar, tabeller eller köer tar det en liten stund innan namnet blir tillgängligt igen.

Klientappen bör använda unika containernamn när nya containrar skapas om det är ett vanligt mönster att ta bort/återskapa dem.

### <a name="metrics-show-low-percentsuccess-or-analytics-log-entries-have-operations-with-transaction-status-of-clientothererrors"></a><a name="metrics-show-low-percent-success"></a>Mått visar låga procentkonsuccess eller analysloggposter har operationer med transaktionsstatus för ClientOtherErrors
Måttet **Procentsuccess** fångar procentandelen av åtgärder som lyckades baserat på deras HTTP-statuskod. Åtgärder med statuskoder på 2XX räknas som lyckade, medan operationer med statuskoder i intervallen 3XX, 4XX och 5XX räknas som misslyckade och sänker **måttvärdet Procentsuccess.** I lagringsloggfilerna på serversidan registreras dessa åtgärder med transaktionsstatus **för ClientOtherErrors**.

Det är viktigt att notera att dessa åtgärder har slutförts och därför inte påverkar andra mått, till exempel tillgänglighet. Några exempel på åtgärder som körs men som kan resultera i misslyckade HTTP-statuskoder är:

* **ResourceNotFound** (Hittades inte 404), till exempel från en GET-begäran till en blob som inte finns.
* **ResursExreadyExister (Konflikt** 409), till exempel från en **CreateIfNotExist-åtgärd** där resursen redan finns.
* **ConditionNotMet** (Inte ändrat 304), till exempel från en villkorlig åtgärd, till exempel när en klient skickar ett **ETag-värde** och ett HTTP **If-None-Match-huvud** för att begära en avbildning endast om den har uppdaterats sedan den senaste åtgärden.

Du hittar en lista över vanliga REST API-felkoder som lagringstjänsterna returnerar på sidan [Vanliga REST API-felkoder](https://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase-in-storage-capacity-usage"></a><a name="capacity-metrics-show-an-unexpected-increase"></a>Kapacitetsmått visar en oväntad ökning av användningen av lagringskapacitet
Om du ser plötsliga, oväntade ändringar i kapacitetsanvändningen i ditt lagringskonto kan du undersöka orsakerna genom att först titta på dina tillgänglighetsmått. Till exempel kan en ökning av antalet misslyckade borttagningsbegäranden leda till en ökning av mängden blob-lagring som du använder som programspecifika rensningsåtgärder som du kanske förväntade dig att frigöra utrymme kanske inte fungerar som förväntat (till exempel , eftersom SAS-token som används för att frigöra utrymme har upphört att gälla).

### <a name="your-issue-arises-from-using-the-storage-emulator-for-development-or-test"></a><a name="your-issue-arises-from-using-the-storage-emulator"></a>Ditt problem uppstår från att använda lagringsemulatorn för utveckling eller test
Du använder vanligtvis lagringsemulatorn under utveckling och test för att undvika kravet på ett Azure-lagringskonto. De vanligaste problemen som kan uppstå när du använder lagringsemulatorn är:

* [Funktionen "X" fungerar inte i lagringsemulatorn]
* [Fel "Värdet för ett av HTTP-huvudena är inte i rätt format" när lagringsemulatorn används]
* [Att köra lagringsemulatorn kräver administratörsbehörighet]

#### <a name="feature-x-is-not-working-in-the-storage-emulator"></a><a name="feature-X-is-not-working"></a>Funktionen "X" fungerar inte i lagringsemulatorn
Lagringsemulatorn stöder inte alla funktioner i Azure-lagringstjänsterna, till exempel filtjänsten. Mer information finns i [Använd Azure Storage-emulatorn för utveckling och testning](storage-use-emulator.md).

För de funktioner som lagringsemulatorn inte stöder använder du Azure-lagringstjänsten i molnet.

#### <a name="error-the-value-for-one-of-the-http-headers-is-not-in-the-correct-format-when-using-the-storage-emulator"></a><a name="error-HTTP-header-not-correct-format"></a>Fel "Värdet för ett av HTTP-huvudena är inte i rätt format" när lagringsemulatorn används
Du testar ditt program som använder storage-klientbiblioteket mot den lokala lagringsemulatorn och metodanrop som **CreateIfNotExists** misslyckas med felmeddelandet "Värdet för ett av HTTP-huvudena är inte i rätt format." Detta indikerar att den version av lagringsemulatorn du använder inte stöder den version av lagringsklientbiblioteket som du använder. Storage Client Library lägger till huvud **x-ms-versionen** i alla begäranden som görs. Om lagringsemulatorn inte känner igen värdet i **x-ms-versionshuvudet** avvisas begäran.

Du kan använda lagringsbibliotekets klientloggar för att se värdet för **x-ms-versionshuvudet** som skickas. Du kan också se värdet för **x-ms-versionshuvudet** om du använder Fiddler för att spåra begäranden från klientprogrammet.

Det här scenariot uppstår vanligtvis om du installerar och använder den senaste versionen av storage client library utan att uppdatera lagringsemmulatorn. Du bör antingen installera den senaste versionen av lagringsemulatorn eller använda molnlagring i stället för emulatorn för utveckling och test.

#### <a name="running-the-storage-emulator-requires-administrative-privileges"></a><a name="storage-emulator-requires-administrative-privileges"></a>Att köra lagringsemulatorn kräver administratörsbehörighet
Du uppmanas att ange administratörsbehörighet när du kör lagringsemulatorn. Detta inträffar bara när du initierar lagringsemulatorn för första gången. När du har initierat lagringsemulatorn behöver du inte administratörsbehörighet för att köra den igen.

Mer information finns i [Använd Azure Storage-emulatorn för utveckling och testning](storage-use-emulator.md). Du kan också initiera lagringsemulatorn i Visual Studio, vilket också kräver administratörsbehörighet.

### <a name="you-are-encountering-problems-installing-the-azure-sdk-for-net"></a><a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Du stöter på problem med att installera Azure SDK för .NET
NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k fÃ¶rsÃ¶k att installera SDK misslyckas fÃ¶r att installera lagringsemultorn fÃ¶r lagringsemultorn fÃ¶r den lokala datorn. Installationsloggen innehåller något av följande meddelanden:

* CAQuietExec: Fel: Det gick inte att komma åt SQL-instans
* CAQuietExec: Fel: Det gick inte att skapa databasen

Orsaken är ett problem med befintlig LocalDB-installation. Som standard använder lagringsemulatorn LocalDB för att bevara data när den simulerar Azure-lagringstjänster. Du kan återställa localdb-instansen genom att köra följande kommandon i ett kommandotolksfönster innan du försöker installera SDK.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

Kommandot **Delete** tar bort alla gamla databasfiler från tidigare installationer av lagringsemulatorn.

### <a name="you-have-a-different-issue-with-a-storage-service"></a><a name="you-have-a-different-issue-with-a-storage-service"></a>Du har ett annat problem med en lagringstjänst
Om de tidigare felsökningsavsnitten inte innehåller problemet du har med en lagringstjänst bör du använda följande metod för att diagnostisera och felsöka problemet.

* Kontrollera dina mått för att se om det finns någon förändring från ditt förväntade baslinjebeteende. Från måtten kanske du kan avgöra om problemet är tillfälligt eller permanent och vilka lagringsåtgärder problemet påverkar.
* Du kan använda mätinformationen för att söka efter loggdata på serversidan efter mer detaljerad information om eventuella fel som uppstår. Den här informationen kan hjälpa dig att felsöka och lösa problemet.
* Om informationen i serverloggarna inte är tillräcklig för att felsöka problemet kan du använda klientbibliotekets klientserverloggar för lagringsklientbibliotek för att undersöka hur klientprogrammet fungerar och verktyg som Fiddler, Wireshark och Microsoft Message Analyzer för att undersöka nätverket.

Mer information om hur du använder Fiddler finns i "[Bilaga 1: Använda Spelman för att fånga HTTP- och HTTPS-trafik]."

Mer information om hur du använder Wireshark finns i "[Tillägg 2: Använda Wireshark för att fånga nätverkstrafik]."

Mer information om hur du använder Microsoft Message Analyzer finns i "[Bilaga 3: Använda Microsoft Message Analyzer för att samla in nätverkstrafik]."

## <a name="appendices"></a><a name="appendices"></a>Bilagor
Tilläggen beskriver flera verktyg som kan vara användbara när du diagnostiserar och felsöker problem med Azure Storage (och andra tjänster). Dessa verktyg är inte en del av Azure Storage och vissa är tredjepartsprodukter. Verktygen som beskrivs i dessa bilagor omfattas därför inte av något supportavtal som du kan ha med Microsoft Azure eller Azure Storage, och därför bör du som en del av utvärderingsprocessen undersöka vilka licensierings- och supportalternativ som är tillgängliga från leverantörer av dessa verktyg.

### <a name="appendix-1-using-fiddler-to-capture-http-and-https-traffic"></a><a name="appendix-1"></a>Tillägg 1: Använda Fiddler för att fånga HTTP- och HTTPS-trafik
[Fiddler](https://www.telerik.com/fiddler) är ett användbart verktyg för att analysera HTTP- och HTTPS-trafiken mellan klientprogrammet och den Azure-lagringstjänst som du använder.

> [!NOTE]
> Spelman kan avkoda HTTPS-trafik. Du bör läsa Fiddler-dokumentationen noggrant för att förstå hur den gör detta och förstå säkerhetskonsekvenserna.
>
>

Den här bilagan innehåller en kort genomgång av hur du konfigurerar Fiddler för att samla in trafik mellan den lokala datorn där du har installerat Fiddler och Azure-lagringstjänster.

När du har startat Fiddler börjar den fånga HTTP- och HTTPS-trafik på din lokala dator. Följande är några användbara kommandon för att styra Fiddler:

* Stanna och börja fånga trafik. Gå till **Arkiv** på huvudmenyn och klicka sedan på **Fånga trafik** för att växla till och från.
* Spara infångade trafikdata. Gå till **Arkiv**på huvudmenyn, klicka på **Spara**och klicka sedan på **Alla sessioner**: det gör att du kan spara trafiken i en sessionsarkivfil. Du kan läsa in ett sessionsarkiv igen senare för analys eller skicka det om det begärs till Microsoft-support.

Om du vill begränsa mängden trafik som Fiddler samlar in kan du använda filter som du konfigurerar på fliken **Filter.** Följande skärmbild visar ett filter som bara fångar trafik som skickas till **slutpunkten för contosoemaildist.table.core.windows.net** lagring:

![][5]

### <a name="appendix-2-using-wireshark-to-capture-network-traffic"></a><a name="appendix-2"></a>Tillägg 2: Använda Wireshark för att fånga nätverkstrafik
[Wireshark](https://www.wireshark.org/) är en nätverksprotokollanalysator som gör att du kan visa detaljerad paketinformation för ett brett spektrum av nätverksprotokoll.

Följande procedur visar hur du samlar in detaljerad paketinformation för trafik från den lokala datorn där du installerade Wireshark till tabelltjänsten i ditt Azure-lagringskonto.

1. Starta Wireshark på din lokala dator.
2. I avsnittet **Start** väljer du det eller de lokala nätverksgränssnitt som är anslutna till internet.
3. Klicka på **Insamlingsalternativ**.
4. Lägg till ett filter i textrutan **Insamlingsfilter.** **Värd contosoemaildist.table.core.windows.net** konfigurerar till exempel Wireshark så att endast paket som skickas till eller från tabelltjänstens slutpunkt i **contosoemaildist-lagringskontot.** Kolla in hela [listan över capture-filter](https://wiki.wireshark.org/CaptureFilters).

   ![][6]
5. Klicka på **Starta**. Wireshark kommer nu att fånga alla paket som skickas till eller från table service slutpunkten när du använder ditt klientprogram på din lokala dator.
6. När du är klar klickar du på **Fånga** på huvudmenyn och sedan **på Stopp**.
7. Om du vill spara de infångade data i en Wireshark Capture File klickar du på **Arkiv** på huvudmenyn och sedan **sparar**.

WireShark markerar alla fel som finns i **paketlistfönstret.** Du kan också använda fönstret **Expertinformation** (klicka på **Analysera**och sedan **expertinformation)** för att visa en sammanfattning av fel och varningar.

![][7]

Du kan också välja att visa TCP-data som programlagret ser det genom att högerklicka på TCP-data och välja **Följ TCP Stream**. Detta är användbart om du fångade din dump utan ett insamlingsfilter. Mer information finns i [Följande TCP-strömmar](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [!NOTE]
> Mer information om hur du använder Wireshark finns i [användarhandboken för Wireshark](https://www.wireshark.org/docs/wsug_html_chunked).
>
>

### <a name="appendix-3-using-microsoft-message-analyzer-to-capture-network-traffic"></a><a name="appendix-3"></a>Tillägg 3: Använda Microsoft Message Analyzer för att fånga nätverkstrafik
Du kan använda Microsoft Message Analyzer för att samla in HTTP- och HTTPS-trafik på ett liknande sätt som Fiddler och fånga nätverkstrafik på ett liknande sätt som Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Konfigurera en webbspårningssession med Microsoft Message Analyzer
Om du vill konfigurera en webbspårningssession för HTTP- och HTTPS-trafik med Microsoft Message Analyzer kör du Microsoft Message Analyzer-programmet och klickar sedan på **Fånga/spåra**på **Arkiv-menyn.** Välj **Webbproxy**i listan över tillgängliga spårningsscenarier . Lägg sedan till namnen på dina lagringsslutpunkter i textrutan **Trace Scenario** i textrutan Trace **Scenario** (du kan slå upp dessa namn i [Azure-portalen](https://portal.azure.com)). Om namnet på ditt Azure-lagringskonto till exempel är **contosodata**bör du lägga till följande i **textrutan HostnameFilter:**

```
contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
```

> [!NOTE]
> Ett blanksteg skiljer värdnamnen åt.
>
>

När du är redo att börja samla in spårningsdata klickar du på knappen **Börja med.**

Mer information om **webbproxyspårningen** för Microsoft Message Analyzer finns i [Microsoft-PEF-WebProxy Provider](https://technet.microsoft.com/library/jj674814.aspx).

Den inbyggda **webbproxyspårningen** i Microsoft Message Analyzer baseras på Fiddler. Det kan fånga https-trafik på klientsidan och visa okrypterade HTTPS-meddelanden. **Webbproxyspårningen** fungerar genom att konfigurera en lokal proxy för all HTTP- och HTTPS-trafik som ger den åtkomst till okrypterade meddelanden.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnostisera nätverksproblem med Microsoft Message Analyzer
Förutom att använda **webbproxyspårningen** i Microsoft Message Analyzer för att samla in information om HTTP/HTTPs-trafiken mellan klientprogrammet och lagringstjänsten, kan du också använda den inbyggda spårningen **för lokalt länklager** för att samla in information om nätverkspaket. På så sätt kan du samla in data som liknar dem som du kan samla in med Wireshark och diagnostisera nätverksproblem som ignorerade paket.

Följande skärmbild visar ett exempel på spårning **av lokalt länklager** med vissa **informationsmeddelanden** i kolumnen **Diagnostyper.** Om du klickar på en ikon i kolumnen **Diagnostyper** visas information om meddelandet. I det här exemplet skickade servern meddelandet #305 eftersom det inte fick någon bekräftelse från klienten:

![][9]

När du skapar spårningssessionen i Microsoft Message Analyzer kan du ange filter för att minska mängden brus i spårningen. På sidan **Fånga/ Spåra** där du definierar spårningen klickar du på länken **Konfigurera bredvid** **Microsoft-Windows-NDIS-PacketCapture**. Följande skärmbild visar en konfiguration som filtrerar TCP-trafik för IP-adresserna för tre lagringstjänster:

![][10]

Mer information om spårningen av lokallänklager i [Microsoft-PEF-NDIS-PacketCapture finns i Microsoft-PEF-NDIS-PacketCapture Provider](https://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4-using-excel-to-view-metrics-and-log-data"></a><a name="appendix-4"></a>Tillägg 4: Använda Excel för att visa mått och logga data
Med många verktyg kan du hämta lagringsmåttdata från Azure-tabelllagring i ett avgränsat format som gör det enkelt att läsa in data i Excel för visning och analys. Lagringsloggningsdata från Azure blob storage är redan i ett avgränsat format som du kan läsa in i Excel. Du måste dock lägga till lämpliga kolumnrubriker baserat på informationen i [tabellschemat för lagringsanalysloggformat](https://msdn.microsoft.com/library/azure/hh343259.aspx) och [lagringsanalys.](https://msdn.microsoft.com/library/azure/hh343264.aspx)

Så här importerar du lagringsloggningsdata till Excel när du har hämtat dem från blob-lagring:

* Klicka på Från **text**på **Data-menyn** .
* Bläddra till loggfilen som du vill visa och klicka på **Importera**.
* Välj **Avgränsad**i steg 1 i **guiden Textimport**.

I steg 1 i **guiden Textimport**väljer du **Semikolon** som enda avgränsare och väljer dubbelcitat som **textkvalificerare**. Klicka sedan på **Slutför** och välj var data ska placeras i arbetsboken.

### <a name="appendix-5-monitoring-with-application-insights-for-azure-devops"></a><a name="appendix-5"></a>Tillägg 5: Övervakning med programinsikter för Azure DevOps
Du kan också använda funktionen Application Insights för Azure DevOps som en del av din prestanda- och tillgänglighetsövervakning. Det här verktyget kan:

* Kontrollera att webbtjänsten är tillgänglig och svarar. Oavsett om din app är en webbplats eller en enhetsapp som använder en webbtjänst kan den testa webbadressen med några minuters mellanrum från platser runt om i världen och meddela dig om det finns ett problem.
* Diagnostisera snabbt eventuella prestandaproblem eller undantag i webbtjänsten. Ta reda på om CPU eller andra resurser sträcks ut, få stackspår från undantag och sök enkelt igenom loggspårningar. Om appens prestanda sjunker under acceptabla gränser kan Microsoft skicka ett e-postmeddelande till dig. Du kan övervaka både .NET- och Java-webbtjänster.

Du hittar mer information på [What is Application Insights](../../azure-monitor/app/app-insights-overview.md).

## <a name="next-steps"></a>Nästa steg

Mer information om analys i Azure Storage finns i följande resurser:

* [Övervaka ett lagringskonto i Azure-portalen](storage-monitor-storage-account.md)
* [Lagringsanalys](storage-analytics.md)
* [Mått för lagringsanalys](storage-analytics-metrics.md)
* [Tabellschema för lagringsanalysmått](/rest/api/storageservices/storage-analytics-metrics-table-schema)
* [Lagringsanalysloggar](storage-analytics-logging.md)
* [Loggformat för lagringsanalys](/rest/api/storageservices/storage-analytics-log-format)

<!--Anchors-->
[Introduktion]: #introduction
[Hur den här guiden är organiserad]: #how-this-guide-is-organized

[Övervaka din lagringstjänst]: #monitoring-your-storage-service
[Övervakning av tjänstens hälsa]: #monitoring-service-health
[Övervakningskapacitet]: #monitoring-capacity
[Övervaka tillgänglighet]: #monitoring-availability
[Övervaka prestanda]: #monitoring-performance

[Diagnostisera lagringsproblem]: #diagnosing-storage-issues
[Hälsofrågor för tjänster]: #service-health-issues
[Prestandaproblem]: #performance-issues
[Diagnostisera fel]: #diagnosing-errors
[Problem med lagringsemulatorn]: #storage-emulator-issues
[Verktyg för lagringsloggning]: #storage-logging-tools
[Använda verktyg för nätverksloggning]: #using-network-logging-tools

[Spårning från på slut- till-slut]: #end-to-end-tracing
[Korrelera loggdata]: #correlating-log-data
[ID för klientförfrågan]: #client-request-id
[ID för serverbegäran]: #server-request-id
[Tidsstämplar]: #timestamps

[Felsökningsvägledning]: #troubleshooting-guidance
[Mätningar visar ett högt värde för AverageE2ELatency och ett lågt värde för AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Mätningar visar låga värden för AverageE2ELatency och AverageServerLatency, men klienten har ändå långa svarstider]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Mätningar visar ett högt värde för AverageServerLatency]: #metrics-show-high-AverageServerLatency
[Du upplever oväntade fördröjningar i en köad meddelandeleverans]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Mätningar visar en ökning i PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Övergående ökning av PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Permanent ökning av procenttorlingerrorsfel]: #permanent-increase-in-PercentThrottlingError
[Mätningar visar en ökning i PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Mätningar visar en ökning i PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[Klienten får HTTP 403-meddelanden (Förbjudet)]: #the-client-is-receiving-403-messages
[Klienten får HTTP 404-meddelanden (Hittades inte)]: #the-client-is-receiving-404-messages
[Klienten eller en annan process har tagit bort objektet]: #client-previously-deleted-the-object
[Det är problem med SAS-autentiseringen (signatur för delad åtkomst)]: #SAS-authorization-issue
[JavaScript-koden på klientsidan har inte behörighet att komma åt objektet]: #JavaScript-code-does-not-have-permission
[Nätverksfel]: #network-failure
[Klienten får HTTP 409-meddelanden (Konflikt)]: #the-client-is-receiving-409-messages

[Mått visar låga procentkonsuccess eller analysloggposter har operationer med transaktionsstatus för ClientOtherErrors]: #metrics-show-low-percent-success
[Kapacitetsmått visar en oväntad ökning av användningen av lagringskapacitet]: #capacity-metrics-show-an-unexpected-increase
[Ditt problem uppstår från att använda lagringsemulatorn för utveckling eller test]: #your-issue-arises-from-using-the-storage-emulator
[Funktionen "X" fungerar inte i lagringsemulatorn]: #feature-X-is-not-working
[Fel "Värdet för ett av HTTP-huvudena är inte i rätt format" när lagringsemulatorn används]: #error-HTTP-header-not-correct-format
[Att köra lagringsemulatorn kräver administratörsbehörighet]: #storage-emulator-requires-administrative-privileges
[Du stöter på problem med att installera Azure SDK för .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Du har ett annat problem med en lagringstjänst]: #you-have-a-different-issue-with-a-storage-service

[Bilagor]: #appendices
[Tillägg 1: Använda Fiddler för att fånga HTTP- och HTTPS-trafik]: #appendix-1
[Tillägg 2: Använda Wireshark för att fånga nätverkstrafik]: #appendix-2
[Tillägg 3: Använda Microsoft Message Analyzer för att fånga nätverkstrafik]: #appendix-3
[Tillägg 4: Använda Excel för att visa mått och logga data]: #appendix-4
[Tillägg 5: Övervakning med programinsikter för Azure DevOps]: #appendix-5

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
