---
title: Felsöka problem med Log Analytics-agent för Windows
description: Beskriv symptom, orsaker och lösning för de vanligaste problemen med Log Analytics-agenten för Windows i Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 4112555347ce1d718375fbab3f166c6f2f5deeaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333512"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Felsöka problem med Log Analytics-agenten för Windows 

Den här artikeln innehåller hjälp med felsökning av fel som kan uppstå med Log Analytics-agenten för Windows i Azure Monitor och föreslår möjliga lösningar för att lösa dem.

Om inget av dessa steg fungerar för dig är även följande supportkanaler tillgängliga:

* Kunder med Premier-supportförmåner kan öppna en supportbegäran med [Premier](https://premier.microsoft.com/).
* Kunder med Azure-supportavtal kan öppna en supportbegäran [i Azure-portalen](https://manage.windowsazure.com/?getsupport=true).
* Besök log analytics feedback-sidan för att [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) granska inskickade idéer och buggar eller fil en ny. 

## <a name="important-troubleshooting-sources"></a>Viktiga felsökningskällor

 Agenten loggar händelser till Windows-händelseloggen för att hjälpa till med felsökningsproblem relaterade till Log Analytics-agenten för *Windows.*  

## <a name="connectivity-issues"></a>Anslutningsproblem

Om agenten kommunicerar via en proxyserver eller brandvägg kan det finnas begränsningar som förhindrar kommunikation från källdatorn och Azure Monitor-tjänsten. Om kommunikationen blockeras kan registrering med en arbetsyta misslyckas när agenten försöker installera agenten eller konfigurera agentefterinställningen för att rapportera till ytterligare en arbetsyta på grund av felkonfiguration. Agentkommunikation kan misslyckas efter lyckad registrering. I det här avsnittet beskrivs metoderna för att felsöka den här typen av problem med Windows-agenten.

Dubbelkolla att brandväggen eller proxyn är konfigurerad så att följande portar och webbadresser som beskrivs i följande tabell tillåts. Bekräfta också http-inspektion inte är aktiverad för webbtrafik, eftersom det kan förhindra en säker TLS-kanal mellan agenten och Azure Monitor.  

|Agentresurs|Portar |Riktning |Kringgå HTTPS-kontroll|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Utgående|Ja |  
|*.oms.opinsights.azure.com |Port 443 |Utgående|Ja |  
|*.blob.core.windows.net |Port 443 |Utgående|Ja |  

Brandväggsinformation som krävs för Azure Government finns i [Azure Government management](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). Om du planerar att använda Azure Automation Hybrid Runbook Worker för att ansluta till och registrera dig med Tjänsten Automation för att använda runbooks eller hanteringslösningar i din miljö, måste den ha åtkomst till portnumret och webbadresserna som beskrivs i [Konfigurera nätverket för Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Det finns flera sätt att verifiera om agenten har kommunicerat med Azure Monitor.

- Aktivera [Azure Log Analytics Agent Health-utvärderingen](../insights/solution-agenthealth.md) på arbetsytan. Från instrumentpanelen Agent Hälsa kan du visa kolumnen **Antal agenter som inte svarar** för att snabbt se om agenten visas.  

- Kör följande fråga för att bekräfta att agenten skickar ett pulsslag till arbetsytan som den är konfigurerad att rapportera till. Ersätt `<ComputerName>` med maskinens faktiska namn.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Om datorn har kommunicerat med tjänsten bör frågan returnera ett resultat. Om frågan inte returnerade något resultat kontrollerar du först att agenten är konfigurerad för att rapportera till rätt arbetsyta. Om den är korrekt konfigurerad fortsätter du till steg 3 och söker i Windows-händelseloggen för att identifiera om agenten loggar vilket problem som kan hindra den från att kommunicera med Azure Monitor.

- En annan metod för att identifiera ett anslutningsproblem är genom att köra **TestCloudConnectivity-verktyget.** Verktyget installeras som standard med agenten i mappen *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent*. Från en upphöjd kommandotolk navigerar du till mappen och kör verktyget. Verktyget returnerar resultaten och höjdpunkterna där testet misslyckades (till exempel om det var relaterat till en viss port/URL som blockerades). 

    ![TestCloudConnection-verktygskörningsresultat](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filtrera *Händelseloggen i Operations Manager* efter*hälsotjänstmoduler* **för** - hälsotjänst, *HealthService*och *Service Connector* och filtrera efter *varning* och *fel* på **händelsenivå** för att bekräfta om den har skrivit händelser från följande tabell. Om de är, granska de resolutionssteg som ingår för varje möjlig händelse.

    |Händelse-ID |Källa |Beskrivning |Lösning |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Hälsotjänst |Anslutningen till tjänsten från agenten misslyckades |Det här felet kan uppstå när agenten inte kan kommunicera direkt eller via en brandvägg/proxyserver till Azure Monitor-tjänsten. Verifiera proxyinställningarna för agent eller att nätverksbrandväggen/proxyn tillåter TCP-trafik från datorn till tjänsten.|
    |2138 |Moduler för hälso- och sjukvård |Proxy kräver autentisering |Konfigurera proxyinställningarna för agenten och ange vilket användarnamn/lösenord som krävs för att autentisera med proxyservern. |
    |2129 |Moduler för hälso- och sjukvård |Misslyckad anslutning/misslyckad TLS-förhandling |Kontrollera TCP/IP-inställningarna för nätverkskortet och proxyinställningarna för agenten.|
    |2127 |Moduler för hälso- och sjukvård |Det gick inte att skicka data mottagen felkod |Om det bara händer regelbundet under dagen, kan det bara vara en slumpmässig anomali som kan ignoreras. Övervaka för att förstå hur ofta det händer. Om det händer ofta under hela dagen kontrollerar du först nätverkskonfigurationen och proxyinställningarna. Om beskrivningen innehåller HTTP-felkod 404 och det är första gången som agenten försöker skicka data till tjänsten, kommer det att innehålla ett 500-fel med en inre 404-felkod. 404 betyder inte hittades, vilket tyder på att lagringsområdet för den nya arbetsytan fortfarande håller på att etableras. Vid nästa nytt försök skrivs data som förväntat till arbetsytan. Ett HTTP-fel 403 kan tyda på ett problem med behörighet eller autentiseringsuppgifter. Det finns mer information som ingår i 403-felet för att felsöka problemet.|
    |4000 |Serviceanslutning |DNS-namnmatchning misslyckades |Det gick inte att lösa den Internet-adress som användes när data skickades till tjänsten. Detta kan vara DNS-matchningsinställningar på datorn, felaktiga proxyinställningar eller kanske ett tillfälligt DNS-problem med din leverantör. Om det sker med jämna mellanrum kan det orsakas av ett tillfälligt nätverksrelaterat problem.|
    |4001 |Serviceanslutning |Anslutningen till tjänsten misslyckades. |Det här felet kan uppstå när agenten inte kan kommunicera direkt eller via en brandvägg/proxyserver till Azure Monitor-tjänsten. Verifiera proxyinställningarna för agent eller att nätverksbrandväggen/proxyn tillåter TCP-trafik från datorn till tjänsten.|
    |4002 |Serviceanslutning |Tjänsten returnerade HTTP-statuskod 403 som svar på en fråga. Kontakta tjänstadministratören för att få tjänstens hälsotillstånd. Frågan kommer att göras om senare. |Det här felet skrivs under agentens inledande registreringsfas och du ser en url som liknar följande: *https://\<workspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest*. En felkod 403 betyder förbjuden och kan orsakas av ett feltypat arbetsyte-ID eller nyckel, eller så är data och tid felaktig på datorn. Om tiden är +/- 15 minuter från aktuell tid misslyckas introduktionen. Du korrigerar detta genom att uppdatera datum och/eller tidszon för din Windows-dator.|

## <a name="data-collection-issues"></a>Problem med datainsamling

När agenten har installerats och rapporterar till dess konfigurerade arbetsyta eller arbetsytor kan den sluta ta emot konfiguration, samla in eller vidarebefordra prestanda, loggar eller andra data till tjänsten beroende på vad som är aktiverat och som är inriktat på datorn. Det är nödvändigt att avgöra om:

- Är det en viss datatyp eller alla data som inte är tillgängliga på arbetsytan?
- Anges datatypen av en lösning eller anges som en del av konfigurationen för insamling av arbetsytedata?
- Hur många datorer påverkas? Är det en eller flera datorer som rapporterar till arbetsytan?
- Fungerade det och slutade det vid en viss tid på dagen, eller har det aldrig samlats in? 
- Är loggsökningsfrågan som du använder syntaktiskt korrekt? 
- Har agenten någonsin fått sin konfiguration från Azure Monitor?

Det första steget i felsökningen är att avgöra om datorn skickar en pulsslagshändelse.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Om frågan returnerar resultat måste du avgöra om en viss datatyp inte samlas in och vidarebefordras till tjänsten. Detta kan orsakas av att agenten inte tar emot uppdaterad konfiguration från tjänsten, eller något annat symptom som hindrar agenten från att fungera normalt. Utför följande steg för att ytterligare felsöka.

1. Öppna en upphöjd kommandotolk på datorn och `net stop healthservice && net start healthservice`starta om agenttjänsten genom att skriva .
2. Öppna händelseloggen i *Operations Manager* och sök efter **händelse-ID:er** *7023, 7024, 7025, 7028* och *1210* från *Hälsotjänsten*för **händelsekällor** .  Dessa händelser indikerar att agenten har tagit emot konfiguration från Azure Monitor och de övervakar aktivt datorn. Händelsebeskrivningen för händelse-ID 1210 anger också på den sista raden alla lösningar och insikter som ingår i omfattningen av övervakningen på agenten.  

    ![Beskrivning av händelse-ID 1210](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Om du efter flera minuter inte ser förväntade data i frågeresultaten eller visualiseringen, beroende på om du visar data från en lösning eller Insikt, från Händelseloggen i *Operations Manager,* söker du efter *hälsotjänstmoduler* för *hälsotjänst* och hälsotjänst för **händelsekällor** och filtrerar efter *varning* och *fel* **på händelsenivå** för att bekräfta om det har skrivit händelser från följande tabell.

    |Händelse-ID |Källa |Beskrivning |Lösning |
    |---------|-------|------------|
    |8000 |Hälsoservice |Den här händelsen anger om ett arbetsflöde som är relaterat till prestanda, händelse eller annan datatyp som samlas in inte kan vidarebefordras till tjänsten för inmatning till arbetsytan. | Händelse-ID 2136 från source HealthService är skriven tillsammans med den här händelsen och kan indikera att agenten inte kan kommunicera med tjänsten, eventuellt på grund av felkonfiguration av proxy- och autentiseringsinställningarna, nätverksavbrott eller nätverksbrandväggen/proxyn tillåter inte TCP-trafik från datorn till tjänsten.| 
    |10102 och 10103 |Moduler för hälso- och sjukvård |Arbetsflödet kunde inte lösa datakällan. |Detta kan inträffa om den angivna prestandaräknaren eller instansen inte finns på datorn eller är felaktigt definierad i arbetsytedatainställningarna. Om detta är en användarspecificerad [prestandaräknare](data-sources-performance-counters.md#configuring-performance-counters)kontrollerar du att den angivna informationen följer rätt format och finns på måldatorerna. |
    |26002 |Moduler för hälso- och sjukvård |Arbetsflödet kunde inte lösa datakällan. |Detta kan inträffa om den angivna Windows-händelseloggen inte finns på datorn. Det här felet kan ignoreras på ett säkert sätt om datorn inte förväntas ha den här händelseloggen registrerad, annars om det är en användarspecificerad [händelselogg,](data-sources-windows-events.md#configuring-windows-event-logs)kontrollera att den angivna informationen är korrekt. |

