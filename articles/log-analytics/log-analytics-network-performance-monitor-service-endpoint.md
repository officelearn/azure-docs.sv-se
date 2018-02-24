---
title: "Network Performance Monitor-lösning i Azure Log Analytics | Microsoft Docs"
description: "Tjänsten Endpoint Manager-funktion i Prestandaövervakaren för nätverket kan du övervaka nätverksanslutning till någon slutpunkt som har en öppen TCP-port."
services: log-analytics
documentationcenter: 
author: abshamsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 7eb31b91480b6e57135581cfa2f5503de3189e10
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
---
# <a name="service-endpoint-manager"></a>Service Endpoint Manager

Tjänsthanteraren för slutpunkt-funktionen i [Network Performance Monitor](log-analytics-network-performance-monitor.md) kan du övervaka nätverksanslutning till någon slutpunkt som har en öppen TCP-port. Dessa slutpunkter är webbplatser, SaaS-program, PaaS-program och SQL-databaser. 

Du kan utföra följande funktioner med **Endpoint Tjänstövervakare**: 

- Övervaka nätverksanslutningen till dina program och tjänster för nätverk (till exempel Office 365, Dynamics CRM interna branschspecifika affärsprogram, SQL-databas o.s.v.) från flera gren kontor platser 
- Inbyggda tester nätverksanslutning till Office 365 och Dynamics365 slutpunkter 
- Fastställa svarstid, Nätverksfördröjningen, paketförlust inträffade vid anslutning till slutpunkten 
- Avgöra om dåliga programmens prestanda är på grund av nätverket eller på grund av vissa problem i slutet av providern för programmet 
- Identifiera aktiva punkter i nätverket som kan orsaka dåliga programmens prestanda genom att visa svarstid som har bidragit med varje hopp på en topologisk karta. 


![Tjänsten Endpoint för övervakning](media/log-analytics-network-performance-monitor/service-endpoint-intro.png)


## <a name="configuration"></a>Konfiguration 
För att öppna konfigurationen för Network Performance Monitor, öppna den [Network Performance Monitor lösning](log-analytics-network-performance-monitor.md) och klicka på den **konfigurera** knappen.

![Konfigurera nätverket Prestandaövervakaren](media/log-analytics-network-performance-monitor/npm-configure-button.png)


### <a name="configure-oms-agents-for-the-monitoring"></a>Konfigurera OMS-agenter för övervakning.  
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

Börja skapa dina tester för att övervaka nätverksanslutning till Tjänsteslutpunkter 

1. Klicka på den **Endpoint Tjänstövervakare** fliken.
2. Klicka på **lägga till Test** och ange Test namn och beskrivning. 
3. Välj typ av test:<br>Välj **webbtest** om du övervakar anslutning till en tjänst som svarar på HTTP/S-begäranden, till exempel outlook.office365.com, bing.com.<br>Välj **nätverkstest** om du övervakar anslutning till en tjänst som svarar på TCP-begäran men inte svarar på HTTP/S-begäran, till exempel en SQLServer FTP-servern, SSH-port etc. 
4. Om du inte vill utföra nätverket mått (Nätverksfördröjningen, paketförlust topologi identifiering) avmarkerar du textrutan. Vi rekommenderar att du behåller det kontrolleras för att få största möjliga nytta av kapaciteten. 
5. Ange mål-URL/FQDN/IP-adress som du vill övervaka nätverksanslutning.  
6. Ange portnumret för Måltjänsten. 
7. Ange hur ofta du vill testa att köra. 
8. Markera de noder som du vill övervaka nätverksanslutningen till tjänsten. 

    >[!NOTE]
    > För Windows server-baserade noder använder kapaciteten för TCP-baserade begäranden för att utföra nätverket mått. För Windows-klientbaserad noder använder kapaciteten ICMP-baserade begäranden för att utföra nätverket mått. I vissa fall blockerar målprogrammet inkommande ICMP-baserade begäran på grund av som när noderna är Windows klientbaserade, lösningen kan inte utföra nätverket mått. Därför bör du använda Windows server-baserade noder i sådana fall. 

9. Om du inte vill skapa hälsa händelser för de objekt som du har valt, sedan avmarkera **aktivera övervakning av hälsotillstånd på mål som omfattas av det här testet**. 
10. Välj övervakning villkor. Du kan ange anpassade tröskelvärden för hälsotillstånd händelse genereras genom att skriva tröskelvärden. När värdet för villkoret går över dess valda tröskelvärdet för det valda nätverk/undernätverk paret, skapas en hälsohändelse. 
11. Klicka på **spara** att spara konfigurationen. 

 ![Tjänsten Endpoint Monitor-konfiguration](media/log-analytics-network-performance-monitor/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Genomgång 

Flytta till instrumentpanelsvy prestandaövervakning av nätverk och se den **Endpoint Tjänstövervakare** sidan för att få en sammanfattning av hälsotillståndet för de olika tester som du har skapat.  

![Tjänsten Endpoint övervakaren sida](media/log-analytics-network-performance-monitor/service-endpoint-blade.png)

Klicka på panelen nedåt och visa information om testerna på den **testerna** sidan. Du kan visa i tidpunkt hälsotillstånd och värdet av svarstid för tjänsten, nätverks-svarstid och paketförlust för alla test på tabellen LHS. Du kan använda nätverket tillstånd lådan kontrollen visas nätverk på en annan tid i tidigare. Klicka på Testa i tabellen som du vill undersöka. Du kan visa historiska trender för dataförlust, svarstid och svar tidsvärden från diagrammen i rutan till höger. Klicka på länken Test information om du vill visa prestanda från varje nod. 

![Tester för tjänsten Endpoint övervakning](media/log-analytics-network-performance-monitor/service-endpoint-tests.png)

På den **Test noder** vy, kan du se nätverksanslutning mellan varje nod. Klicka på noden med försämrade prestanda.  Det här är en nod från där programmet observeras körs långsamt. 

Avgöra om dåliga programmens prestanda är på grund av nätverket eller på grund av vissa problem i slutet av providern programmet genom att följa sambandet mellan svarstiden för programmet och nätverks-svarstid- 

**Problem med programmet:** om det är en topp i svarstid, men nätverkslatensen är konsekvent och sedan det tyder på att det fungerar bra och problemet beror på ett problem i programmet slutet.  

![Problem med tjänsten Endpoint övervaka programmet](media/log-analytics-network-performance-monitor/service-endpoint-application-issue.png)

**Nätverk problemet:** om en topp svarstid tillsammans med en motsvarande topp i Nätverks-svarstid, så det tyder på att ökningen av svarstiden är på grund av en ökning av svarstid för nätverk.  

![Tjänsten Endpoint övervakaren nätverksproblem](media/log-analytics-network-performance-monitor/service-endpoint-network-issue.png)

När du har bestämt att problemet ligger på grund av nätverket, kan du klicka på den **topologi** visningslänk för att identifiera program som krånglar hopp på topologisk karta. Till exempel kan i bilden nedan du se att 96 ms är utanför den totala svarstiden 105 ms mellan noden och programslutpunkten, på grund av hopp markerat i rött. När du har hittat program som krånglar hopp, kan du vidta lämpliga åtgärder.  

![Tester för tjänsten Endpoint övervakning](media/log-analytics-network-performance-monitor/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnostik 

Om du upptäcker en avvikelse, följer du dessa steg:

Om svarstid för tjänsten nätverksförluster och fördröjning visas som saknas, det kan vara på grund av en eller flera av följande skäl:
- Programmet är inte tillgänglig.
- Noden som används för att kontrollera nätverksanslutningen till tjänsten har stoppats.
- Målet anges i test-konfigurationen är felaktig.
- Noden har inte någon nätverksanslutning.

Om en giltig svarstid visas men nätverksförluster samt latens visas som NA, kan det vara på grund av en eller flera av följande skäl:
- Om den nod som används för att kontrollera nätverksanslutningen till tjänsten Windows-klientdatorn Måltjänsten blockerar ICMP-begäranden, eller en nätverksbrandvägg blockerar ICMP-begäranden från noden.
- Kryssrutan för **utföra nätverket mätningar** har avmarkerats i test-konfigurationen. 

Om tjänsten svarstiden är NA men nätverksförluster samt svarstiden är giltiga, sedan föreslår den att Måltjänsten inte är ett webbprogram. Redigera test-konfigurationen och välj testtypen som nätverkstest i stället för webbtestet. 

Om programmet körs långsamt, bör du kontrollera om dåliga programmens prestanda är på grund av nätverket eller på grund av vissa problem i slutet av providern för programmet.


## <a name="next-steps"></a>Nästa steg
* [Söka i loggar](log-analytics-log-searches.md) att visa detaljerad nätverket prestanda dataposter.
