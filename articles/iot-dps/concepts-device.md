---
title: Enhetsbegrepp i Etablerar av Azure-enheter | Microsoft-dokument
description: Beskriver begrepp för enhetsetablering som är specifika för enheter med DPS (Device Provisioning Service) och IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f5f931622f793a1146c04403e8c5e1a5ef7a7d62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79285167"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>Självstudier om IoT Hub Device Provisioning-tjänstens begrepp

IoT Hub Device Provisioning Service är en hjälptjänst för IoT Hub som du använder för att konfigurera zero-touch-enhetsetablering till en angiven IoT-hubb. Med enhetsetableringstjänsten kan du etablera miljontals enheter på ett säkert och skalbart sätt.

Den här artikeln innehåller en översikt över *enhetsbegreppen* som ingår i enhetsetablering. Den här artikeln är mest relevant för personer som är involverade i [tillverkningssteget](about-iot-dps.md#manufacturing-step) för att få en enhet redo för distribution.

## <a name="attestation-mechanism"></a>Attesteringsmekanism

Attestation mekanismen är den metod som används för att bekräfta en enhets identitet. Attestation-mekanismen är också relevant för registreringslistan, som talar om för etableringstjänsten vilken metod för attestering som ska användas med en viss enhet.

> [!NOTE]
> IoT Hub använder "autentiseringsschema" för ett liknande koncept i den tjänsten.

Enhetsetableringstjänsten stöder följande former av intyg:
* **X.509-certifikat** baserat på standardflödet för X.509-certifikatautentisering.
* **TPM (Trusted Platform Module)** baserat på en nonce-utmaning med hjälp av TPM-standarden för nycklar för att presentera en signerad SAS-token (Shared Access Signature). Detta kräver inte en fysisk TPM på enheten, men tjänsten förväntar sig att intyga att använda godkännandenyckeln per [TPM-specifikationen](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Symmetrisk nyckel** baserat på [SAS-säkerhetstoken](../iot-hub/iot-hub-devguide-security.md#security-tokens)(Shared Access Signature), som innehåller en hash-signatur och en inbäddad förfallodatum. Mer information finns i [Symmetrisk nyckeltysst .](concepts-symmetric-key-attestation.md)

## <a name="hardware-security-module"></a>Säkerhetsmodul för maskinvara

Maskinvarusäkerhetsmodulen, eller HSM, används för säker, maskinvarubaserad lagring av enhetshemligheter och är den säkraste formen av hemlig lagring. Både X.509-certifikat och SAS-token kan lagras i HSM. HSM kan användas med båda attestation mekanismer som etablering tjänsten stöder.

> [!TIP]
> Vi rekommenderar starkt att du använder en HSM med enheter för att säkert lagra hemligheter på dina enheter.

Enhetshemligheter kan också lagras i programvara (minne), men det är en mindre säker form av lagring än en HSM.

## <a name="registration-id"></a>Registrerings-ID

Registrerings-ID:et används för att unikt identifiera en enhet i enhetsetableringstjänsten. Enhets-ID:t måste vara unikt i [etableringstjänst-ID-scopet](#id-scope). Varje enhet måste ha ett registrerings-ID. Registrerings-ID:t är alfanumeriskt, skiftlägesokänsligt och kan innehålla specialtecken som kolon, period, understreck och bindestreck.

* När det gäller TPM tillhandahålls registrerings-ID av själva TPM:en.
* När det gäller X.509-baserade intyget anges registrerings-ID som certifikatets ämnesnamn.

## <a name="device-id"></a>Enhets-ID

Enhets-ID:t är ID:et som det visas i IoT Hub. Önskat enhets-ID kan anges i registreringsposten, men det behöver inte anges. Att ange önskat enhets-ID stöds bara i enskilda registreringar. Om inget önskat enhets-ID anges i registreringslistan används registrerings-ID:n som enhets-ID när enheten registreras. Läs mer om [enhets-ID:er i IoT Hub](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>ID-scope

ID-scopet tilldelas en enhetsetableringstjänst när den skapas av användaren och används för att unikt identifiera den specifika etableringstjänst som enheten ska registrera genom. ID-scopet genereras av tjänsten och är oföränderlig, vilket garanterar unikhet.

> [!NOTE]
> Unikhet är viktigt för långvariga distributionsoperationer och fusions- och förvärvsscenarier.
