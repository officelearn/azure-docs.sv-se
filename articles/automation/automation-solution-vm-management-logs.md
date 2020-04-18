---
title: Så här frågar du efter loggar från start-/stopp-virtuella datorer
description: I den här artikeln beskrivs hur du frågar loggdata som genereras av start/stop-virtuella datorer-lösningen från Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 472f3762ca18f71ba95053576daf025d8477fee9
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604729"
---
# <a name="how-to-query-logs-from-startstop-vms-solution"></a>Så här frågar du efter loggar från start-/stopp-virtuella datorer

Azure Automation vidarebefordrar två typer av poster till den länkade Log Analytics-arbetsytan: jobbloggar och jobbströmmar. Dessa data är tillgängliga för [frågor](../azure-monitor/log-query/log-query-overview.md) i Azure Monitor.

## <a name="job-logs"></a>Jobbloggar

|Egenskap | Beskrivning|
|----------|----------|
|Anropare |  Den som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb.|
|Kategori | Klassificering av typ av data. För Automation är värdet JobLogs.|
|CorrelationId | GUID som är korrelations-ID för runbook-jobbet.|
|JobId | GUID som är ID för runbook-jobbet.|
|operationName | Anger åtgärdstypen i Azure. För Automation är värdet Jobb.|
|resourceId | Anger resurstypen i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
|ResourceGroup | Anger resursgruppens namn på runbook-jobbet.|
|ResourceProvider | Anger den Azure-tjänst som tillhandahåller de resurser som du kan distribuera och hantera. För Automation är värdet Azure Automation.|
|ResourceType | Anger resurstypen i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
|resultType | Status för runbookjobbet. Möjliga värden:<br>- Startad<br>- Stoppad<br>-Pausad<br>- Misslyckades<br>- Slutförd|
|resultDescription | Beskriver jobbstatusen för runbook. Möjliga värden:<br>-Jobbet har startats<br>-Jobbet misslyckades<br>-Jobbet slutfördes|
|RunbookName | Anger namnet på runbooken.|
|SourceSystem | Anger källsystemet för data som skickats. För Automation är värdet OpsManager|
|StreamType | Anger händelsetypen. Möjliga värden:<br>- Verbose<br>- Utdata<br>- Fel<br>- Varning|
|SubscriptionId | Anger prenumerations-ID för jobbet.
|Tid | Datum och tid då runbook-jobbet körs.|

## <a name="job-streams"></a>Arbetsflöden

|Egenskap | Beskrivning|
|----------|----------|
|Anropare |  Den som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb.|
|Kategori | Klassificering av typ av data. För Automation är värdet JobStreams.|
|JobId | GUID som är ID för runbook-jobbet.|
|operationName | Anger åtgärdstypen i Azure. För Automation är värdet Jobb.|
|ResourceGroup | Anger resursgruppens namn på runbook-jobbet.|
|resourceId | Anger resurs-ID i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
|ResourceProvider | Anger den Azure-tjänst som tillhandahåller de resurser som du kan distribuera och hantera. För Automation är värdet Azure Automation.|
|ResourceType | Anger resurstypen i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
|resultType | Resultatet av runbook-jobbet då händelsen skapades. Ett möjligt värde är:<br>- Ärendeposter|
|resultDescription | Innehåller utdataströmmen från runbook.|
|RunbookName | Anger namnet på runbooken.|
|SourceSystem | Anger källsystemet för data som skickats. För Automation är värdet OpsManager.|
|StreamType | Typ av jobbström. Möjliga värden:<br>- Framsteg<br>- Utdata<br>- Varning<br>- Fel<br>- Felsökning<br>- Verbose|
|Tid | Datum och tid då runbook-jobbet körs.|

När du utför en loggsökning som returnerar kategoriposter för **JobLogs** eller **JobStreams**kan du välja vyn **JobLogs** eller **JobStreams,** som visar en uppsättning paneler som sammanfattar uppdateringarna som returneras av sökningen.

## <a name="sample-log-searches"></a>Exempel på loggsökningar

Följande tabell innehåller exempel på sökningar i loggen för jobbposter som har samlats in av den här lösningen.

|Söka i data | Beskrivning|
|----------|----------|
|Hitta jobb för runbook ScheduledStartStop_Parent som har slutförts | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Hitta jobb för runbook ScheduledStartStop_Parent som inte har slutförts | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Hitta jobb för runbook SequencedStartStop_Parent som har slutförts | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Hitta jobb för runbook SequencedStartStop_Parent som inte har slutförts | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>Nästa steg

**Start/Stop virtuella datorer under off-hours-lösningen** innehåller inte en fördefinierad uppsättning aviseringar. Granska [Create-loggaviseringarna](../azure-monitor/platform/alerts-log.md) med Azure Monitor för att lära dig hur du skapar misslyckade aviseringar för jobb för att stödja dina DevOps eller operativa processer och procedurer.