---
title: Koncept i Azure IoT Hub Device Provisioning Service-tjänsten | Microsoft Docs
description: Beskriver tjänsten etablering begrepp som är specifika för enheter med Device Provisioning-tjänsten och IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 4a4f53f991355e634e8139f9e90bec6c508a527d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792755"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>IoT Hub Device Provisioning Service-koncept

IoT Hub Device Provisioning Service är en hjälptjänst för IoT-hubb som används för att konfigurera zero touch-enhetsetablering till en angiven IoT-hubb. Med Device Provisioning-tjänsten, kan du [automatiskt etablera](concepts-auto-provisioning.md) miljontals enheter på ett säkert och skalbart sätt.

Enhetsetablering är en process i två steg. Den första delen att etablera den första anslutningen mellan enheten och IoT-lösning genom att *registrerar* enheten. Den andra delen är tillämpa rätt *configuration* till baserat på de specifika kraven för lösningen. När båda stegen utförts, enheten har blivit fullständigt *etablerade*. Enhetsetableringstjänsten automatiserar båda stegen och ger sömlös etablering av enheten.

Den här artikeln ger en översikt över de etablering begrepp som är mest användbar för att hantera den *service*. Den här artikeln är mest relevant för personer som ingår i den [molnet installationssteget](about-iot-dps.md#cloud-setup-step) för att få en enhet som är klar för distribution.

## <a name="service-operations-endpoint"></a>Åtgärder för tjänstslutpunkt

Operations tjänstslutpunkten är slutpunkten för att hantera tjänstinställningar för och underhålla registreringslistan. Den här slutpunkten används endast av tjänstadministratör; den används inte av enheter.

## <a name="device-provisioning-endpoint"></a>Etablering enhetsslutpunkt

Etablering enhetsslutpunkt är den enda slutpunkt som alla enheter använder för automatisk etablering. URL: en är detsamma för alla tjänstinstanser för enhetsetablering, att eliminera behovet av att reflash enheter med nya anslutningsinformationen i kedjan scenarier. ID-omfång säkerställer klientisolering.

## <a name="linked-iot-hubs"></a>Länkade IoT-hubbar

Device Provisioning-tjänsten kan endast etablera enheter till IoT-hubbar som har länkats till den. Länka en IoT hub till en instans av Device Provisioning-tjänsten ger Läs-/ skrivbehörigheter tjänsten till IoT-hubbens enhetsregister; med länken som en Device Provisioning-tjänsten registrera ett enhets-ID och ange den första konfigurationen i enhetstvillingen. Länkade IoT-hubbar kan finnas i valfri Azure-region. Du kan länka hubbar i andra prenumerationer till din etableringstjänst.

## <a name="allocation-policy"></a>Allokeringsprincip

Service avgör-inställning som hur enheter tilldelas till en IoT-hubb i Device Provisioning-tjänsten. Det finns tre allokeringsprinciper som stöds:

* **Jämnt viktad distribution**: länkade IoT-hubbar är lika sannolikt att enheter etablerade till sig. Standardinställningen. Om du endast etablerar enheter till en IoT-hubb kan du behålla den här inställningen.

* **Kortast svarstid**: enheter etableras till en IoT-hubb med kortast svarstid till enheten. Om flera länkade IoT-hubbar skulle ge samma lägsta svarstid, hashar etableringstjänsten enheter över dessa hubbar

* **Statisk konfiguration via registreringslistan**: specifikation av önskad IoT-hubben på registreringslistan har högre prioritet än allokeringsprincipen på tjänstnivå.

## <a name="enrollment"></a>Registrering

En registrering är en post för enheter eller grupper av enheter som kan registreras via automatisk etablering. Registreringsposten innehåller information om enhet eller grupp av enheter, inklusive:
- den [attesteringsmetod](concepts-security.md#attestation-mechanism) används av enheten
- valfritt inledande önskad konfiguration
- önskad IoT hub
- önskat enhets-ID

Det finns två typer av registrering stöds av Device Provisioning-tjänsten:

### <a name="enrollment-group"></a>Grupp för registrering

En grupp för registrering är en grupp av enheter som delar en specifik attesteringsmekanism. Alla enheter i registreringsgruppen presentera X.509-certifikat som har signerats av samma rot- eller mellanliggande certifikatutfärdare (CA). Registrering av grupper kan bara använda mekanismen för X.509-attestering. Registreringsgruppnamnet och certifikatets namn måste vara alfanumeriskt, gemener och får innehålla bindestreck.

> [!TIP]
> Vi rekommenderar en registreringsgrupp för ett stort antal enheter som delar en önskad inledande konfiguration eller för enheter ska till samma klient.

### <a name="individual-enrollment"></a>Enskild registrering

En enskild registrering är en post för en enskild enhet som kan registreras. Enskilda registreringar kan använda antingen löv X.509-certifikat eller SAS-token (från en fysisk eller virtuell TPM) som attesteringsmekanismer. Registrerings-ID i en enskild registrering är alfanumeriskt, gemener och får innehålla bindestreck. Enskilda registreringar kan ha angivet önskat enhets-ID för IoT Hub.

> [!TIP]
> Vi rekommenderar att du använder enskilda registreringar för enheter som kräver unika första konfigurationer eller för enheter som endast kan autentiseras med hjälp av SAS-token via TPM-attestering.

## <a name="registration"></a>Registrering

En registrering är en post för en enhet som har registreras/driftsättning till en IoT Hub via Device Provisioning-tjänsten. Registreringsposter skapas automatiskt. de kan tas bort, men de kan inte uppdateras.

## <a name="operations"></a>Åtgärder

Åtgärder är faktureringsenheten för Device Provisioning-tjänsten. En åtgärd har slutförts och lyckats en instruktion till tjänsten. Åtgärder är bland annat registreringar och omregistreringar av enheter. Åtgärder är även ändringar på tjänstsidan, till exempel tillägg och uppdatering av poster i registreringslistan.
