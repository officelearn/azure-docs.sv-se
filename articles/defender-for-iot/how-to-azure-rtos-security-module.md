---
title: Konfigurera och anpassa säkerhetsmodulen för Azure återställnings tider
description: Lär dig hur du konfigurerar och anpassar din säkerhetsmodul för Azure återställnings tider.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 9106e00fe2146978f97b480e3afd3b7ed58c9130
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937779"
---
# <a name="configure-and-customize-security-module-for-azure-rtos-preview"></a>Konfigurera och anpassa säkerhetsmodulen för Azure återställnings tider (för hands version)

Använd följande fil för att konfigurera enhetens beteende.

## <a name="azure_iot_security_moduleincasc_porth"></a>azure_iot_security_module/Inc/asc_port. h

 Standard beteendet för varje konfiguration finns i följande tabeller: 

### <a name="general"></a>Allmänt

| Namn | Typ | Standardvärde | Information |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | Sträng | --- | Unikt id för enheten  |
| ASC_SECURITY_MODULE_PENDING_TIME  | Antal | 300 | Förväntad tid i sekunder för säkerhetsmodulen. Om tiden överskrider tillstånds ändringen för att pausa. |

#### <a name="collection"></a>Samling

| Namn | Typ | Standardvärde | Information |
| - | - | - | - |
| ASC_HIGH_PRIORITY_INTERVAL | Antal | 10 | Samlar in grupp intervall med hög prioritet i sekunder. |
| ASC_MEDIUM_PRIORITY_INTERVAL | Antal | 30 | Samlar in mellanliggande prioritets grupp intervall i sekunder. |
| ASC_LOW_PRIORITY_INTERVAL | Antal | 145 440  | Samlar in grupp intervall med låg prioritet i sekunder. |

#### <a name="collector-network-activity"></a>Insamlarens nätverks aktivitet

Om du vill anpassa konfigurationen för insamlarens nätverks aktivitet använder du följande:

| Namn | Typ | Standardvärde | Information |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Boolesk | falskt | Filtrera `TCP` nätverks aktivitet |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Boolesk | falskt | Filtrera `UDP` nätverks aktivitets händelser |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Boolesk | falskt | Filtrera `ICMP` nätverks aktivitets händelser |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Boolesk | true | Hämta endast utgående unicast-paket, om det är inställt på falskt fånga även sändning och multicast |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Antal | 64 | Maximalt antal IPv4-nätverks händelser som ska lagras i minnet |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Antal | 64  | Maximalt antal IPv6-nätverks händelser att lagra i minnet |


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

Även om det är valfritt och inte obligatoriskt, kan det vara bra att aktivera och konfigurera Log Analytics när du vill undersöka enhets händelser och aktiviteter. Läs mer om hur du konfigurerar och använder [Log Analytics med tjänsten Defender for IoT](how-to-security-data-access.md#log-analytics) för att lära dig mer. 

## <a name="next-steps"></a>Nästa steg

- Granska och anpassa säkerhetsmodulen för [säkerhets aviseringar och rekommendationer](concept-rtos-security-alerts-recommendations.md) för Azure återställnings tider
- Se [säkerhetsmodulen för Azure återställnings tider API](azure-rtos-security-module-api.md) vid behov.

