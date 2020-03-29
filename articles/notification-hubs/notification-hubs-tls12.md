---
title: TLS-uppdateringar för meddelandehubbar
description: Läs mer om stöd för TLS i Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/30/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87309e20efd9d6f8bd1a659451e5a603e6b95bc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908532"
---
# <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

För att säkerställa en högre säkerhetsnivå inaktiverar Notification Hubs stöd för TLS-versionerna 1.0 och 1.1 den 30 april 2020. Dessa äldre protokoll ger svag kryptografi och är sårbara för BEAST- och PUDELattacker. Program som distribueras till enheter som kör Android version 5 eller senare, eller iOS version 5 eller mer, påverkas inte av den här ändringen eftersom dessa operativsystem stöder TLS 1.2 och klienten och servern kommer att förhandla fram den högsta versionen av protokoll vid anslutning.

Vi rekommenderar att du granskar alla dina program som använder Azure Notification Hubs för att säkerställa att de använder de mest tillämpliga biblioteken och TLS-staplarna som stöder TLS 1.2.

## <a name="update-apps"></a>Uppdatera appar

Du kan se till att dina iOS-appar använder TLS 1.2 med Apples säkerhetsfunktion för nätverk som kallas App Transport Security (ATS). ATS kan inte användas för SDK:er som är äldre än iOS 9.0 eller macOS 10.11, och du kan läsa mer om det från [Apples dokumentation](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

För Android-program som använder SSLSocket-instanser anger du aktiverade protokoll för varje SSLSocket-instans som anges i [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)).

Tabellen på stödsidan för [TLS-protokollkompatibilitet](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) hjälper till att mappa operativsystem med kompatibla TLS-versioner.

Mer information finns i översikten över [stödet för TLS-protokoll i Windows](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows).

## <a name="next-steps"></a>Nästa steg

- [Översikt över meddelandehubbar](notification-hubs-push-notification-overview.md)