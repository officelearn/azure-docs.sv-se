---
title: Konfigurera och anpassa säkerhetsmodulen för Azure återställnings tider
description: Lär dig hur du konfigurerar och anpassar din säkerhetsmodul för Azure återställnings tider.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: eed0422f574e54ff6d7df486b4929850a26418fa
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514932"
---
# <a name="configure-and-customize-security-module-for-azure-rtos-preview"></a>Konfigurera och anpassa säkerhetsmodulen för Azure återställnings tider (för hands version)

Använd följande fil för att konfigurera enhetens beteende.

## <a name="azure_iot_security_moduleincasc_porth"></a>azure_iot_security_module/Inc/asc_port. h

 Standard beteendet för varje konfiguration finns i följande tabeller: 

### <a name="general"></a>Allmänt

| Namn | Typ | Standardvärde | Information |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | Sträng | --- | Unikt id för enheten  |
| ASC_SECURITY_MODULE_PENDING_TIME  | Tal | 300 | Förväntad tid i sekunder för säkerhetsmodulen. Om tiden överskrider tillstånds ändringen för att pausa. |

#### <a name="collection"></a>Samling

| Namn | Typ | Standardvärde | Information |
| - | - | - | - |
| ASC_HIGH_PRIORITY_INTERVAL | Tal | 10 | Samlar in grupp intervall med hög prioritet i sekunder. |
| ASC_MEDIUM_PRIORITY_INTERVAL | Tal | 30 | Samlar in mellanliggande prioritets grupp intervall i sekunder. |
| ASC_LOW_PRIORITY_INTERVAL | Tal | 145 440  | Samlar in grupp intervall med låg prioritet i sekunder. |

#### <a name="collector-network-activity"></a>Insamlarens nätverks aktivitet

Om du vill anpassa konfigurationen för insamlarens nätverks aktivitet använder du följande:

| Namn | Typ | Standardvärde | Information |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Boolesk | falskt | Filtrera `TCP` nätverks aktivitet |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Boolesk | falskt | Filtrera `UDP` nätverks aktivitets händelser |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Boolesk | falskt | Filtrera `ICMP` nätverks aktivitets händelser |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Boolesk | true | Hämta endast utgående unicast-paket, om det är inställt på falskt fånga även sändning och multicast |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Tal | 64 | Maximalt antal IPv4-nätverks händelser som ska lagras i minnet |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Tal | 64  | Maximalt antal IPv6-nätverks händelser att lagra i minnet |


## <a name="compile-flags"></a>Kompilera flaggor
Med compile Flags kan du åsidosätta de fördefinierade konfigurationerna.

### <a name="collectors"></a>Insamlare
| Namn | Typ | Standardvärde | Information |
| - | - | - | - |
| collector_heartbeat_enabled | Boolesk | ON | Aktivera pulsslags insamlaren |
| collector_network_activity_enabled | Boolesk | ON | Aktivera insamlaren av nätverks aktivitet |
| collector_system_information_enabled | Boolesk | ON | Aktivera system informations insamlaren |

## <a name="supported-security-alerts-and-recommendations"></a>Säkerhets aviseringar och rekommendationer som stöds

Säkerhetsmodulen för Azure återställnings tider har stöd för vissa säkerhets aviseringar och rekommendationer. Se till att [Granska och anpassa relevanta aviserings-och rekommendations värden](concept-rtos-security-alerts-recommendations.md) för din tjänst.

## <a name="log-analytics-optional"></a>Log Analytics (valfritt)

Även om det är valfritt och inte obligatoriskt, kan det vara bra att aktivera och konfigurera Log Analytics när du vill undersöka enhets händelser och aktiviteter. Läs mer om hur du konfigurerar och använder [Log Analytics med tjänsten Azure Security Center for IoT](how-to-security-data-access.md#log-analytics) för att lära dig mer. 

## <a name="next-steps"></a>Nästa steg

- Granska och anpassa säkerhetsmodulen för [säkerhets aviseringar och rekommendationer](concept-rtos-security-alerts-recommendations.md) för Azure återställnings tider
- Se [säkerhetsmodulen för Azure återställnings tider API](azure-rtos-security-module-api.md) vid behov.

