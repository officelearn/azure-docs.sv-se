---
title: Enhets koncept i Azure Device Provisioning | Microsoft Docs
description: Beskriver de koncept för enhets etablering som är särskilt för enheter med enhets etablerings tjänsten och IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 8ea1be02dee0e0ef00010e8ac7a4dfb75eadbe96
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173389"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>Koncept för IoT Hub Device Provisioning Service enhet

IoT Hub Device Provisioning Service är en hjälp tjänst för IoT Hub som du använder för att konfigurera enhets etablering med noll touch till en angiven IoT-hubb. Med enhetsetableringstjänsten kan du etablera miljontals enheter på ett säkert och skalbart sätt.

Den här artikeln ger en översikt över de *enhets* koncept som ingår i enhets etableringen. Den här artikeln är mest relevant för personer som ingår i [tillverknings steget](about-iot-dps.md#manufacturing-step) för att få en enhet redo för distribution.

## <a name="attestation-mechanism"></a>Mekanism för attestering

Mekanismen för attestering är den metod som används för att bekräfta en enhets identitet. Mekanismen för attestering är också relevant för registrerings listan, som talar om för etablerings tjänsten vilken metod som attesteringen ska använda med en specifik enhet.

> [!NOTE]
> IoT Hub använder "autentiseringsschema" för ett liknande koncept i den tjänsten.

Enhets etablerings tjänsten har stöd för följande former av attestering:
* **X. 509-certifikat** som baseras på flödet för standard-x. 509-certifikatautentisering.
* **Trusted Platform Module (TPM)** baserat på en nonce-utmaning med TPM-standarden för nycklar för att presentera en signerad SAS-token (signatur för delad åtkomst). Detta kräver inte en fysisk TPM på enheten, men tjänsten förväntar sig attestering med bekräftelse nyckeln enligt [TPM-specifikationen](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Symmetrisk nyckel** baserat på SAS- [säkerhetstoken](../iot-hub/iot-hub-devguide-security.md#security-tokens)(signatur för delad åtkomst), som innehåller en hashad signatur och en inbäddad förfallo tid. Mer information finns i den [symmetriska nyckeln attestering](concepts-symmetric-key-attestation.md).

## <a name="hardware-security-module"></a>Modul för maskin varu säkerhet

Modulen för maskin varu säkerhet, eller HSM, används för säker, maskinvarubaserad lagring av enhets hemligheter och är den säkraste formen av hemlig lagring. Både X. 509-certifikat och SAS-token kan lagras i HSM. HSM: er kan användas med både mekanismen för attestering som stöds av etablerings tjänsten.

> [!TIP]
> Vi rekommenderar starkt att du använder en HSM med enheter för att säkert lagra hemligheter på dina enheter.

Enhets hemligheter kan också lagras i program vara (minne), men det är en mindre säker form av lagring än en HSM.

## <a name="registration-id"></a>Registrerings-ID

Registrerings-ID: t används för att unikt identifiera en enhet i enhets etablerings tjänsten. Registrerings-ID: t måste vara unikt i etablerings tjänstens [ID-omfång](#id-scope). Varje enhet måste ha ett registrerings-ID. Registrerings-ID: t är alfanumeriskt, gemener och får innehålla bindestreck.

* När det gäller TPM anges registrerings-ID: t av själva TPM: en.
* Om det gäller X. 509-baserad attestering anges registrerings-ID: t som certifikatets ämnes namn.

## <a name="device-id"></a>Enhets-ID

Enhets-ID: t är det ID som det visas i IoT Hub. Det önskade enhets-ID: t kan anges i registrerings posten, men det är inte obligatoriskt att ställas in. Om inget önskat enhets-ID anges i registrerings listan används registrerings-ID: t som enhets-ID när enheten registreras. Läs mer om [enhets-ID: n i IoT Hub](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>ID-omfång

ID-omfånget tilldelas en enhets etablerings tjänst när den skapas av användaren och används för att unikt identifiera den specifika etablerings tjänsten som enheten ska registrera sig. ID-omfånget genereras av tjänsten och är oföränderligt, vilket garanterar unikheten.

> [!NOTE]
> Unikhet är viktigt för långvariga distributions åtgärder och sammanslagnings-och förvärvs scenarier.
