---
title: Tjänstbegrepp i Azure IoT Hub Device Provisioning Service | Microsoft-dokument
description: Beskriver koncept för tillhandahållande av tjänster som är specifika för enheter med DPS (Device Provisioning Service) och IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f42502ac4db12a060af5906243d3f8e7584c5df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79285219"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>IoT Hub Device Provisioning Service begrepp

IoT Hub Device Provisioning Service är en hjälptjänst för IoT Hub som du använder för att konfigurera zero-touch-enhetsetablering till en angiven IoT-hubb. Med enhetsetableringstjänsten kan du tillhandahålla miljontals enheter [automatiskt](concepts-auto-provisioning.md) på ett säkert och skalbart sätt.

Enhetsetablering är en process i två delar. Den första delen är att upprätta den första anslutningen mellan enheten och IoT-lösningen genom *att registrera* enheten. Den andra delen är att tillämpa rätt *konfiguration* på enheten baserat på de specifika kraven i lösningen. När båda stegen har slutförts har enheten *etablerats*helt. Enhetsetableringstjänsten automatiserar båda stegen och ger sömlös etablering av enheten.

Den här artikeln innehåller en översikt över de etableringskoncept som är mest tillämpliga för att hantera *tjänsten*. Den här artikeln är mest relevant för personer som är involverade i [molnet setup steg](about-iot-dps.md#cloud-setup-step) för att få en enhet redo för distribution.

## <a name="service-operations-endpoint"></a>Slutpunkt för tjänståtgärder

Slutpunkten för tjänståtgärder är slutpunkten för att hantera tjänstinställningarna och underhålla registreringslistan. Den här slutpunkten används endast av tjänstadministratören. den används inte av enheter.

## <a name="device-provisioning-endpoint"></a>Slutpunkt för etablerande av enhet

Slutpunkten för enhetsetablering är den enda slutpunkten som alla enheter använder för automatisk etablering. URL:en är densamma för alla etableringstjänstinstanser, för att eliminera behovet av att reflash-enheter med ny anslutningsinformation i scenarier för leveranskedjan. ID-scopet säkerställer klientisolering.

## <a name="linked-iot-hubs"></a>Länkade IoT-hubbar

Enhetsetableringstjänsten kan bara etablera enheter till IoT-hubbar som har länkats till den. Genom att länka en IoT-hubb till en instans av tjänsten Enhetsetablering får tjänsten läs-/skrivbehörighet till IoT-hubbens enhetsregister. med länken kan en enhetsetableringstjänst registrera ett enhets-ID och ställa in den första konfigurationen i enhetstvillingen. Länkade IoT-hubbar kan finnas i alla Azure-regioner. Du kan länka nav i andra prenumerationer till din etableringstjänst.

## <a name="allocation-policy"></a>Fördelningspolicy

Inställningen på tjänstnivå som avgör hur enhetsetableringstjänsten tilldelar enheter till en IoT-hubb. Det finns tre allokeringsprinciper som stöds:

* **Jämnt viktad distribution**: länkade IoT-hubbar är lika benägna att ha enheter som är etablerade till dem. Standardinställningen. Om du endast etablerar enheter till en IoT-hubb kan du behålla den här inställningen.

* **Lägsta svarstid:** enheter etableras till en IoT-hubb med den lägsta svarstiden till enheten. Om flera länkade IoT-hubbar skulle ge samma lägsta svarstid, hashar etableringstjänsten enheter över dessa hubbar

* **Statisk konfiguration via registreringslistan**: specifikationen av den önskade IoT-hubben i registreringslistan har företräde framför allokeringsprincipen på tjänstnivå.

## <a name="enrollment"></a>Registrering

En registrering är en post för enheter eller grupper av enheter som kan registrera sig genom automatisk etablering. Registreringsposten innehåller information om enheten eller gruppen av enheter, inklusive:
- [den attestationmekanism](concepts-security.md#attestation-mechanism) som används av enheten
- den valfria ursprungliga önskade konfigurationen
- önskat IoT-nav
- önskat enhets-ID

Det finns två typer av registreringar som stöds av enhetsetableringstjänsten:

### <a name="enrollment-group"></a>Registreringsgrupp

En registreringsgrupp är en grupp enheter som delar en specifik attestation-mekanism. Registreringsgrupper stöder både X.509 och symmetriska. Alla enheter i X.509-registreringsgruppen presenterar X.509-certifikat som har signerats av samma rot- eller mellanliggande certifikatutfärdare. Varje enhet i den symmetriska nyckelregistreringsgruppen presenterar SAS-token som härleds från gruppens symmetriska nyckel. Registreringsgruppens namn och certifikatnamn måste vara alfanumeriska, gemener och kan innehålla bindestreck.

> [!TIP]
> Vi rekommenderar att du använder en registreringsgrupp för ett stort antal enheter som delar en önskad inledande konfiguration, eller för enheter som alla går till samma klient.

### <a name="individual-enrollment"></a>Individuell registrering

En enskild registrering är en post för en enda enhet som kan registrera. Enskilda registreringar kan använda antingen X.509 leaf-certifikat eller SAS-token (från en fysisk eller virtuell TPM) som attesteringsmekanismer. Registrerings-ID:t i en enskild registrering är alfanumeriskt, gemener och kan innehålla bindestreck. Enskilda registreringar kan ha angivet önskat enhets-ID för IoT Hub.

> [!TIP]
> Vi rekommenderar att du använder enskilda registreringar för enheter som kräver unika inledande konfigurationer eller för enheter som bara kan autentisera med SAS-token via TPM-intyg.

## <a name="registration"></a>Registrering

En registrering är posten för en enhet som har registrerats/etablerats på en IoT-hubb via enhetsetableringstjänsten. Registreringsposter skapas automatiskt. de kan tas bort, men de kan inte uppdateras.

## <a name="operations"></a>Åtgärder

Åtgärder är faktureringsenheten för enhetsetableringstjänsten. En åtgärd är att slutföra en instruktion till tjänsten. Verksamheten omfattar enhetsregistreringar och omregistreringar. Åtgärder omfattar även ändringar på tjänstsidan, till exempel lägga till registreringslistposter och uppdatera registreringslistposter.
