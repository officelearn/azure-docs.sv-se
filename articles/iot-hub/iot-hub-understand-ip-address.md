---
title: Förstå IP-adressen för din IoT Hub | Microsoft Docs
description: Lär dig hur du frågar din IP-adress för IoT Hub och dess egenskaper. IP-adressen för din IoT Hub kan ändras under vissa scenarier som haveri beredskap eller regional redundans.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 9c9d02eeead4ccbed372e9a55d9b551da83caa39
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642298"
---
# <a name="understanding-the-ip-address-of-your-iot-hub"></a>Förstå IP-adressen för din IoT Hub

IP-adressen för din IoT Hub är en belastningsutjämnad offentlig slut punkt för hubben och inte en dedikerad IP-adress. Adressen bestäms av nätverks adress intervallet för den Azure-region där den distribueras. Den här IP-adressen kan ändras utan föregående meddelande. Nätverks uppdateringar, data Center haveri beredskap eller regional redundans för en IoT Hub kan ändra din IP-adress för IoT Hub. Se [IoT Hub hög tillgänglighet och haveri beredskap](iot-hub-ha-dr.md) för mer information om Azure IoT Hub regional redundans och tillgänglighet.

IP-adressen för din IoT-hubb ändras efter en redundansväxling till en annan region. Du kan testa den här funktionen genom att följa själv studie kursen [utföra en manuell redundansväxling av en IoT-hubb](tutorial-manual-failover.md).

## <a name="discover-your-iot-hub-ip-address"></a>Identifiera din IoT Hub-IP-adress

Du kan identifiera din IoT Hub IP-adress med hjälp av en omvänd DNS-sökning på CNAME ([*IoT-Hub-Name*]. Azure-Devices.net). Du kan använda **nslookup** för att verifiera IP-adressen för en IoT Hub-instans:

```cmd/sh
nslookup {YourIoTHubName}.azure-devices.net
```

Den här IP-adressen kan ändras utan föregående meddelande. I ett haveri-eller katastrof återställnings scenario måste du söka efter IP-adressen för IoT Hub i den sekundära regionen.

## <a name="outbound-firewall-rules-for-iot-hub"></a>Utgående brand Väggs regler för IoT Hub

Försök att skapa brand Väggs regler och filtrering baserat på värd namnet eller domänen för IoT Hub. Om du bara kan tillåta utgående trafik till vissa adresser avsöker du din IoT Hub-IP-adress regelbundet och uppdaterar brand Väggs reglerna.

