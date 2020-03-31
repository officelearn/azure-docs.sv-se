---
title: Anslutning till anslutning till anslutning till anslutning till nätverksprestandaövervakningslösning – Azure Log Analytics
description: Använd funktionen Service Connectivity Monitor i Network Performance Monitor för att övervaka nätverksanslutningen till alla slutpunkter som har en öppen TCP-port.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 93f3820b7cf1db85b9ff4cd514fe22efc75a90d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249248"
---
# <a name="service-connectivity-monitor"></a>Övervakare av tjänstanslutning

Du kan använda tjänsten Anslutningsövervakaren i [Network Performance Monitor](network-performance-monitor.md) för att övervaka nätverksanslutningen till alla slutpunkter som har en öppen TCP-port. Sådana slutpunkter inkluderar webbplatser, SaaS-program, PaaS-program och SQL-databaser. 

Du kan utföra följande funktioner med Service Connectivity Monitor: 

- Övervaka nätverksanslutningen till dina program och nätverkstjänster från flera filialkontor eller platser. Program och nätverkstjänster omfattar Office 365, Dynamics CRM, interna affärsprogram och SQL-databaser.
- Använd inbyggda tester för att övervaka nätverksanslutningen till Office 365- och Dynamics 365-slutpunkter. 
- Bestäm svarstid, nätverksfördröjning och paketförlust som uppstår vid anslutning till slutpunkten.
- Ta reda på om dålig programprestanda beror på nätverket eller på grund av något problem i programleverantörens.
- Identifiera aktiva punkter i nätverket som kan orsaka dåliga programprestanda genom att visa svarstiden som varje hopp bidrar med på en topologikarta.


![Övervakare av tjänstanslutning](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>Konfiguration 
Om du vill öppna konfigurationen för Network Performance Monitor öppnar du [lösningen För nätverksprestandaövervakare](network-performance-monitor.md) och väljer **Konfigurera**.

![Konfigurera Övervakare av nätverksprestanda](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>Konfigurera Log Analytics-agenter för övervakning
Aktivera följande brandväggsregler för de noder som används för övervakning så att lösningen kan identifiera topologin från noderna till tjänstslutpunkten: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Skapa serviceanslutningsövervakaretester 

Börja skapa dina tester för att övervaka nätverksanslutningen till tjänstens slutpunkter.

1. Välj fliken **Övervakare av tjänstanslutning.**
2. Välj **Lägg till test**och ange testnamn och beskrivning. Du kan skapa maximalt 450 tester per arbetsyta. 
3. Välj typ av test:<br>

    * Välj **Webben** om du vill övervaka anslutningen till en tjänst som svarar på HTTP/S-begäranden, till exempel outlook.office365.com eller bing.com.<br>
    * Välj **Nätverk** för att övervaka anslutningen till en tjänst som svarar på TCP-begäranden men inte svarar på HTTP/S-begäranden, till exempel en SQL-server, FTP-server eller SSH-port. 
    * Om du till exempel vill skapa ett webbtest på ett blob-lagringskonto väljer du **Webben** och anger mål som *ditt blob.core.windows.net.* På samma sätt kan du skapa tester för annan tabelllagring, kölagring och Azure-filer med den [här länken.](https://docs.microsoft.com/azure/storage/common/storage-account-overview#storage-account-endpoints)
4. Om du inte vill utföra nätverksmätningar, till exempel nätverksfördröjning, paketförlust och topologiidentifiering, avmarkerar du kryssrutan **Utför nätverksmätningar.** Håll den vald för att få maximal nytta av kapaciteten. 
5. I **Mål**anger du den URL/FQDN/IP-adress som du vill övervaka nätverksanslutningen till.
6. I **Portnummer**anger du måltjänstens portnummer. 
7. I **Testfrekvens**anger du ett värde för hur ofta du vill att testet ska köras. 
8. Välj de noder som du vill övervaka nätverksanslutningen till tjänsten från. Se till att antalet tillsatta ämnen per test är mindre än 150. Alla agenter kan testa maximalt 150 slutpunkter/agenter.

    >[!NOTE]
    > För Windows serverbaserade noder används TCP-baserade begäranden i funktionen för att utföra nätverksmätningarna. För Windows-klientbaserade noder använder funktionen ICMP-baserade begäranden för att utföra nätverksmätningarna. I vissa fall blockerar målprogrammet inkommande ICMP-baserade begäranden när noderna är Windows-klientbaserade. Lösningen kan inte utföra nätverksmätningar. Vi rekommenderar att du använder Windows serverbaserade noder i sådana fall. 

9. Om du inte vill skapa hälsohändelser för de objekt du väljer avmarkerar du **Aktivera hälsoövervakning i de mål som omfattas av det här testet**. 
10. Välj övervakningsvillkor. Du kan ange anpassade tröskelvärden för generering av hälsohändelseer genom att ange tröskelvärden. När värdet för villkoret överskrider det valda tröskelvärdet för det valda nätverket eller nätparet genereras en hälsohändelse. 
11. Välj **Spara** om du vill spara konfigurationen. 

    ![Testkonfigurationer för tjänstanslutningsövervakaren](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Genomgång 

Gå till instrumentpanelsvyn Nätverksprestandaövervakare. Om du vill få en sammanfattning av hälsotillståndet för de olika tester som du har skapat kan du titta på sidan **Service Connectivity Monitor.** 

![Sidan Övervakare för tjänstanslutning](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

Välj panelen om du vill visa information om testerna på sidan **Tester.** I tabellen till vänster kan du visa point-in-time-hälso- och värdet för tjänstens svarstid, nätverksfördröjning och paketförlust för alla tester. Använd kontrollen Nätverkstillståndsinspelaren för att visa nätverksögonblicksbilden vid ett annat tillfälle. Markera testet i tabellen som du vill undersöka. I diagrammen i fönstret till höger kan du visa den historiska trenden för värdena för förlust, svarstid och svarstid. Välj länken **Testinformation** om du vill visa prestanda från varje nod.

![Tester av tjänstanslutningsövervakaren](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

I vyn **Testnoder** kan du observera nätverksanslutningen från varje nod. Välj den nod som har prestandaförsämring. Detta är den nod där programmet observeras för att köra långsamt.

Ta reda på om dålig programprestanda beror på nätverket eller ett problem i programleverantörens genom att observera korrelationen mellan programmets svarstid och nätverksfördröjningen. 

* **Programproblem:** En topp i svarstiden men konsekvens i nätverksfördröjningen tyder på att nätverket fungerar bra och problemet kan bero på ett problem på programmets. 

    ![Programproblem för tjänstanslutningsövervakaren](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **Problem med nätverket:** En ökning av svarstiden tillsammans med en motsvarande ökning av nätverksfördröjningen tyder på att ökningen av svarstiden kan bero på en ökning av nätverksfördröjningen. 

    ![Problem med nätverksproblem för tjänstanslutningsövervakaren](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

När du har fastställt att problemet beror på nätverket väljer du länken **Topologi-vy** för att identifiera det besvärliga hoppet på topologikartan. Ett exempel visas i följande bild. Av den totala svarstiden på 105 ms mellan noden och programslutpunkten beror 96 ms på att hoppet är markerat med rött. När du har identifierat det besvärliga hoppet kan du vidta korrigerande åtgärder. 

![Tester av tjänstanslutningsövervakaren](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnostik 

Om du observerar en avvikelse följer du dessa steg:

* Om tjänstens svarstid, nätverksförlust och svarstid visas som NA kan en eller flera av följande orsaker vara orsaken:

    - Programmet är nere.
    - Noden som används för att kontrollera nätverksanslutningen till tjänsten är nere.
    - Målet som anges i testkonfigurationen är felaktigt.
    - Noden har ingen nätverksanslutning.

* Om en giltig svarstid för tjänsten visas men nätverksförlust och svarstid visas som NA, kan en eller flera av följande orsaker vara orsaken:

    - Om noden som används för att kontrollera nätverksanslutningen till tjänsten är en Windows-klientdator blockerar antingen måltjänsten ICMP-begäranden eller en nätverksbrandvägg blockerar ICMP-begäranden som kommer från noden.
    - Kryssrutan **Utför nätverksmätningar** är tom i testkonfigurationen. 

* Om tjänstens svarstid är NA men nätverksförlusten och svarstiden är giltiga kanske måltjänsten inte är ett webbprogram. Redigera testkonfigurationen och välj testtypen som **Nätverk** i stället för **webben**. 

* Om programmet går långsamt avgör du om dålig programprestanda beror på nätverket eller ett problem i programleverantörens.

## <a name="gcc-office-urls-for-us-government-customers"></a>GCC Office-url:er för kunder inom den amerikanska regeringen
För regionen US Government Virginia är endast DOD-url:er inbyggda NPM. Kunder som använder GCC-url:er måste skapa anpassade tester och lägga till varje webbadress individuellt.

| Field | GCC |
|:---   |:--- |
| Office 365 Portal och delad | portal.apps.mil |
| Office 365-autentisering och identitet | * login.microsoftonline.us <br> * api.login.microsoftonline.com <br> * clientconfig.microsoftonline-p.net <br> * login.microsoftonline.com <br> * login.microsoftonline-p.com <br> * login.windows.net <br> * loginex.microsoftonline.com <br> * login-us.microsoftonline.com <br> * nexus.microsoftonline-p.com <br> * mscrl.microsoft.com <br> * secure.aadcdn.microsoftonline-p.com |
| Office Online | * adminwebservice.gov.us.microsoftonline.com <br>  * adminwebservice-s1-bn1a.microsoftonline.com <br> * adminwebservice-s1-dm2a.microsoftonline.com <br> * becws.gov.us.microsoftonline.com <br> * provisioningapi.gov.us.microsoftonline.com <br> * officehome.msocdn.us <br> * prod.msocdn.us <br> * portal.office365.us <br> * webshell.suite.office365.us <br> * www .office365.us <br> * activation.sls.microsoft.com <br> * crl.microsoft.com <br> * go.microsoft.com <br> * insertmedia.bing.office.net <br> * ocsa.officeapps.live.com <br> * ocsredir.officeapps.live.com <br> * ocws.officeapps.live.com <br> * office15client.microsoft.com <br>* officecdn.microsoft.com <br> * officecdn.microsoft.com.edgesuite.net <br> * officepreviewredir.microsoft.com <br> * officeredir.microsoft.com <br> * ols.officeapps.live.com  <br> * r.office.microsoft.com <br> * cdn.odc.officeapps.live.com <br> * odc.officeapps.live.com <br> * officeclient.microsoft.com |
| exchange online | * outlook.office365.us <br> * attachments.office365-net.us <br> * autodiscover-s.office365.us <br> * manage.office365.us <br> * scc.office365.us |
| MS-team | gov.teams.microsoft.us | 

## <a name="next-steps"></a>Nästa steg
[Sök loggar](../../azure-monitor/log-query/log-query-overview.md) för att visa detaljerade dataposter för nätverksprestanda.
