---
title: Windows Virtual Desktop diagnostik logganalys - Azure
description: Så här använder du logganalys med diagnostikfunktionen för Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 355acb081afef8c78cdf971c7a82acdb91ab5593
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127964"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Använda Log Analytics för diagnostikfunktionen

Windows Virtual Desktop erbjuder en diagnostikfunktion som gör det möjligt för administratören att identifiera problem via ett enda gränssnitt. Den här funktionen loggar diagnostikinformation när någon tilldelad Windows Virtual Desktop-roll använder tjänsten. Varje logg innehåller information om vilken Windows Virtual Desktop-roll som var inblandad i aktiviteten, eventuella felmeddelanden som visas under sessionen, klientinformation och användarinformation. Diagnostikfunktionen skapar aktivitetsloggar för både användar- och administrativa åtgärder. Varje aktivitetslogg faller under tre huvudkategorier: 

- Feed-prenumerationsaktiviteter: när en användare försöker ansluta till sin feed via Microsoft Remote Desktop-program.
- Anslutningsaktiviteter: när en användare försöker ansluta till ett skrivbord eller RemoteApp via Microsoft Remote Desktop-program.
- Hanteringsaktiviteter: när en administratör utför hanteringsåtgärder på systemet, till exempel att skapa värdpooler, tilldela användare till appgrupper och skapa rolltilldelningar.

Anslutningar som inte når Windows Virtual Desktop visas inte i diagnostikresultat eftersom själva diagnostikrolltjänsten är en del av Windows Virtual Desktop. Anslutningsproblem för Virtuella datorer i Windows kan inträffa när användaren har problem med nätverksanslutningen.

## <a name="why-you-should-use-log-analytics"></a>Varför du bör använda Log Analytics

Vi rekommenderar att du använder Log Analytics för att analysera diagnostikdata i Azure-klienten som går utöver felsökning för en användare. När du kan hämta prestandaräknare för virtuella datorer i Logganalys har du ett verktyg för att samla in information för distributionen.

## <a name="before-you-get-started"></a>Innan du börjar

Innan du kan använda Log Analytics med diagnostikfunktionen måste du [skapa en arbetsyta](../azure-monitor/learn/quick-collect-windows-computer.md#create-a-workspace).

När du har skapat arbetsytan följer du instruktionerna i [Anslut Windows-datorer till Azure Monitor](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) för att få följande information: 

- Arbetsyte-ID:et
- Den primära nyckeln på din arbetsyta

Du behöver den här informationen senare i installationsprocessen.

## <a name="push-diagnostics-data-to-your-workspace"></a>Skicka diagnostikdata till arbetsytan 

Du kan skicka diagnostikdata från din Windows Virtual Desktop-klient till Logganalysen för din arbetsyta. Du kan ställa in den här funktionen direkt när du först skapar din klient genom att länka arbetsytan till din klientorganisation, eller så kan du ställa in den senare med en befintlig klientorganisation.

Om du vill länka din klient till logganalysarbetsytan medan du konfigurerar din nya klientorganisation kör du följande cmdlet för att logga in på Windows Virtual Desktop med ditt TenantCreator-användarkonto: 

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com 
```

Om du ska länka en befintlig klient i stället för en ny klient kör du den här cmdleten i stället: 

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Du måste köra dessa cmdlets för varje klient som du vill länka till Log Analytics. 

>[!NOTE]
>Om du inte vill länka log analytics-arbetsytan när du `New-RdsTenant` skapar en klient kör du cmdleten i stället. 

## <a name="cadence-for-sending-diagnostic-events"></a>Kadens för att skicka diagnostiska händelser

Diagnostiska händelser skickas till Log Analytics när de är klara.  

## <a name="example-queries"></a>Exempelfrågor

Följande exempelfrågor visar hur diagnostikfunktionen genererar en rapport för de vanligaste aktiviteterna i systemet:

I det här första exemplet visas anslutningsaktiviteter som initierats av användare med fjärrskrivbordsklienter som stöds:

```powershell
WVDActivityV1_CL 

| where Type_s == "Connection" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```

I nästa exempelfråga visas hanteringsaktiviteter för administratörer på klienter:

```powershell
WVDActivityV1_CL 

| where Type_s == "Management" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```
 
## <a name="stop-sending-data-to-log-analytics"></a>Sluta skicka data till Log Analytics 

Om du vill sluta skicka data från en befintlig klient till Log Analytics kör du följande cmdlet och anger tomma strängar:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Du måste köra den här cmdleten för varje klient som du vill sluta skicka data från. 

## <a name="next-steps"></a>Nästa steg 

Information om hur du granskar vanliga felscenarier som diagnostikfunktionen kan identifiera åt dig finns [i Identifiera och diagnostisera problem](diagnostics-role-service.md#common-error-scenarios).
