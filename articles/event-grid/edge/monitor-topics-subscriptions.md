---
title: Övervaka ämnen och händelseprenumerationer – Azure Event Grid IoT Edge | Microsoft-dokument
description: Övervaka ämnen och händelseprenumerationer
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ce7c92f121fb458d528d63d0af0aad025b377386
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086671"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Övervaka ämnen och händelseprenumerationer

Event Grid on Edge visar ett antal mått för ämnen och händelseprenumerationer i [Prometheus-utläggningsformatet](https://prometheus.io/docs/instrumenting/exposition_formats/). I den här artikeln beskrivs tillgängliga mått och hur du aktiverar dem.

## <a name="enable-metrics"></a>Aktivera mått

Konfigurera modulen så att måtten `metrics__reporterType` ska `prometheus` avges genom att ange miljövariabeln till i behållaren skapa alternativ:

 ```json
        {
          "Env": [
            "metrics__reporterType=prometheus"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
 ```    

Mått kommer att `5888/metrics` finnas tillgängliga på `4438/metrics` modulen för http och för https. Till exempel `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` för http. Vid denna punkt kan en måttmodul avsöka slutpunkten för att samla in mått som i den här [exempelarkitekturen](https://github.com/veyalla/ehm).

## <a name="available-metrics"></a>Tillgängliga mått

Både ämnen och händelseprenumerationer släpper ut mått för att ge dig insikter om händelseleverans och modulprestanda.

### <a name="topic-metrics"></a>Ämnesmått

| Mått | Beskrivning |
| ------ | ----------- |
| HändelserKom med | Antal händelser som publicerats i avsnittet
| OmatchadeHändelser | Antal händelser som publicerats i ämnet som inte matchar en händelseprenumeration och som tas bort
| FramgångRequests | Antal inkommande publiceringsbegäranden som tagits emot av ämnet
| SystemErrorRequests | Antalet inkommande publiceringsbegäranden misslyckades på grund av ett internt systemfel
| UserErrorRequests | Antalet på inkommande publiceringsbegäranden misslyckades på grund av användarfel som felaktigt formade JSON
| LyckaRequestLatencyMs | Publicera svarssvarsfördröjning i millisekunder


### <a name="event-subscription-metrics"></a>Mått för händelseprenumeration

| Mått | Beskrivning |
| ------ | ----------- |
| LeveransSuccessCounts | Antal händelser som har levererats till den konfigurerade slutpunkten
| DeliveryFailureCounts | Antal händelser som inte kunde levereras till den konfigurerade slutpunkten
| LeveransSugsLatencyMs | Svarstiden för händelser som har levererats i millisekunder
| DeliveryFailureLatencyMs | Svarstid för händelseleveransfel i millisekunder
| SystemDelayForFirstAttemptMs | Systemfördröjning av händelser före första leveransförsöket i millisekunder
| LeveransAttemptsCount | Antal händelseleveransförsök - lyckad och misslyckad
| Utgångna antal | Antal händelser som har upphört att gälla och som inte levererades till den konfigurerade slutpunkten