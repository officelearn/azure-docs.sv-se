---
title: "Koncept i Azure IoT Hub-enhet etablering Service-tjänsten | Microsoft Docs"
description: "Beskriver-etablering koncept som är specifika för enheter med DP- och IoT-hubb"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 10/03/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 96c63e5d0379150ea619dbbe912a21e373f808af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>IoT-hubb enheten etablering-koncept

IoT-hubb Device etablering Service är en helper-tjänsten för IoT-hubb som används för att konfigurera zero touch enhet etablering till en angiven IoT-hubb. Du kan etablera miljontals enheter på en säker och skalbar sätt med tjänsten etablering av enheten.

Enhetsetableringen är en process i två steg. Den första delen är att upprätta den första anslutningen mellan enheten och IoT-lösningen av *registrerar* enheten. Den andra delen tillämpar rätt *configuration* till enheten baserat på de särskilda kraven i lösningen. När båda dessa steg har slutförts, kan vi säga att enheten har blivit fullständigt *etableras*. Etablering av tjänst automatiserar båda dessa steg för att ge en allokering smidigt för enheten.

Den här artikeln ger en översikt över etablering begrepp som är mest användbar för att hantera den *tjänsten*. Den här artikeln är mest relevant för personer som ingår i den [moln steget av installationen](about-iot-dps.md#cloud-setup-step) för att få en enhet som är klar för distribution.

## <a name="service-operations-endpoint"></a>Tjänstslutpunkten åtgärder

Operations tjänstslutpunkten är slutpunkt för att hantera tjänstinställningar för och hantera listan över registrering. Den här slutpunkten används endast av tjänstadministratör; den används inte av enheter.

## <a name="device-provisioning-endpoint"></a>Enheten etablering slutpunkt

Enheten slutpunkt-etablering är den centrala slutpunkten alla enheter kommunicerar med för etablering. URL: en är samma för alla etablering tjänster att eliminera behovet av att reflash enheter med ny anslutningsinformation i kedjan scenarier. Den [ID scope](#id-scope) garanterar klientisolering.

## <a name="linked-iot-hubs"></a>Länkade IoT-hubbar

Etablering av tjänst kan bara etablera enheter till IoT-hubbar som har länkats till den. Länka en IoT-hubb till enheten Etableringstjänsten ger Tjänstbehörigheter för läsning och skrivning till registret för IoT-hubb-enhet. med länken, etablering av tjänst registrera enhets-ID och ange den första konfigurationen i enheten dubbla. Länkade IoT-hubbar kan vara i Azure-region. Du kan länka hubbar i andra prenumerationer till etablering tjänsten.

## <a name="allocation-policy"></a>Princip för resursallokering

Service avgör-inställning som hur enheten Etableringstjänsten tilldelar enheter till en IoT-hubb. Det finns tre resursallokeringsprinciper som stöds:
* **Jämnt viktas distribution**: länkade IoT-hubbar sannolikt lika har enheter som har etablerats till dem. Standardinställningen. Om du etablerar enheter till endast en IoT-hubb kan du behålla den här inställningen.
* **Lägsta fördröjningen**: enheter etableras till en IoT-hubb med den lägsta fördröjningen till enheten. Om flera länkade IoT-hubbar skulle ge samma lägsta svarstid, hashar etablering tjänsten enheter över dessa hubbar
* **Statisk konfiguration via listan registrering**: specificering av önskade IoT-hubben i listan över registrering har högre prioritet än den servicenivå principen.

## <a name="enrollment"></a>Registrering

En registrering är post för enheter eller grupper av enheter som kan vid en tidpunkt register. Registreringsposten innehåller information om enheten eller grupp av enheter, inklusive metoden attestering för enhet(er) och eventuellt inledande önskad konfiguration önskas IoT hub och önskad enhet-ID. Det finns två typer av registreringar som stöds av enheten Etableringstjänsten.

### <a name="enrollment-group"></a>Registrering av grupp

En grupp för registrering är en grupp av enheter som delar en mekanism för specifika attestering. Alla enheter i gruppen registrering finns X.509-certifikat som har signerats av samma rot-CA. Registrering av grupper kan bara använda mekanismen för attestering X.509. Gruppnamn för registrering och certifikatets namn måste vara alfanumeriskt, gemener och får innehålla bindestreck.

> [!TIP]
> Vi rekommenderar en grupp för registrering för ett stort antal enheter som delar en önskad inledande konfiguration eller för enheter alla kommer att samma klientorganisation.

### <a name="individual-enrollment"></a>Enskilda registrering

En enskild registreringen är en post för en enda enhet som kan registrera. Enskilda registreringar kan använda X.509-certifikat eller SAS-token (i TPM verkliga eller virtuella) som mekanismer för attestering. Registrerings-ID i en enskild registrering är alfanumeriska, gemener och får innehålla bindestreck. Enskilda registreringar kanske önskade IoT-hubb enhets-ID angetts.

> [!TIP]
> Vi rekommenderar att du använder enskilda registreringar för enheter som kräver unika första konfigurationer eller för enheter som kan bara använda SAS-token via TPM eller virtuella TPM som metod för attestering.

## <a name="registration"></a>Registrering

En registrering är en post för en enhet som har registrering/etablering till en IoT-hubb via etablering av tjänst. Registreringsposter skapas automatiskt. de kan tas bort, men de kan inte uppdateras.

## <a name="operations"></a>Åtgärder

Åtgärder är enheten Etableringstjänsten fakturering enheten. En åtgärd är slutförande av en instruktion till tjänsten. Åtgärder är bland annat registreringar och omregistreringar av enheter. Åtgärder är även ändringar på tjänstsidan, till exempel tillägg och uppdatering av poster i registreringslistan.
