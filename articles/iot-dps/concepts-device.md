---
title: Enhetskoncept i Azure enhetsetablering | Microsoft Docs
description: Beskriver koncept som är specifika för enheter med Device Provisioning-tjänsten och IoT Hub för enhetsetablering
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 354ef48f7935536864cde9dc0d9a130fa5aeb865
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972869"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>IoT Hub Device Provisioning Service enhetskoncept

IoT Hub Device Provisioning Service är en hjälptjänst för IoT-hubb som används för att konfigurera zero touch-enhetsetablering till en angiven IoT-hubb. Med enhetsetableringstjänsten kan du etablera miljontals enheter på ett säkert och skalbart sätt.

Den här artikeln ger en översikt över den *enhet* begrepp som ingår i enhetsetablering. Den här artikeln är mest relevant för personer som ingår i den [tillverkning steg](about-iot-dps.md#manufacturing-step) för att få en enhet som är klar för distribution.

## <a name="attestation-mechanism"></a>Attesteringsmetod

Attesteringsmetod är den metod som används för att bekräfta en enhetens identitet. Attesteringsmetod är också relevant för registreringslistan, som anger vilken metod för attestering ska användas med en given enhet för etableringstjänsten.

> [!NOTE]
> IoT Hub använder ”autentiseringsschema” för ett liknande koncept i tjänsten.

Device Provisioning-tjänsten stöder följande typer av attestering:
* **X.509-certifikat** baserat på standard autentiseringsflödet för X.509-certifikat.
* **Trusted Platform Module (TPM)** baserat på ett tillfälligt utmaningen med TPM-standarden för nycklar för att presentera en signerade token för signatur för delad åtkomst (SAS). Detta kräver inte en fysisk TPM på enheten, men tjänsten förväntar sig att bekräfta att använda bekräftelsenyckeln per den [TPM-specifikationen](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Symmetrisk nyckel** baserat på signatur för delad åtkomst (SAS) [säkerhetstoken](../iot-hub/iot-hub-devguide-security.md#security-tokens), som innehåller en hashade signatur och en inbäddad upphör att gälla. Mer information finns i [symmetriska nyckelattestering](concepts-symmetric-key-attestation.md).

## <a name="hardware-security-module"></a>Modul för maskinvarusäkerhet

Hardware security module eller HSM, används för säker, maskinvarubaserad lagring av enheten hemligheter och är den mest säkra formen av hemliga lagring. Både X.509-certifikat och SAS-token kan lagras i HSM. HSM: er kan användas med både attesteringsmetoder etablering tjänsten stöder.

> [!TIP]
> Vi rekommenderar starkt att använda en HSM med enheter för att lagra hemligheter säkert på dina enheter.

Enheten hemligheter kan också lagras i programvara (minne), men det är ett mindre säkert formulär lagring än en HSM.

## <a name="registration-id"></a>Registrerings-ID

Registrerings-ID används för att unikt identifiera en enhet i Device Provisioning-tjänsten. Enhets-ID måste vara unikt i etableringstjänsten [ID-omfång](#id-scope). Varje enhet måste ha ett registrerings-ID. Registrerings-ID är alfanumeriskt, gemener och får innehålla bindestreck.

* När det gäller TPM, kommer registrerings-ID från själv TPM.
* När det gäller X.509-baserade Attesteringen tillhandahålls registrerings-ID som ämnesnamnet för certifikatet.

## <a name="device-id"></a>Enhets-ID

Enhets-ID är ID som det visas i IoT Hub. Önskat enhets-ID kan anges i registreringsposten, men du behöver inte anges. Om inga önskade enhets-ID har angetts i registreringslistan används registrerings-ID som enhets-ID när du registrerar enheten. Läs mer om [enhets-ID i IoT Hub](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>ID-omfång

ID-omfång har tilldelats en Device Provisioning-tjänsten när den har skapats av användaren och används för att unikt identifiera specifika enheten registreras via för etableringstjänsten. ID-omfång genereras av tjänsten och kan inte ändras, vilket garanterar unikhet.

> [!NOTE]
> Unikhet är viktigt för tidskrävande distributionsåtgärder och fusioner och förvärv scenarier.
