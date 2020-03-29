---
title: Konfiguration – Azure Event Grid IoT Edge | Microsoft-dokument
description: Konfiguration i händelserutnät på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 841b5092775353bbe3340dbbd55610026f998a15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76846469"
---
# <a name="event-grid-configuration"></a>Konfiguration av händelserutnät

Event Grid innehåller många konfigurationer som kan ändras per miljö. Följande avsnitt är en referens till alla tillgängliga alternativ och deras standardvärden.

## <a name="tls-configuration"></a>TLS-konfiguration

Mer information om klientautentisering i allmänhet finns i [Säkerhet och autentisering](security-authentication.md). Exempel på dess användning finns i [den här artikeln](configure-api-protocol.md).

| Egenskapsnamn | Beskrivning |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| TLS-principen för modulen Händelserutnät. Standardvärdet är endast HTTPS.
|`inbound__serverAuth__serverCert__source`| Källa till servercertifikat som används av event grid-modulen för dess TLS-konfiguration. Standardvärdet är IoT Edge.

## <a name="incoming-client-authentication"></a>Autentisering av inkommande klient

Mer information om klientautentisering i allmänhet finns i [Säkerhet och autentisering](security-authentication.md). Exempel finns i [den här artikeln](configure-client-auth.md).

| Egenskapsnamn | Beskrivning |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| Så här aktiverar/inaktiverar du certifikatbaserad klientautentisering. Standardvärdet är sant.
|`inbound__clientAuth__clientCert__source`| Källa för validering av klientcertifikat. Standardvärdet är IoT Edge.
|`inbound__clientAuth__clientCert__allowUnknownCA`| Princip för att tillåta ett självsignerat klientcertifikat. Standardvärdet är sant.
|`inbound__clientAuth__sasKeys__enabled`| Så här aktiverar/inaktiverar du SAS-nyckelbaserad klientautentisering. Standardvärdet är inaktiverat.
|`inbound__clientAuth__sasKeys__key1`| Ett av värdena för att validera inkommande begäranden.
|`inbound__clientAuth__sasKeys__key2`| Valfritt andra värde för att validera inkommande begäranden.

## <a name="outgoing-client-authentication"></a>Autentisering av utgående klient
Mer information om klientautentisering i allmänhet finns i [Säkerhet och autentisering](security-authentication.md). Exempel finns i [den här artikeln](configure-identity-auth.md).

| Egenskapsnamn | Beskrivning |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| Så här aktiverar/inaktiverar du att bifoga ett identitetscertifikat för utgående begäranden. Standardvärdet är sant.
|`outbound__clientAuth__clientCert__source`| Källa för hämtning av eventrutnätsmodulens utgående certifikat. Standardvärdet är IoT Edge.

## <a name="webhook-event-handlers"></a>Webhook-händelsehanterare

Mer information om klientautentisering i allmänhet finns i [Säkerhet och autentisering](security-authentication.md). Exempel finns i [den här artikeln](configure-webhook-subscriber-auth.md).

| Egenskapsnamn | Beskrivning |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| Policy för att kontrollera om endast HTTPS-prenumeranter tillåts. Standardvärdet är sant (endast HTTPS).
|`outbound__webhook__skipServerCertValidation`| Flagga för att styra om abonnentens certifikat ska valideras. Standardvärdet är sant.
|`outbound__webhook__allowUnknownCA`| Princip för att kontrollera om ett självsignerat certifikat kan visas av en prenumerant. Standardvärdet är sant. 

## <a name="delivery-and-retry"></a>Leverans och nytt försök

Mer information om den här funktionen i allmänhet finns i [Leverans och försök igen](delivery-retry.md).

| Egenskapsnamn | Beskrivning |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Maximalt antal försök att leverera en händelse. Standardvärdet är 30.
| `broker__defaultEventTimeToLiveInSeconds` | Time-to-live (TTL) i sekunder efter vilken en händelse kommer att släppas om den inte levereras. Standardvärdet är **7200** sekunder

## <a name="output-batching"></a>Batchbearbetning av utdata

Mer information om den här funktionen i allmänhet finns i [Batchbearbetning av leverans och utflöde](delivery-output-batching.md).

| Egenskapsnamn | Beskrivning |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | Maximalt värde som `ApproxBatchSizeInBytes` tillåts för vredet. Standardvärdet `1_058_576`är .
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Maximalt värde som `MaxEventsPerBatch` tillåts för vredet. Standardvärdet `50`är .
| `broker__defaultMaxBatchSizeInBytes` | Maximal storlek för `MaxEventsPerBatch` leveransbegäran när endast anges. Standardvärdet `1_058_576`är .
| `broker__defaultMaxEventsPerBatch` | Maximalt antal händelser som ska läggas `MaxBatchSizeInBytes` till i en batch när endast anges. Standardvärdet `10`är .

## <a name="metrics"></a>Mått

Mer information om hur du använder mått med Händelserutnät på IoT Edge finns i [övervaka ämnen och prenumerationer](monitor-topics-subscriptions.md)

| Egenskapsnamn | Beskrivning |
| ---------------- | ------------ |
| `metrics__reporterType` | Reportertyp för måtten enpoint. Standard `none` är och inaktiverar mått. Inställning `prometheus` för att aktivera mått i Prometheus-utläggningsformatet.