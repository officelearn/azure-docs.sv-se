---
title: Så här felsöker du problem med Log Analytics agent för Windows | Microsoft Docs
description: Beskriv symptom, orsaker och lösningar för de vanligaste problemen med Log Analytics agent för Windows i Azure Monitor.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2019
ms.author: magoedte
ms.openlocfilehash: 96f020f24e27ff799f9bfbc08d899e8375b86094
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72431819"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Så här felsöker du problem med Log Analytics agent för Windows 

Den här artikeln innehåller fel söknings fel som kan uppstå i Log Analytics-agenten för Windows i Azure Monitor och ger förslag på möjliga lösningar för att lösa dem.

Om inget av dessa steg fungerar för dig, är följande Support kanaler också tillgängliga:

* Kunder med Premier Support-förmåner kan öppna en support förfrågan med [Premier](https://premier.microsoft.com/).
* Kunder med support avtal för Azure kan öppna en support förfrågan [i Azure Portal](https://manage.windowsazure.com/?getsupport=true).
* Besök sidan Log Analytics feedback för att granska skickade idéer och buggar [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) eller en ny fil. 

## <a name="important-troubleshooting-sources"></a>Viktiga fel söknings källor

 För att hjälpa till med fel sökning av problem som rör Log Analytics agent för Windows loggar agenten händelser till händelse loggen i Windows, särskilt under *program-och Services\Operations Manager*.  

## <a name="connectivity-issues"></a>Anslutningsproblem

Om agenten kommunicerar via en proxyserver eller brand vägg kan det finnas begränsningar för att förhindra kommunikation från käll datorn och tjänsten Azure Monitor. Om kommunikationen är blockerad, på grund av felaktig konfiguration, kan registrering med en arbets yta Miss lyckas vid försök att installera agenten eller konfigurera agenten efter installationen så att den rapporterar till en annan arbets yta. Agent kommunikation kan Miss lyckas efter en lyckad registrering. I det här avsnittet beskrivs metoderna för att felsöka den här typen av problem med Windows-agenten.

Kontrol lera att brand väggen eller proxyservern har kon figurer ATS för att tillåta följande portar och URL: er som beskrivs i följande tabell. Bekräfta också att HTTP-kontroll inte har Aktiver ATS för webb trafik, eftersom den kan förhindra en säker TLS-kanal mellan agenten och Azure Monitor.  

|Agentresurs|Portar |Riktning |Kringgå HTTPS-kontroll|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Utgående|Ja |  
|*.oms.opinsights.azure.com |Port 443 |Utgående|Ja |  
|*.blob.core.windows.net |Port 443 |Utgående|Ja |  
|*.azure-automation.net |Port 443 |Utgående|Ja |  

För brand Väggs information som krävs för Azure Government, se [Azure Government hantering](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Det finns flera sätt som du kan kontrol lera om agenten lyckas kommunicera med Azure Monitor.

- Aktivera [Azure Log Analytics agenthälsa-utvärdering](../insights/solution-agenthealth.md) på arbets ytan. På instrument panelen Agenthälsa visar du kolumnen **antal agenter** som inte svarar för att snabbt se om agenten visas.  

- Kör följande fråga för att bekräfta att agenten skickar ett pulsslag till arbets ytan som den är konfigurerad att rapportera till. Ersätt `<ComputerName>` med datorns faktiska namn.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Om datorn lyckas kommunicera med tjänsten ska frågan returnera ett resultat. Om frågan inte returnerade ett resultat ska du först kontrol lera att agenten har kon figurer ATS för att rapportera till rätt arbets yta. Om den har kon figurer ATS korrekt går du till steg 3 och söker i Windows-händelseloggen för att identifiera om agenten loggar in vilket problem som kan förhindra att den kommunicerar med Azure Monitor.

- En annan metod för att identifiera ett anslutnings problem är genom att köra verktyget **TestCloudConnectivity** . Verktyget installeras som standard med agenten i mappen *%systemroot%\Program Files\Microsoft Monitoring Agent\Agent*. Från en upphöjd kommando tolk navigerar du till mappen och kör verktyget. Verktyget returnerar resultaten och högdagrar där testet misslyckades (till exempel om det var relaterat till en viss port/URL som blockerades). 

    ![TestCloudConnection verktygs körnings resultat](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filtrera *Operations Manager* händelse loggen efter **händelse källor** - *Hälsotjänst moduler*, *HealthService*och *service Connector* och filtrera efter **händelse nivå** *Varning* och *fel* till bekräfta om den har skrivna händelser från följande tabell. Om de är det, granskar du lösnings stegen som ingår för varje möjlig händelse.

    |Händelse-ID |Källa |Beskrivning |Upplösning |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Hälsotjänst |Det gick inte att ansluta till tjänsten från agenten |Felet kan uppstå om agenten inte kan kommunicera direkt eller via en brand vägg/proxyserver till Azure Monitor tjänsten. Verifiera inställningarna för agent proxy eller att nätverks brand väggen/proxyn tillåter TCP-trafik från datorn till tjänsten.|
    |2138 |Hälsotjänst moduler |Proxy kräver autentisering |Konfigurera agentens proxyinställningar och ange det användar namn/lösen ord som krävs för att autentisera med proxyservern. |
    |2129 |Hälsotjänst moduler |Misslyckad anslutning/misslyckad SSL-förhandling |Kontrol lera inställningarna för TCP/IP för nätverkskortet och agentens proxyinställningar.|
    |2127 |Hälsotjänst moduler |Fel vid sändning av mottagna data felkod |Om det bara inträffar regelbundet under dagen kan det vara en slumpmässig avvikelse som kan ignoreras. Övervaka för att förstå hur ofta det sker. Om det inträffar ofta under dagen kontrollerar du först nätverks konfigurationen och proxyinställningarna. Om beskrivningen inkluderar HTTP-felkod 404 och det är första gången som agenten försöker skicka data till tjänsten, kommer den att innehålla ett 500-fel med en inre 404-felkod. 404 betyder att lagrings området för den nya arbets ytan fortfarande håller på att tillhandahållas. Vid nästa försök kommer data att skrivas till arbets ytan som förväntat. Ett HTTP-fel 403 kan tyda på problem med behörighet eller autentiseringsuppgifter. Det finns mer information i 403-fel som hjälper dig att felsöka problemet.|
    |4000 |Service Connector |DNS-namnmatchning misslyckades |Datorn kunde inte matcha Internet adressen som användes när data skickades till tjänsten. Detta kan vara inställningar för DNS-matchare på datorn, felaktiga proxyinställningar eller kanske ett tillfälligt DNS-problem med leverantören. Om det inträffar regelbundet kan det bero på ett tillfälligt nätverksrelaterade problem.|
    |4001 |Service Connector |Det gick inte att ansluta till tjänsten. |Felet kan uppstå om agenten inte kan kommunicera direkt eller via en brand vägg/proxyserver till Azure Monitor tjänsten. Verifiera inställningarna för agent proxy eller att nätverks brand väggen/proxyn tillåter TCP-trafik från datorn till tjänsten.|
    |4002 |Service Connector |Tjänsten returnerade HTTP-statuskod 403 som svar på en fråga. Kontakta tjänst administratören för hälso tillståndet för tjänsten. Frågan kommer att göras senare. |Det här felet skrivs under agentens inledande registrerings fas och du ser en URL som liknar följande: *https://\<workspaceID >. OMS. OpInsights. Azure. com/AgentService. svc/AgentTopologyRequest*. Felkoden 403 betyder att den är förbjuden och kan orsakas av ett felanget arbetsyte-ID eller nyckel, eller så är data och tid felaktigt på datorn. Om tiden är +/-15 minuter från aktuell tid, Miss lyckas onboarding. Korrigera detta genom att uppdatera datum och/eller tidszon för Windows-datorn.|

## <a name="data-collection-issues"></a>Problem med data insamling

När agenten har installerats och rapporter ATS till den konfigurerade arbets ytan eller arbets ytorna kan det sluta ta emot konfiguration, insamling eller vidarebefordran av prestanda, loggar eller annan information till tjänsten beroende på vad som är aktiverat och mål datorn. Det är nödvändigt att fastställa om:

- Är det en specifik datatyp eller alla data som inte är tillgängliga i arbets ytan?
- Är data typen som anges av en lösning eller anges som en del av konfigurationen av data insamling för arbets yta?
- Hur många datorer påverkas? Är det en enstaka eller flera datorer som rapporterar till arbets ytan?
- Fungerade den och slutade den vid en viss tidpunkt, eller har den aldrig samlats in? 
- Är loggs öknings frågan du använder syntaktiskt korrekt? 
- Har agenten någonsin tagit emot sin konfiguration från Azure Monitor?

Det första steget i fel sökning är att avgöra om datorn skickar en pulsslags händelse.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Om frågan returnerar resultat måste du bestämma om en viss datatyp inte samlas in och vidarebefordras till tjänsten. Detta kan orsakas av att agenten inte tar emot en uppdaterad konfiguration från tjänsten, eller något annat problem som hindrar agenten från att fungera normalt. Utför följande steg för att ytterligare felsöka.

1. Öppna en kommando tolk med förhöjd behörighet på datorn och starta om Agent tjänsten genom att skriva `net stop healthservice && net start healthservice`.
2. Öppna händelse loggen *Operations Manager* och Sök efter **händelse-id** *7023, 7024, 7025, 7028* och *1210* från **händelse källan** *HealthService*.  Dessa händelser anger att agenten tar emot konfiguration från Azure Monitor och att datorn övervakas aktivt. Händelse beskrivningen för händelse-ID 1210 anges också på den sista raden alla lösningar och insikter som ingår i övervaknings området på agenten.  

    ![Beskrivning av händelse-ID 1210](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Om du efter flera minuter inte ser oväntade data i frågeresultatet eller visualiseringen, beroende på om du visar data från en lösning eller insikter, kan du söka efter **händelse källor** HealthService i händelse loggen *Operations Manager*och *Hälsotjänst moduler* och filtrera efter **händelse nivå** *Varning* och *fel* för att bekräfta om den har skrivna händelser från följande tabell.

    |Händelse-ID |Källa |Beskrivning |Upplösning |
    |---------|-------|------------|
    |8000 |HealthService |Den här händelsen anger om ett arbets flöde som rör prestanda, händelse eller annan insamlad datatyp inte kan vidarebefordra till tjänsten för inmatning till arbets ytan. | Händelse-ID 2136 från käll-HealthService skrivs tillsammans med den här händelsen och kan tyda på att agenten inte kan kommunicera med tjänsten, eventuellt på grund av felaktig konfiguration av proxy-och autentiseringsinställningarna, nätverks avbrott eller nätverks brand väggen/ Proxy tillåter inte TCP-trafik från datorn till tjänsten.| 
    |10102 och 10103 |Hälsotjänst moduler |Det gick inte att matcha data källan i arbets flödet. |Detta kan inträffa om den angivna prestanda räknaren eller instansen inte finns på datorn eller felaktigt har definierats i data inställningarna för arbets ytan. Om det här är en användardefinierad [prestanda räknare](data-sources-performance-counters.md#configuring-performance-counters)kontrollerar du att den angivna informationen följer rätt format och finns på mål datorerna. |
    |26002 |Hälsotjänst moduler |Det gick inte att matcha data källan i arbets flödet. |Detta kan inträffa om den angivna Windows-händelseloggen inte finns på datorn. Det här felet kan ignoreras om datorn inte förväntas ha den här händelse loggen registrerad, annars om det är en användardefinierad [händelse logg](data-sources-windows-events.md#configuring-windows-event-logs)kontrollerar du att den angivna informationen är korrekt. |

