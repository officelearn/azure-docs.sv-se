---
title: Koncept i Azure IoT Hub-enhet etablering Service-tjänsten | Microsoft Docs
description: Beskriver-etablering koncept som är specifika för enheter med DP- och IoT-hubb
services: iot-dps
keywords: ''
author: nberdy
ms.author: nberdy
ms.date: 03/30/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: d2bc58514ea716954ec3ac96151549168fedc2ed
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>IoT-hubb enheten etablering-koncept

IoT-hubb Device etablering Service är en helper-tjänsten för IoT-hubb som används för att konfigurera zero touch enhet etablering till en angiven IoT-hubb. Med tjänsten etablering enhet kan du [automatiskt etablera](concepts-auto-provisioning.md) miljoner enheter på en säker och skalbar sätt.

Enhetsetableringen är en process i två steg. Den första delen är att upprätta den första anslutningen mellan enheten och IoT-lösningen av *registrerar* enheten. Den andra delen tillämpar rätt *configuration* till enheten baserat på de särskilda kraven i lösningen. När båda dessa steg har utförts, enheten har blivit fullständigt *etableras*. Enhetsetableringstjänsten automatiserar båda stegen och ger sömlös etablering av enheten.

Den här artikeln ger en översikt över etablering begrepp som är mest användbar för att hantera den *tjänsten*. Den här artikeln är mest relevant för personer som ingår i den [moln steget av installationen](about-iot-dps.md#cloud-setup-step) för att få en enhet som är klar för distribution.

## <a name="service-operations-endpoint"></a>Tjänstslutpunkten åtgärder

Operations tjänstslutpunkten är slutpunkt för att hantera tjänstinställningar för och hantera listan över registrering. Den här slutpunkten används endast av tjänstadministratör; den används inte av enheter.

## <a name="device-provisioning-endpoint"></a>Enheten etablering slutpunkt

Enheten etablering slutpunkten är den enda slutpunkt alla enheter som ska användas för automatisk etablering. Webbadressen är samma för alla etablering tjänstinstanser att eliminera behovet av att reflash enheter med ny anslutningsinformation i kedjan scenarier. Den [ID scope](#id-scope) garanterar klientisolering.

## <a name="linked-iot-hubs"></a>Länkade IoT-hubbar

Etablering av tjänst kan bara etablera enheter till IoT-hubbar som har länkats till den. Länka en IoT-hubb till enheten Etableringstjänsten ger Tjänstbehörigheter för läsning och skrivning till registret för IoT-hubb-enhet. med länken, etablering av tjänst registrera enhets-ID och ange den första konfigurationen i enheten dubbla. Länkade IoT-hubbar kan vara i Azure-region. Du kan länka hubbar i andra prenumerationer till etablering tjänsten.

## <a name="allocation-policy"></a>Princip för resursallokering

Service avgör-inställning som hur enheten Etableringstjänsten tilldelar enheter till en IoT-hubb. Det finns tre allokeringsprinciper som stöds:
* **Jämnt viktas distribution**: länkade IoT-hubbar sannolikt lika har enheter som har etablerats till dem. Standardinställningen. Om du endast etablerar enheter till en IoT-hubb kan du behålla den här inställningen.
* **Lägsta fördröjningen**: enheter etableras till en IoT-hubb med den lägsta fördröjningen till enheten. Om flera länkade IoT-hubbar skulle ge samma lägsta svarstid, hashar etablering tjänsten enheter över dessa hubbar
* **Statisk konfiguration via listan registrering**: specificering av önskade IoT-hubben i listan över registrering har högre prioritet än den servicenivå principen.

## <a name="enrollment"></a>Registrering

En registrering är post för enheter eller grupper av enheter som kan registrera genom automatisk etablering. Registreringsposten innehåller information om enhet eller grupp av enheter, inklusive:
- den [attestering mekanism](concepts-security.md#attestation-mechanism) används av enheten
- valfria inledande önskad konfiguration
- önskad IoT-hubb
- önskad enhets-ID

Det finns två typer av registreringar som stöds av enheten etablering av tjänst:

### <a name="enrollment-group"></a>Registrering av grupp

En grupp för registrering är en grupp av enheter som delar en mekanism för specifika attestering. Alla enheter i gruppen registrering finns X.509-certifikat som har signerats av samma rot- eller mellanliggande Certifikatutfärdare. Registrering av grupper kan bara använda mekanismen för attestering X.509. Gruppnamn för registrering och certifikatets namn måste vara alfanumeriskt, gemener och får innehålla bindestreck.

> [!TIP]
> Vi rekommenderar en grupp för registrering för ett stort antal enheter som delar en önskad inledande konfiguration eller för enheter alla kommer att samma klientorganisation.

### <a name="individual-enrollment"></a>Enskilda registrering

En enskild registreringen är en post för en enda enhet som kan registrera. Enskilda registreringar kan använda löv X.509-certifikat eller SAS-token (från en fysisk eller virtuell TPM) som mekanismer för attestering. Registrerings-ID i en enskild registrering är alfanumeriska, gemener och får innehålla bindestreck. Enskilda registreringar kan ha angivet önskat enhets-ID för IoT Hub.

> [!TIP]
> Vi rekommenderar att du använder enskilda registreringar för enheter som kräver unika första konfigurationer eller för enheter som endast kan autentiseras med hjälp av SAS-token via TPM-nyckelattestering.

## <a name="registration"></a>Registrering

En registrering är en post för en enhet som har registrering/etablering till en IoT-hubb via etablering av tjänst. Registreringsposter skapas automatiskt. de kan tas bort, men de kan inte uppdateras.

## <a name="operations"></a>Åtgärder

Åtgärder är enheten Etableringstjänsten fakturering enheten. En åtgärd är slutförande av en instruktion till tjänsten. Åtgärder är bland annat registreringar och omregistreringar av enheter. Åtgärder är även ändringar på tjänstsidan, till exempel tillägg och uppdatering av poster i registreringslistan.
