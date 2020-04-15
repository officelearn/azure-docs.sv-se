---
title: Anpassningsbara säkerhetsaviseringar
description: Lär dig mer om anpassningsbara säkerhetsaviseringar och rekommenderad reparation med Azure Security Center för IoT-funktioner och -tjänst.
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
ms.date: 03/04/2020
ms.author: mlottner
ms.openlocfilehash: 3b39d70c60a4c9701d0a8bafde17b241fe01cc46
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311641"
---
# <a name="azure-security-center-for-iot-security-alerts"></a>Azure Security Center for IoT-säkerhetsaviseringar

Azure Security Center for IoT analyserar kontinuerligt din IoT-lösning med hjälp av avancerad analys och hotinformation för att varna dig för skadlig aktivitet.

Vi uppmuntrar dig att skapa anpassade aviseringar baserat på din kunskap om förväntat enhetsbeteende för att säkerställa att aviseringar fungerar som de mest effektiva indikatorerna på potentiell kompromettering i din unika organisationsdistribution och landskap.

Följande lista över Azure Security Center för IoT-aviseringar kan definieras av dig baserat på ditt förväntade IoT Hub och/eller enhetsbeteende. Mer information om hur du anpassar varje avisering finns i [skapa anpassade aviseringar](quickstart-create-custom-alerts.md).

## <a name="iot-hub-alerts-available-for-customization"></a>IoT Hub-varningar tillgängliga för anpassning

| Severity | Aviseringsnamn | Datakälla | Beskrivning | Föreslagen reparation|
|---|---|---|---|---|
| Låg      | Anpassad avisering - antalet moln-till-enhetsmeddelanden i AMQP-protokollet ligger utanför det tillåtna intervallet          | IoT Hub     | Antal amqp-protokoll (cloud to device messages) inom ett visst tidsfönster ligger utanför det för närvarande konfigurerade och tillåtna intervallet.||
| Låg      | Anpassad avisering - antalet avvisade moln till enhetsmeddelanden i AMQP-protokollet ligger utanför det tillåtna intervallet | IoT Hub     | Antal amqp-protokoll (cloud to device protocol) som avvisats av enheten, inom ett visst tidsfönster ligger utanför det för närvarande konfigurerade och tillåtna intervallet.||
| Låg      | Anpassad avisering - antalet enheter till molnmeddelanden i AMQP-protokollet ligger utanför det tillåtna intervallet      | IoT Hub     | Mängden enhet till molnmeddelanden (AMQP-protokoll) inom ett visst tidsfönster ligger utanför det för närvarande konfigurerade och tillåtna intervallet.|   |
| Låg      | Anpassad avisering - antalet direkta metoden som anropas ligger utanför det tillåtna intervallet | IoT Hub     | Mängden direkt metod som anropas inom ett visst tidsfönster ligger utanför det för närvarande konfigurerade och tillåtna intervallet.||
| Låg      | Anpassad avisering - antalet filuppladdningar ligger utanför det tillåtna intervallet | IoT Hub     | Mängden filöverföringar inom ett visst tidsfönster ligger utanför det för närvarande konfigurerade och tillåtna intervallet.| |
| Låg      | Anpassad avisering - antalet moln-till-enhetsmeddelanden i HTTP-protokollet ligger utanför det tillåtna intervallet | IoT Hub     | Mängden moln-till-enhetsmeddelanden (HTTP-protokoll) i ett tidsfönster ligger inte i det konfigurerade tillåtna intervallet                                  |
| Låg      | Anpassad avisering - antalet avvisade moln till enhetsmeddelanden i HTTP-protokollet finns inte i det tillåtna intervallet | IoT Hub     | Mängden moln till enhetsmeddelanden (HTTP-protokoll) inom ett visst tidsfönster ligger utanför det för närvarande konfigurerade och tillåtna intervallet. |
| Låg      | Anpassad avisering - antalet enheter till molnmeddelanden i HTTP-protokollet ligger utanför det tillåtna intervallet | IoT Hub| Mängden enhet till molnmeddelanden (HTTP-protokoll) inom ett visst tidsfönster ligger utanför det för närvarande konfigurerade och tillåtna intervallet.|    |
| Låg      | Anpassad avisering - antalet moln-till-enhetsmeddelanden i MQTT-protokollet ligger utanför det tillåtna intervallet | IoT Hub     | Mängden moln-till-enhetsmeddelanden (MQTT-protokoll) inom ett visst tidsfönster ligger utanför det för närvarande konfigurerade och tillåtna intervallet.|   |
| Låg      | Anpassad avisering - antalet avvisade moln till enhetsmeddelanden i MQTT-protokollet ligger utanför det tillåtna intervallet | IoT Hub     | Mängden moln till enhetsmeddelanden (MQTT-protokoll) som avvisas av enheten inom ett visst tidsfönster ligger utanför det för närvarande konfigurerade och tillåtna intervallet. |
| Låg      | Anpassad avisering - antalet enheter till molnmeddelanden i MQTT-protokollet ligger utanför det tillåtna intervallet          | IoT Hub     | Mängden enhet till molnmeddelanden (MQTT-protokoll) inom ett visst tidsfönster ligger utanför det för närvarande konfigurerade och tillåtna intervallet.|
| Låg      | Anpassad avisering - antalet kommandokörensningar ligger utanför det tillåtna intervallet                               | IoT Hub     | Mängden kommandokörensningar inom ett visst tidsfönster ligger utanför det för närvarande konfigurerade och tillåtna intervallet.||
| Låg      | Anpassad avisering - antalet modultvillinguppdateringar ligger utanför det tillåtna intervallet                                       | IoT Hub     | Mängden modultvillinguppdateringar inom ett visst tidsfönster ligger utanför det för närvarande konfigurerade och tillåtna intervallet.|
| Låg      | Anpassad varning - antalet obehöriga åtgärder ligger utanför det tillåtna intervallet  | IoT Hub     | Mängden obehöriga åtgärder inom ett visst tidsfönster ligger utanför det för närvarande konfigurerade och tillåtna intervallet.|
|

## <a name="agent-alerts-available-for-customization"></a>Agentaviseringar tillgängliga för anpassning

| Severity | Aviseringsnamn | Datakälla | Beskrivning | Föreslagen reparation|
|---|---|---|---|---|
| Låg      | Anpassad avisering - antalet aktiva anslutningar ligger utanför det tillåtna intervallet  | Agent       | Antalet aktiva anslutningar inom ett visst tidsfönster ligger utanför det för närvarande konfigurerade och tillåtna intervallet.|  Undersök enhetsloggarna. Lär dig var anslutningen har sitt ursprung och ta reda på om den är godartad eller skadlig. Om skadlig, ta bort eventuell skadlig kod och förstå källa. Om det är godartade lägger du till källan i den tillåtna anslutningslistan.  |
| Låg      | Anpassad avisering – utgående anslutning som skapats till en IP-adress som inte är tillåten                             | Agent       | En utgående anslutning skapades till en IP som ligger utanför den tillåtna IP-listan. |Undersök enhetsloggarna. Lär dig var anslutningen har sitt ursprung och ta reda på om den är godartad eller skadlig. Om skadlig, ta bort eventuell skadlig kod och förstå källa. Om det är godartade lägger du till källan i den tillåtna IP-listan.                        |
| Låg      | Anpassad avisering - antalet misslyckade lokala inloggningar ligger utanför det tillåtna intervallet                               | Agent       | Mängden misslyckade lokala inloggningar inom ett visst tidsfönster ligger utanför det för närvarande konfigurerade och tillåtna intervallet. |   |
| Låg      | Anpassad avisering - inloggning av en användare som inte finns med i den tillåtna användarlistan | Agent       | En lokal användare utanför listan över tillåtna användare, inloggad på enheten.|  Om du sparar rådata navigerar du till ditt logganalyskonto och använder data för att undersöka enheten, identifierar källan och åtgärdar sedan listan tillåt/blockera för dessa inställningar. Om du för närvarande inte sparar rådata går du till enheten och åtgärdar listan tillåt/blockera för dessa inställningar.|
| Låg      | Anpassad avisering - en process har utförts som inte är tillåten | Agent       | En process som inte är tillåten utfördes på enheten. |Om du sparar rådata navigerar du till ditt logganalyskonto och använder data för att undersöka enheten, identifierar källan och åtgärdar sedan listan tillåt/blockera för dessa inställningar. Om du för närvarande inte sparar rådata går du till enheten och åtgärdar listan tillåt/blockera för dessa inställningar.  |
|

## <a name="next-steps"></a>Nästa steg

- Läs om hur du [anpassar en avisering](quickstart-create-custom-alerts.md)
- Översikt över Azure Security [Overview](overview.md) Center för IoT-tjänst
- Läs om hur du [kommer åt dina säkerhetsdata](how-to-security-data-access.md)
- Läs mer om [att undersöka en enhet](how-to-investigate-device.md)
