---
title: Logg analys för Windows Virtual Desktop-diagnostik – Azure
description: Så här använder du Log Analytics med funktionen Windows-diagnostik för virtuella skriv bord.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 76a5e12eee7a325a73b3c17dba6c775b6984b89a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195912"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Använd Log Analytics för funktionen diagnostik

>[!IMPORTANT]
>Det här innehållet gäller för våren 2020-uppdateringen med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder den virtuella Windows-datorn med version 2019 utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md).
>
> Den virtuella Windows-skrivbordets våren 2020-uppdateringen är för närvarande en offentlig för hands version. Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar inte att du använder den för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. 
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Windows Virtual Desktop använder [Azure Monitor](../azure-monitor/overview.md) för övervakning och aviseringar som många andra Azure-tjänster. Detta gör att administratörer kan identifiera problem via ett enda gränssnitt. Tjänsten skapar aktivitets loggar för både användar-och administrations åtgärder. Varje aktivitets logg faller under följande kategorier:  

- Hanterings aktiviteter:
    - Spåra om försök att ändra virtuella Windows-skrivbordet med API: er eller PowerShell lyckas. Kan någon till exempel skapa en adresspool med PowerShell?
- Tillförsel 
    - Kan användare prenumerera på arbets ytor? 
    - Ser användarna alla resurser som publicerats i fjärr skrivbords klienten?
- Anslutningar: 
    - När användarna initierar och slutför anslutningar till tjänsten. 
- Värd registrering: 
    - Har sessions värden registrerats med tjänsten vid anslutning?
- Kompileringsfel 
    - Påträffar användarna eventuella problem med specifika aktiviteter? Den här funktionen kan generera en tabell som spårar aktivitets data så länge informationen är kopplad till aktiviteterna.
- Kontroll punkter  
    - Vissa steg i livs längden för en aktivitet som har nåtts. Under en-session har en användare exempelvis belastnings utjämning till en viss värd, sedan var användaren inloggad vid en anslutning och så vidare.

Anslutningar som inte når Windows Virtual Desktop visas inte i diagnostiska resultat, eftersom själva roll tjänsten för diagnostik är en del av det virtuella Windows-skrivbordet. Problem med Windows Virtual Desktop-anslutning kan inträffa när användaren har problem med nätverks anslutningen.

Med Azure Monitor kan du analysera Windows-data för virtuella skriv bord och granska prestanda räknare för virtuella datorer (VM), allt inom samma verktyg. I den här artikeln får du lära dig mer om hur du aktiverar diagnostik för din Windows Virtual Desktop-miljö. 

>[!NOTE] 
>Information om hur du övervakar dina virtuella datorer i Azure finns i [övervaka virtuella Azure-datorer med Azure Monitor](../azure-monitor/insights/monitor-vm-azure.md). Se även till att [Granska tröskelvärdena för prestanda räknaren](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds) för att få en bättre förståelse för användar upplevelsen på sessionen.

## <a name="before-you-get-started"></a>Innan du börjar

Innan du kan använda Log Analytics måste du skapa en arbets yta. Det gör du genom att följa anvisningarna i någon av följande två artiklar:

- Om du föredrar att använda Azure Portal, se [skapa en Log Analytics arbets yta i Azure Portal](../azure-monitor/learn/quick-create-workspace.md).
- Om du föredrar PowerShell, se [skapa en Log Analytics arbets yta med PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

När du har skapat arbets ytan följer du anvisningarna i [Anslut Windows-datorer till Azure Monitor](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) för att få följande information:

- Arbetsyte-ID
- Den primära nyckeln för din arbets yta

Du behöver den här informationen senare i installations processen.

Se till att granska behörighets hantering för Azure Monitor för att aktivera data åtkomst för de som övervakar och underhåller din Windows-miljö för virtuella skriv bord. Mer information finns i [Kom igång med roller, behörigheter och säkerhet med Azure Monitor](../azure-monitor/platform/roles-permissions-security.md). 

## <a name="push-diagnostics-data-to-your-workspace"></a>Skicka diagnostikdata till din arbets yta

Du kan skicka diagnostikdata från dina Windows virtuella Skriv bords objekt till Log Analytics för din arbets yta. Du kan ställa in den här funktionen direkt när du först skapar dina objekt.

Så här konfigurerar du Log Analytics för ett nytt objekt:

1. Logga in på Azure Portal och gå till **det virtuella Windows-skrivbordet**. 

2. Navigera till objektet (till exempel en värdstat, en app-grupp eller en arbets yta) som du vill avbilda loggar och händelser för. 

3. Välj **diagnostikinställningar** i menyn på vänster sida av skärmen. 

4. Välj **Lägg till diagnostisk inställning** på menyn som visas på höger sida av skärmen. 
   
    Vilka alternativ som visas på sidan diagnostikinställningar varierar beroende på vilken typ av objekt du redigerar.

    När du till exempel aktiverar diagnostik för en app-grupp visas alternativ för att konfigurera kontroll punkter, fel och hantering. För arbets ytor konfigurerar de här kategorierna en feed som spårar när användarna prenumererar på listan över appar. Mer information om diagnostikinställningar finns i [skapa diagnostisk inställning för att samla in resurs loggar och mått i Azure](../azure-monitor/platform/diagnostic-settings.md). 

     >[!IMPORTANT] 
     >Kom ihåg att aktivera diagnostik för varje Azure Resource Manager objekt som du vill övervaka. Data blir tillgängliga för aktiviteter efter att diagnostik har Aktiver ATS. Det kan ta några timmar efter första insamlingen.  

5. Ange ett namn för konfiguration av inställningar och välj sedan **Skicka till Log Analytics**. Namnet du använder får inte innehålla blank steg och bör följa [namngivnings konventionerna för Azure](../azure-resource-manager/management/resource-name-rules.md). Som en del av loggarna kan du välja alla alternativ som du vill lägga till i Log Analytics, t. ex. kontroll punkt, fel, hantering och så vidare.

6. Välj **Spara**.

>[!NOTE]
>Log Analytics ger dig möjlighet att strömma data till [Event Hubs](../event-hubs/event-hubs-about.md) eller arkivera dem i ett lagrings konto. Mer information om den här funktionen finns i [Stream Azure Monitoring data to a Event Hub](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) och [arkivera Azure-resurs loggar till lagrings kontot](../azure-monitor/platform/resource-logs-collect-storage.md). 

## <a name="how-to-access-log-analytics"></a>Så här kommer du åt Log Analytics

Du kan komma åt Log Analytics arbets ytor på Azure Portal eller Azure Monitor.

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Åtkomst Log Analytics på en Log Analytics arbets yta

1. Logga in på Azure Portal.

2. Sök efter **Log Analytics-arbetsyta**. 

3. Under tjänster väljer du **Log Analytics arbets ytor**. 
   
4. I listan väljer du den arbets yta som du konfigurerade för ditt Windows Virtual Desktop-objekt.

5. På arbets ytan väljer du **loggar**. Du kan filtrera ut din meny lista med **Sök** funktionen. 

### <a name="access-log-analytics-on-azure-monitor"></a>Åtkomst Log Analytics på Azure Monitor

1. Logga in på Azure-portalen

2. Sök efter och välj **övervakare**. 

3. Välj **loggar**.

4. Följ instruktionerna på sidan loggning för att ange omfånget för frågan.  

5. Du är redo att fråga diagnostik. Alla diagnostiska tabeller har prefixet "WVD".

## <a name="cadence-for-sending-diagnostic-events"></a>Takt för att skicka diagnostiska händelser

Diagnostiska händelser skickas till Log Analytics när de är klara.

Log Analytics endast rapporter i dessa mellanliggande tillstånd för anslutnings aktiviteter:

- Startade: när en användare väljer och ansluter till en app eller ett skriv bord i fjärr skrivbords klienten.
- Ansluten: när användaren har anslutit till den virtuella datorn där appen eller Skriv bordet finns.
- Slutfört: när användaren eller servern kopplar från sessionen som aktiviteten ägde rum i.

## <a name="example-queries"></a>Exempelfrågor

Följande exempel frågor visar hur Diagnostics-funktionen genererar en rapport för de mest frekventa aktiviteterna i systemet.

Kör den här cmdleten om du vill hämta en lista över anslutningar som skapats av användarna:

```kusto
WVDConnections 
| project-away TenantId,SourceSystem 
| summarize arg_max(TimeGenerated, *), StartTime =  min(iff(State== 'Started', TimeGenerated , datetime(null) )), ConnectTime = min(iff(State== 'Connected', TimeGenerated , datetime(null) ))   by CorrelationId 
| join kind=leftouter ( 
    WVDErrors 
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId 
    ) on CorrelationId     
| join kind=leftouter ( 
   WVDCheckpoints 
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId 
   | mv-apply Checkpoints on 
    ( 
        order by todatetime(Checkpoints['Time']) asc 
        | summarize Checkpoints=makelist(Checkpoints) 
    ) 
   ) on CorrelationId 
| project-away CorrelationId1, CorrelationId2 
| order by  TimeGenerated desc 
```

Visa flödes aktivitet för dina användare:

```kusto
WVDFeeds  
| project-away TenantId,SourceSystem  
| join kind=leftouter (  
    WVDErrors  
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId  
    ) on CorrelationId      
| join kind=leftouter (  
   WVDCheckpoints  
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId  
   | mv-apply Checkpoints on  
    (  
        order by todatetime(Checkpoints['Time']) asc  
        | summarize Checkpoints=makelist(Checkpoints)  
    )  
   ) on CorrelationId  
| project-away CorrelationId1, CorrelationId2  
| order by  TimeGenerated desc 
```

Så här söker du efter alla anslutningar för en enskild användare: 

```kusto
|where UserName == "userupn" 
|take 100 
|sort by TimeGenerated asc, CorrelationId 
```
 

Så här tar du reda på hur många gånger en användare ansluter per dag:

```kusto
WVDConnections 
|where UserName == "userupn" 
|take 100 
|sort by TimeGenerated asc, CorrelationId 
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d) 
```
 

Så här hittar du varaktigheten för sessionen per användare:

```kusto
let Events = WVDConnections | where UserName == "userupn" ; 
Events 
| where State == "Connected" 
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated 
| join (Events 
| where State == "Completed" 
| project EndTime=TimeGenerated, CorrelationId) 
on CorrelationId 
| project Duration = EndTime - StartTime, ResourceAlias 
| sort by Duration asc 
```

Så här hittar du fel för en speciell användare:

```kusto
WVDErrors
| where UserName == "userupn" 
|take 100
```

Ta reda på om ett angivet fel har inträffat:

```kusto
WVDErrors 
| where CodeSymbolic =="ErrorSymbolicCode" 
| summarize count(UserName) by CodeSymbolic 
```

Så här hittar du förekomst av ett fel för alla användare:

```kusto
WVDErrors 
| where ServiceError =="false" 
| summarize usercount = count(UserName) by CodeSymbolic 
| sort by usercount desc
| render barchart 
```

>[!NOTE]
>Den viktigaste tabellen för fel sökning är WVDErrors. Använd den här frågan för att förstå vilka problem som inträffar för användar aktiviteter som anslutningar eller flöden när en användare prenumererar på listan över appar eller skriv bord. I tabellen visas hanterings fel och problem med värd registrering.
>
>Om du behöver hjälp med att lösa ett problem under en offentlig för hands version ser du till att du ger det fel meddelandet i din supportbegäran. Se också till att tjänstens felvärde alltid står ServiceError = "false". Ett "falskt"-värde innebär att problemet kan lösas av en administratörs uppgift i slutet. Om ServiceError = "true" måste du eskalera problemet till Microsoft.

## <a name="next-steps"></a>Nästa steg 

Se [identifiera och diagnostisera problem](diagnostics-role-service.md#common-error-scenarios)för att granska vanliga fel scenarier som kan identifieras av diagnostikprogrammet.
