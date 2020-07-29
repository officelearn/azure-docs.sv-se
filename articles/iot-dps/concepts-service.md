---
title: Tjänst koncept i Azure IoT Hub Device Provisioning Service | Microsoft Docs
description: Beskriver tjänst etablerings begrepp som är relaterade till enheter med enhets etablerings tjänsten (DPS) och IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f42502ac4db12a060af5906243d3f8e7584c5df3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "79285219"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>IoT Hub Device Provisioning Service begrepp

IoT Hub Device Provisioning Service är en hjälp tjänst för IoT Hub som du använder för att konfigurera enhets etablering med noll touch till en angiven IoT-hubb. Med enhets etablerings tjänsten kan du [automatiskt etablera](concepts-auto-provisioning.md) miljon tals enheter på ett säkert och skalbart sätt.

Enhets etablering är en process i två delar. Den första delen upprättar den inledande anslutningen mellan enheten och IoT-lösningen genom att *Registrera* enheten. Den andra delen tillämpar rätt *konfiguration* på enheten baserat på de särskilda kraven för lösningen. När båda stegen har slutförts är enheten helt *etablerad*. Enhetsetableringstjänsten automatiserar båda stegen och ger sömlös etablering av enheten.

Den här artikeln ger en översikt över de etablerings koncept som är mest tillämpliga för att hantera *tjänsten*. Den här artikeln är mest relevant för personer som ingår i [Cloud setup-steget](about-iot-dps.md#cloud-setup-step) för att få en enhet redo för distribution.

## <a name="service-operations-endpoint"></a>Slut punkt för tjänst åtgärder

Slut punkten för tjänst åtgärder är slut punkten för att hantera tjänst inställningar och underhålla registrerings listan. Den här slut punkten används endast av tjänst administratören. den används inte av enheter.

## <a name="device-provisioning-endpoint"></a>Slut punkt för enhets etablering

Enhets etablerings slut punkten är den enda slut punkten som alla enheter använder för automatisk etablering. URL: en är samma för alla etablerings tjänst instanser, för att eliminera behovet av att göra om Flash-enheter med ny anslutnings information i scenarier med leverans kedjan. ID-omfånget garanterar klient isolering.

## <a name="linked-iot-hubs"></a>Länkade IoT-hubbar

Enhets etablerings tjänsten kan endast etablera enheter till IoT-hubbar som har länkats till den. Genom att länka en IoT-hubb till en instans av enhets etablerings tjänsten får du Läs-/Skriv behörighet till IoT Hub-enhetens register. med länken kan en enhets etablerings tjänst registrera ett enhets-ID och ange den inledande konfigurationen på enheten. Länkade IoT-hubbar kan finnas i alla Azure-regioner. Du kan länka hubbar i andra prenumerationer till etablerings tjänsten.

## <a name="allocation-policy"></a>Resursallokeringsprincip

Inställningen på service nivå som avgör hur enhets etablerings tjänsten tilldelar enheter till en IoT-hubb. Det finns tre allokeringsprinciper som stöds:

* **Jämt viktad distribution**: länkade IoT-hubbar är lika troligt att enheter har allokerats till dem. Standardvärdet. Om du endast etablerar enheter till en IoT-hubb kan du behålla den här inställningen.

* **Lägsta latens**: enheter allokeras till en IoT-hubb med den lägsta svars tiden för enheten. Om flera länkade IoT-hubbar skulle ge samma lägsta latens, hash-värden för etablerings tjänsten enheter över dessa nav

* **Statisk konfiguration via registrerings listan**: specifikationen av den önskade IoT-hubben i registrerings listan har företräde framför tilldelnings principen på tjänst nivå.

## <a name="enrollment"></a>Registrering

En registrering är en post för enheter eller grupper av enheter som kan registreras via automatisk etablering. Registrerings posten innehåller information om enheten eller gruppen med enheter, inklusive:
- [mekanismen för attestering](concepts-security.md#attestation-mechanism) som används av enheten
- den valfria inledande önskad konfiguration
- önskad IoT Hub
- önskat enhets-ID

Det finns två typer av registreringar som stöds av enhets etablerings tjänsten:

### <a name="enrollment-group"></a>Registrerings grupp

En registrerings grupp är en grupp enheter som delar en särskild mekanism för attestering. Registrerings grupper stöder både X. 509 och symmetrisk. Alla enheter i gruppen X. 509-registrering finns X. 509-certifikat som har signerats av samma rot-eller mellanliggande certifikat utfärdare (CA). Varje enhet i gruppen för symmetrisk nyckel registrering finns SAS-token som härletts från den symmetriska gruppen. Namnet på registrerings gruppen och certifikat namnet måste vara alfanumeriskt, gement och får innehålla bindestreck.

> [!TIP]
> Vi rekommenderar att du använder en registrerings grupp för ett stort antal enheter som delar en önskad inledande konfiguration, eller för enheter som alla kommer till samma klient.

### <a name="individual-enrollment"></a>Individuell registrering

En enskild registrering är en post för en enskild enhet som kan registreras. Enskilda registreringar kan använda antingen X. 509-löv certifikat eller SAS-token (från en fysisk eller virtuell TPM) som attesterings metoder. Registrerings-ID: t i en enskild registrering är alfanumeriskt, gement och får innehålla bindestreck. Enskilda registreringar kan ha angivet önskat enhets-ID för IoT Hub.

> [!TIP]
> Vi rekommenderar att du använder enskilda registreringar för enheter som kräver unika inledande konfigurationer eller för enheter som bara kan autentiseras med SAS-token via TPM-attestering.

## <a name="registration"></a>Registrering

En registrering är en post i en enhet som har registrerats/tillhandahållits till en IoT Hub via enhets etablerings tjänsten. Registrerings poster skapas automatiskt. de kan tas bort, men de kan inte uppdateras.

## <a name="operations"></a>Åtgärder

Åtgärder är fakturerings enheten för enhets etablerings tjänsten. En åtgärd är slutförd slut för ande av en instruktion till tjänsten. Åtgärderna inkluderar enhets registreringar och omregistreringar; åtgärder omfattar även ändringar på tjänst sidan, till exempel att lägga till registrerings List poster och uppdatera poster i registrerings listan.
