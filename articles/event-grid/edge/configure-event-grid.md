---
title: Konfiguration – Azure Event Grid IoT Edge | Microsoft Docs
description: Konfiguration i Event Grid på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a9bac9a84e1ba034b011691ae82a1cb67eb71af0
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992436"
---
# <a name="event-grid-configuration"></a>Event Grid konfiguration

Event Grid innehåller många konfigurationer som kan ändras per miljö. Följande avsnitt är en referens till alla tillgängliga alternativ och standardvärden.

## <a name="tls-configuration"></a>TLS-konfiguration

Mer information om klientautentisering i allmänhet finns i [säkerhet och autentisering](security-authentication.md). Exempel på användningen finns i [den här artikeln](configure-api-protocol.md).

| Egenskaps namn | Beskrivning |
| ---------------- | ------------ |
|`inbound:serverAuth:tlsPolicy`| TLS-princip för Event Grid-modulen. Standardvärdet är endast HTTPS.
|`inbound:serverAuth:serverCert:source`| Källa för Server certifikat som används av Event Grid-modulen för dess TLS-konfiguration. Standardvärdet är IoT Edge.

## <a name="incoming-client-authentication"></a>Inkommande klientautentisering

Mer information om klientautentisering i allmänhet finns i [säkerhet och autentisering](security-authentication.md). Du hittar exempel i [den här artikeln](configure-client-auth.md).

| Egenskaps namn | Beskrivning |
| ---------------- | ------------ |
|`inbound:clientAuth:clientCert:enabled`| Aktivera/inaktivera certifikatbaserad klientautentisering. Standardvärdet är true.
|`inbound:clientAuth:clientCert:source`| Källa för validering av klient certifikat. Standardvärdet är IoT Edge.
|`inbound:clientAuth:clientCert:allowUnknownCA`| Princip för att tillåta ett självsignerat klient certifikat. Standardvärdet är true.
|`inbound:clientAuth:sasKeys:enabled`| Aktivera/inaktivera SAS-baserad klientautentisering. Standardvärdet är inaktiverat.
|`inbound:clientAuth:sasKeys:key1`| Ett av värdena för att validera inkommande begär Anden.
|`inbound:clientAuth:sasKeys:key2`| Valfritt andra värde för att validera inkommande begär Anden.

## <a name="outgoing-client-authentication"></a>Utgående klientautentisering
Mer information om klientautentisering i allmänhet finns i [säkerhet och autentisering](security-authentication.md). Du hittar exempel i [den här artikeln](configure-identity-auth.md).

| Egenskaps namn | Beskrivning |
| ---------------- | ------------ |
|`outbound:clientAuth:clientCert:enabled`| Aktivera/inaktivera anslutning av ett identitets certifikat för utgående begär Anden. Standardvärdet är true.
|`outbound:clientAuth:clientCert:source`| Källa för att hämta Event Grid modulens utgående certifikat. Standardvärdet är IoT Edge.

## <a name="webhook-event-handlers"></a>Webhook-händelse hanterare

Mer information om klientautentisering i allmänhet finns i [säkerhet och autentisering](security-authentication.md). Du hittar exempel i [den här artikeln](configure-webhook-subscriber-auth.md).

| Egenskaps namn | Beskrivning |
| ---------------- | ------------ |
|`outbound:webhook:httpsOnly`| Princip för att kontrol lera om endast HTTPS-prenumeranter kommer att tillåtas. Standardvärdet är sant (endast HTTPS).
|`outbound:webhook:skipServerCertValidation`| Flagga för att kontrol lera om prenumerantens certifikat ska verifieras. Standardvärdet är true.
|`outbound:webhook:allowUnknownCA`| Princip för att kontrol lera om ett självsignerat certifikat kan visas av en prenumerant. Standardvärdet är true. 

## <a name="delivery-and-retry"></a>Leverans och nytt försök

Läs mer om den här funktionen i allmänhet i avsnittet om [leverans och försök igen](delivery-retry.md).

| Egenskaps namn | Beskrivning |
| ---------------- | ------------ |
| `broker:defaultMaxDeliveryAttempts` | Maximalt antal försök att leverera en händelse. Standardvärdet är 30.
| `broker:defaultEventTimeToLiveInSeconds` | TTL (Time-to-Live) i sekunder efter vilken en händelse tas bort om den inte levereras. Standardvärdet är **7200** sekunder

## <a name="output-batching"></a>Batching av utdata

Om du vill lära dig mer om den här funktionen i allmänhet, se [leverans-och utmatnings batch](delivery-output-batching.md).

| Egenskaps namn | Beskrivning |
| ---------------- | ------------ |
| `api:deliveryPolicyLimits:maxBatchSizeInBytes` | Högsta tillåtna värde för `ApproxBatchSizeInBytes` ratt. Standardvärdet är `1_058_576`.
| `api:deliveryPolicyLimits:maxEventsPerBatch` | Högsta tillåtna värde för `MaxEventsPerBatch` ratt. Standardvärdet är `50`.
| `broker:defaultMaxBatchSizeInBytes` | Maximal storlek för leverans förfrågan när endast `MaxEventsPerBatch` anges. Standardvärdet är `1_058_576`.
| `broker:defaultMaxEventsPerBatch` | Maximalt antal händelser som ska läggas till i en batch när endast `MaxBatchSizeInBytes` anges. Standardvärdet är `10`.
