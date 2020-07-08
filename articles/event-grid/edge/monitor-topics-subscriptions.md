---
title: Övervaka ämnen och händelse prenumerationer – Azure Event Grid IoT Edge | Microsoft Docs
description: Övervaka ämnen och händelse prenumerationer
author: femila
ms.author: femila
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d4fbc5232722bfb08bde9be51d44e8e8d7514570
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84554360"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Övervaka ämnen och händelse prenumerationer

Event Grid på Edge exponerar ett antal mått för ämnen och händelse prenumerationer i formatet för [Prometheus-arbetsbok](https://prometheus.io/docs/instrumenting/exposition_formats/). I den här artikeln beskrivs tillgängliga mått och hur du aktiverar dem.

## <a name="enable-metrics"></a>Aktivera mått

Konfigurera modulen för att generera mått genom att ställa in `metrics__reporterType` miljövariabeln på `prometheus` i behållaren skapa alternativ:

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

Måtten är tillgängliga i `5888/metrics` modulen för http och `4438/metrics` för https. Till exempel `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` för http. I det här läget kan en Metrics-modul avsöka slut punkten för att samla in mått som i den här [exempel arkitekturen](https://github.com/veyalla/ehm).

## <a name="available-metrics"></a>Tillgängliga mått

Både ämnen och händelse prenumerationer genererar mått för att ge dig insikter om prestanda för händelse leverans och moduler.

### <a name="topic-metrics"></a>Ämnes mått

| Metric | Beskrivning |
| ------ | ----------- |
| EventsReceived | Antal händelser som publicerats till ämnet
| UnmatchedEvents | Antal händelser som publicerats till ämnet som inte matchar en händelse prenumeration och som tas bort
| SuccessRequests | Antal inkommande publicerings begär Anden som tagits emot av ämnet
| SystemErrorRequests | Antalet inkommande publicerings begär Anden som misslyckats på grund av ett internt systemfel
| UserErrorRequests | Antal inkommande publicerings begär Anden som misslyckats på grund av ett användar fel, till exempel felaktigt felaktig JSON
| SuccessRequestLatencyMs | Svars fördröjning för publicerings förfrågan i millisekunder


### <a name="event-subscription-metrics"></a>Mått för händelse prenumeration

| Metric | Beskrivning |
| ------ | ----------- |
| DeliverySuccessCounts | Antal händelser som har levererats till den konfigurerade slut punkten
| DeliveryFailureCounts | Antal händelser som inte kunde levereras till den konfigurerade slut punkten
| DeliverySuccessLatencyMs | Svars tid för händelser som har levererats i millisekunder
| DeliveryFailureLatencyMs | Svars tid för händelse leverans problem i millisekunder
| SystemDelayForFirstAttemptMs | System fördröjning av händelser före första leverans försöket i millisekunder
| DeliveryAttemptsCount | Antal händelse leverans försök – lyckades och misslyckades
| ExpiredCounts | Antal händelser som har upphört att gälla och inte levererats till den konfigurerade slut punkten