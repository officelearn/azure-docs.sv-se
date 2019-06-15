---
title: Så här felsöker du problem med Log Analytics-agenten för Windows | Microsoft Docs
description: Beskriv problem, orsaker och upplösning för de vanligaste problemen med Log Analytics-agenten för Windows i Azure Monitor.
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
ms.openlocfilehash: afa4483677336e9a887908a8cccf9590eed27af3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120115"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Så här felsöker du problem med Log Analytics-agenten för Windows 

Du får hjälp med att felsöka fel du kan uppleva med Log Analytics-agenten för Windows i Azure Monitor och föreslår lösningar för att lösa dem.

Om ingen av de här stegen fungerar för dig finns också stöd för följande kanaler:

* Kunder med Premier support-förmåner kan öppna en supportbegäran med [Premier](https://premier.microsoft.com/).
* Kunder med supportavtal för Azure kan öppna en supportbegäran [i Azure-portalen](https://manage.windowsazure.com/?getsupport=true).
* Gå till sidan för Log Analytics Feedback om du vill granska skickade idéer och buggar [ https://aka.ms/opinsightsfeedback ](https://aka.ms/opinsightsfeedback) eller skicka en ny. 

## <a name="important-troubleshooting-sources"></a>Viktiga felsökning källor

 För att hjälpa till med felsökning av problem som rör Log Analytics-agenten för Windows, agenten loggar händelser till Windows händelselogg, särskilt under *program- och Services\Operations Manager*.  

## <a name="connectivity-issues"></a>Anslutningsproblem

Om agenten kommunicerar via en proxyserver eller brandvägg, kan det finnas begränsningar på plats som förhindrar kommunikation från käll- och Azure Monitor-tjänsten. Om kommunikation blockeras felkonfiguration, registrering med en arbetsyta misslyckas medan försöker att installera agenten, konfigurera agenten efter installationen att rapportera till en ytterligare arbetsyta, eller agentkommunikationen misslyckas efter en lyckad registrering. Det här avsnittet beskrivs metoderna för att felsöka den här typen av problem med Windows-agenten. 

Kontrollera att brandväggen eller proxyservern har konfigurerats för att tillåta följande portar och URL: er som beskrivs i följande tabell. Bekräfta också HTTP-kontroll inte är aktiverad för Internet-trafik, eftersom det förhindrar att en säker TLS-kanal mellan agenten och Azure Monitor.  

|Agentresurs|Portar |Riktning |Kringgå HTTPS-kontroll|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Utgående|Ja |  
|*.oms.opinsights.azure.com |Port 443 |Utgående|Ja |  
|*.blob.core.windows.net |Port 443 |Utgående|Ja |  
|*.azure-automation.net |Port 443 |Utgående|Ja |  

Brandväggen information krävs för Azure Government finns i [hantering av Azure Government](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Det finns flera sätt du kan kontrollera om agenten kan kommunicera med Azure Monitor.

- Aktivera den [Agenthälsa för Azure Log Analytics-bedömning](../insights/solution-agenthealth.md) på arbetsytan. Från instrumentpanelen för Agenthälsa visa den **räkning av agenter som inte svarar** kolumnen för att snabbt se om agenten finns.  

- Kör följande fråga för att bekräfta att agenten skickar ett pulsslag till den arbetsyta som den är konfigurerad för att rapportera till. Ersätt <ComputerName> med namnet på datorn.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Om datorn kan kommunicera med tjänsten, ska frågan returnera ett resultat. Om frågan inte returnerade ett resultat, först kontrollera att agenten konfigureras att rapportera till rätt arbetsyta. Om den är korrekt konfigurerad, gå vidare till steg 3 och Sök i Windows-händelseloggen för att identifiera om agenten loggar in vilket problem hindrar den från att kommunicera med Azure Monitor.

- En annan metod för att identifiera ett anslutningsproblem är genom att köra den **TestCloudConnectivity** verktyget. Verktyget är installerat som standard med agenten i mappen *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent*. Navigera till mappen från en upphöjd kommandotolk och kör verktyget. Verktyget returnerar resultat och höjdpunkter där testet misslyckades (till exempel om den var kopplad till en viss port/URL som har blockerats). 

    ![TestCloudConnection verktyget Körningsresultat](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filter i *Operations Manager* händelseloggen genom **händelsekällor** - *Health Service Modules*, *HealthService*, och *Tjänstanslutning* och filtrera efter **Händelsenivå** *varning* och *fel* att bekräfta om de skrivs händelser från den följande tabell. Om så är fallet kan du granska Lösningssteg ingår för varje händelse som möjligt.

    |Händelse-ID |source |Beskrivning |Lösning |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Tjänsten för hälsotillstånd |Anslutningen till tjänsten från agenten misslyckades |Det här felet kan inträffa när agenten inte kan kommunicera direkt eller via en brandvägg/proxyserver till Azure Monitor-tjänsten. Kontrollera proxy-inställningar för agenten eller att brandvägg/nätverksproxy tillåter TCP-trafik från datorn till tjänsten.|
    |2138 |Health Service Modules |Proxyservern kräver autentisering |Konfigurera proxyinställningar för agenten och ange det användarnamn/lösenord som krävs för att autentisera med proxyservern. |
    |2129 |Health Service Modules |Misslyckad anslutning/misslyckades SSL-förhandling |Kontrollera TCP/IP-inställningar för nätverkskort och proxyinställningar för agenten.|
    |2127 |Health Service Modules |Felkod för fel som skickar data |Om det bara händer med jämna mellanrum under dagen kan det bara en slumpmässig avvikelser som kan ignoreras. Övervaka för att förstå hur ofta det händer. Om det händer ofta under dagen, kontrollera först nätverkskonfiguration och proxyinställningar. Om beskrivningen innehåller HTTP-felkoden 404 och det är första gången som agenten försöker skicka data till tjänsten, innehåller den ett 500-fel med en inre 404 felkod. 404 innebär inte att hitta, vilket betyder att etableras fortfarande lagringsplats för den nya arbetsytan. Vid nästa återförsök skriver data har till arbetsytan som förväntat. Ett HTTP-fel 403 kan indikera en behörighet eller autentiseringsuppgifter för problemet. Det finns mer information som ingår i 403-fel för att felsöka problemet.|
    |4000 |Tjänst-anslutningen |DNS-namnmatchningen misslyckades |Datorn kunde inte matcha den Internet-adress som används när data skickas till tjänsten. Det kan vara DNS-matchare inställningar på din dator, felaktig proxyinställningar eller kan vara ett tillfälligt DNS-problem med din provider. Om det händer med jämna mellanrum, kan det bero på av ett tillfälligt nätverksrelaterade problem.|
    |4001 |Tjänst-anslutningen |Det gick inte att ansluta till tjänsten. |Det här felet kan inträffa när agenten inte kan kommunicera direkt eller via en brandvägg/proxyserver till Azure Monitor-tjänsten. Kontrollera proxy-inställningar för agenten eller att brandvägg/nätverksproxy tillåter TCP-trafik från datorn till tjänsten.|
    |4002 |Tjänst-anslutningen |Tjänsten returnerade HTTP-statuskod 403 som svar på en fråga. Kontakta tjänstadministratören för hälsotillståndet för tjänsten. Frågan skickas igen senare. |Det här felet skrivs under fasen för agentens första registreringen och du ser en URL som liknar följande: *https://<workspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest*. Ett fel code 403 innebär tillåts inte och kan orsakas av ett felstavat arbetsyte-ID eller nyckel, eller datum och tid stämmer på datorn. Om tiden är +/-15 minuter efter den aktuella tiden, misslyckas onboarding. Åtgärda detta genom att uppdatera datum och/eller tidszonen för din Windows-dator.|

## <a name="data-collection-issues"></a>Problem med datasamling

När agenten är installerad och rapporter i dess konfigurerade arbetsytan eller arbetsytor, det sluta ta emot konfiguration, samla in eller vidarebefordran av prestanda, loggar eller andra data till tjänsten beroende på vad är aktiverat och riktar in sig på datorn. Det är nödvändigt att ta reda på om:

- Är det en viss typ eller alla data som inte är tillgänglig i arbetsytan?
- Är den datatyp som anges av en lösning eller anges som en del av konfigurationen av insamling av arbetsytan?
- Hur många datorer som påverkas? Är det en eller flera datorer som rapporterar till arbetsytan?
- Fungerade det slutar den vid en viss tid på dagen och har det aldrig varit insamlade? 
- Är logga sökfråga som du använder syntaktiskt korrekt? 
- Har agenten någonsin fått konfigurationen från Azure Monitor?

Det första steget vid felsökning är att avgöra om datorn skickar en händelse för pulsslag.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Om frågan returnerar resultat, måste du avgöra om en viss typ inte samlas in och vidarebefordras till tjänsten. Detta kan bero på att agenten inte ta emot uppdaterade konfigurationen från tjänsten eller några andra problem som förhindrar att agenten fungerar normalt. Utför följande steg för att felsöka detta ytterligare.

1. Öppna en upphöjd kommandotolk på datorn och starta om agenttjänsten genom att skriva `net stop healthservice && net start healthservice`.
2. Öppna den *Operations Manager* händelseloggen och Sök efter **händelse-ID** *7023, 7024, 7025, 7028* och *1210* från **händelse källan** *HealthService*.  Dessa händelser anger att agenten har tar emot konfigurationen från Azure Monitor och de aktivt övervakar datorn. Händelsebeskrivning för händelse-ID 1210 anger också på sist raden alla lösningar och insikter som ingår i omfånget för övervakning på agenten.  

    ![Händelse-ID 1210 beskrivning](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Om efter ett par minuter inte visas det förväntade data i frågeresultaten eller visualisering, beroende på om du visar data från en lösning och insikter från den *Operations Manager* händelseloggen och Sök efter **händelse källor** *HealthService* och *Health Service Modules* och filtrera efter **Händelsenivå** *varning* och *Fel* att bekräfta om de skrivs händelser i följande tabell.

    |Händelse-ID |source |Beskrivning |Lösning |
    |---------|-------|------------|
    |8000 |HealthService |Den här händelsen anger om ett arbetsflöde som rör prestanda, händelse, eller någon annan data som samlas in kan inte vidarebefordra till tjänsten för inmatning till arbetsytan. | Händelse-ID 2136 från källan HealthService skrivs tillsammans med den här händelsen och kan tyda på agenten kan inte kommunicera med tjänsten, möjligen på grund av felaktig konfiguration av inställningar för proxy och autentisering, nätverksavbrott eller nätverksbrandväggen / proxyservern tillåter inte att TCP-trafik från datorn till tjänsten.| 
    |10102 och 10103 |Health Service Modules |Arbetsflödet kunde inte matcha datakällan. |Detta kan inträffa om den angivna prestandaräknaren eller instansen finns inte på datorn eller felaktigt har definierats i arbetsyteinställningarna för data. Om det här är ett användardefinierat [prestandaräknaren](data-sources-performance-counters.md#configuring-performance-counters)kontrollerar du uppgifterna följer rätt format och finns på måldatorerna. |
    |26002 |Health Service Modules |Arbetsflödet kunde inte matcha datakällan. |Detta kan inträffa om den angivna Windows-händelseloggen inte finns på datorn. Det här felet kan ignoreras om datorn inte är förväntas ha den här händelseloggen registrerat, annars om det här är ett användardefinierat [händelseloggen](data-sources-windows-events.md#configuring-windows-event-logs), kontrollera uppgifterna är korrekt. |

