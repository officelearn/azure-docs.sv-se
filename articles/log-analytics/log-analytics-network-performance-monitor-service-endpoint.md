---
title: Network Performance Monitor-lösning i Azure Log Analytics | Microsoft Docs
description: Använda tjänsthanteraren för slutpunkt-funktionen i nätverket Prestandaövervakaren för att övervaka nätverksanslutning till någon slutpunkt som har en öppen TCP-port.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: b21d711e59ddc762eaf72f49e501d9f324d75105
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="service-endpoint-monitor"></a>Tjänsten Endpoint för övervakning

Du kan använda tjänsten Endpoint bildskärmar i [Network Performance Monitor](log-analytics-network-performance-monitor.md) att övervaka nätverksanslutning till någon slutpunkt som har en öppen TCP-port. Dessa slutpunkter är webbplatser, SaaS-program, PaaS-program och SQL-databaser. 

Du kan utföra följande funktioner med tjänsten Endpoint Övervakare: 

- Övervaka nätverksanslutningen till dina program och tjänster från flera kontor och avdelningskontor. Program och tjänster för nätverk innehåller Office 365, Dynamics CRM, interna line-of-business-program och SQL-databaser.
- Använd inbyggda testerna för att övervaka nätverksanslutning till Office 365 och Dynamics 365 slutpunkter. 
- Fastställa svarstid, nätverks-svarstid och paketförlust erfarna när du ansluter till slutpunkten.
- Fastställ om dåliga prestanda på grund av nätverket eller på grund av vissa problem i programmet leverantörens end.
- Identifiera aktiva punkter i nätverket som kan orsaka dåliga programmens prestanda genom att visa svarstid som har bidragit med varje hopp på en topologisk karta.


![Tjänsten Endpoint för övervakning](media/log-analytics-network-performance-monitor/service-endpoint-intro.png)


## <a name="configuration"></a>Konfiguration 
För att öppna konfigurationen för Network Performance Monitor, öppna den [Network Performance Monitor lösning](log-analytics-network-performance-monitor.md) och välj **konfigurera**.

![Konfigurera nätverket Prestandaövervakaren](media/log-analytics-network-performance-monitor/npm-configure-button.png)


### <a name="configure-operations-management-suite-agents-for-monitoring"></a>Konfigurera Operations Management Suite-agenter för övervakning
Aktivera följande brandväggsregler på alla noder som används för att övervaka så att lösningen kan identifiera topologin från noderna till tjänstslutpunkten: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action 
```

### <a name="create-service-endpoint-monitor-tests"></a>Skapa tester för tjänsten Endpoint övervakning 

Börja skapa dina tester för att övervaka nätverksanslutning till Tjänsteslutpunkter.

1. Välj den **Endpoint Tjänstövervakare** fliken.
2. Välj **lägga till testa**, och ange test namn och beskrivning. 
3. Välj typ av test:<br>

    * Välj **Web** att övervaka anslutning till en tjänst som svarar på HTTP/S-begäranden, till exempel outlook.office365.com eller bing.com.<br>
    * Välj **nätverk** att övervaka anslutning till en tjänst som svarar på TCP-förfrågningar men inte svarar på HTTP/S-begäranden, till exempel en SQLServer, FTP-servern eller SSH-port. 
4. Ta bort om du inte vill utföra nätverket mått, till exempel svarstid för nätverk och paketförlust topologi identifiering av **utföra nätverket mätningar** kryssrutan. Behåll den markerad för att få största möjliga nytta av kapaciteten. 
5. I **mål**, ange IP-URL-FQDN-adressen som du vill övervaka nätverksanslutning.
6. I **portnummer**, ange portnumret för Måltjänsten. 
7. I **testa frekvens**, ange ett värde för hur ofta du vill testa att köra. 
8. Markera de noder som du vill övervaka nätverksanslutningen till tjänsten. 

    >[!NOTE]
    > För Windows server-baserade noder använder kapaciteten för TCP-baserade begäranden för att utföra nätverket mått. För Windows-klientbaserad noder använder kapaciteten ICMP-baserade begäranden för att utföra nätverket mått. I vissa fall blockerar målprogrammet inkommande ICMP-baserade begäranden när noderna är Windows-baserad klient. Lösningen är går inte att utföra nätverket mått. Vi rekommenderar att du använder Windows server-baserade noder i sådana fall. 

9. Om du inte vill skapa hälsa händelser för objekt du väljer, rensa **aktivera övervakning av Säkerhetshälsa i mål som omfattas av det här testet**. 
10. Välj övervakning villkor. Du kan ange anpassade tröskelvärden för health-händelse genereras genom att ange tröskelvärden. När värdet för villkoret går över dess valda tröskelvärdet för det valda nätverket eller undernätverk par, skapas en hälsohändelse. 
11. Välj **spara** att spara konfigurationen. 

    ![Tjänsten Endpoint övervakning test konfigurationer](media/log-analytics-network-performance-monitor/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Genomgång 

Gå till vyn Network Performance Monitor-instrumentpanelen. För att få en översikt över hälsotillståndet för de olika tester som du har skapat kan du titta på den **Endpoint Tjänstövervakare** sidan. 

![Tjänsten Endpoint övervakning sida](media/log-analytics-network-performance-monitor/service-endpoint-blade.png)

Markera rutan för att visa information om testerna på den **testerna** sidan. Du kan visa i tidpunkt hälsotillstånd och värdet av svarstid för tjänsten, nätverks-svarstid och paketförlust för alla tester i tabellen till vänster. Använd nätverket tillstånd lådan kontrollen om du vill visa nätverk ögonblicksbilden vid ett senare tillfälle i förflutna. Välj testet i tabellen som du vill undersöka. Du kan visa historiska trender för dataförlust, svarstid och svar tidsvärden i diagram i rutan till höger. Välj den **Test information** länken för att visa prestanda från varje nod.

![Tester för tjänsten Endpoint övervakning](media/log-analytics-network-performance-monitor/service-endpoint-tests.png)

I den **Test noder** vy, kan du se nätverksanslutning mellan varje nod. Välj den nod som har försämrade prestanda. Detta är den nod där programmet observeras körs långsamt.

Fastställ om dåliga prestanda på grund av nätverket eller ett problem i programmet leverantörens end genom att följa sambandet mellan svarstiden för programmet och nätverkslatensen. 

* **Problem med programmet:** en topp i antal svarstiden men konsekvens i nätverkslatensen föreslår att det fungerar bra och problemet kan bero på ett problem i program-end. 

    ![Tjänsten Endpoint övervakning programproblem](media/log-analytics-network-performance-monitor/service-endpoint-application-issue.png)

* **Nätverk problemet:** en topp svarstid tillsammans med en motsvarande topp i Nätverksfördröjningen föreslår att ökningen av svarstiden kan bero på en ökning av svarstid för nätverk. 

    ![Tjänsten Endpoint övervakning nätverksproblem](media/log-analytics-network-performance-monitor/service-endpoint-network-issue.png)

När du har bestämt att problemet ligger på grund av nätverket, Välj den **topologi** visningslänk för att identifiera program som krånglar hopp på topologisk karta. Ett exempel visas i följande bild. Out-of-total svarstid 105 ms mellan noden och programmet slutpunkten är 96 ms på grund av hopp markerat i rött. När du har identifierat de program som krånglar hopp kan du vidta lämpliga åtgärder. 

![Tester för tjänsten Endpoint övervakning](media/log-analytics-network-performance-monitor/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnostik 

Om du upptäcker en avvikelse, gör du följande:

* Om svarstid för tjänsten, nätverksförluster och fördröjning visas som NA, kan en eller flera av följande orsaker vara orsaken:

    - Programmet är inte tillgänglig.
    - Den nod som används för att kontrollera nätverksanslutningen till tjänsten har stoppats.
    - Målet anges i test-konfigurationen är felaktig.
    - Noden har inte någon nätverksanslutning.

* Om en giltig svarstid visas men nätverksförluster samt latens visas som NA, kan en eller flera av följande orsaker vara orsaken:

    - Om den nod som används för att kontrollera nätverksanslutningen till tjänsten är en Windows-klientdator, Måltjänsten blockerar ICMP-begäranden, eller en nätverksbrandvägg blockerar ICMP-förfrågningar som kommer från noden.
    - Den **utföra nätverket mätningar** kryssrutan är tom i test-konfigurationen. 

* Om tjänsten svarstiden är NA men nätverksförluster samt svarstiden är giltiga, kanske inte Måltjänsten ett webbprogram. Redigera test-konfigurationen och välj testtypen som **nätverk** i stället för **Web**. 

* Om programmet körs långsamt kan du avgöra om dåliga programmens prestanda är på grund av nätverket eller ett problem i programmet leverantörens end.


## <a name="next-steps"></a>Nästa steg
[Söka i loggar](log-analytics-log-searches.md) att visa detaljerad nätverket prestanda dataposter.
