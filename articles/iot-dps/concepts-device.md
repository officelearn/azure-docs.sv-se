---
title: Enheten koncept i Azure enhetsetableringen | Microsoft Docs
description: Beskriver begrepp som är specifika för enheter med etablering av tjänst- och IoT-hubb för etablering av enheter
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: bd77a56acee948995bb2fcbb5beea60f69cda9ee
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630161"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>IoT-hubb enheten Etableringstjänsten enheten begrepp

IoT-hubb Device etablering Service är en helper-tjänsten för IoT-hubb som används för att konfigurera zero touch enhet etablering till en angiven IoT-hubb. Med enhetsetableringstjänsten kan du etablera miljontals enheter på ett säkert och skalbart sätt.

Den här artikeln ger en översikt över de *enhet* begrepp som är involverad i enhetsetableringen. Den här artikeln är mest relevant för personer som ingår i den [tillverkning steg](about-iot-dps.md#manufacturing-step) för att få en enhet som är klar för distribution.

## <a name="attestation-mechanism"></a>Mekanism för hälsoattestering

Mekanism för attestering är den metod som används för att bekräfta identiteten för en enhet. Mekanismen för attestering är också relevant för listan registrering som talar om tjänsten etablering vilken metod för att använda med en viss enhet.

> [!NOTE]
> IoT-hubb använder ”autentiseringsschema” för ett liknande koncept i tjänsten.

Etablering av tjänst stöder två typer av intyg:
* **X.509-certifikat** baserat på standard autentiseringsflödet för X.509-certifikat.
* **Trusted Platform Module (TPM)** baserat på ett temporärt ID utmaningen med TPM-standard nycklarna för att presentera en signerad delade signatur åtkomst (SAS)-token. Detta kräver inte en fysisk TPM på enheten, men tjänsten förväntar att bekräfta att använda bekräftelsenyckeln per den [TPM-specifikationen](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).

## <a name="hardware-security-module"></a>Maskinvarusäkerhetsmodul

Maskinvarusäkerhetsmodul eller HSM, används för säker, maskinvarubaserad lagring av enheten hemligheter och är den mest säkra formen av hemliga lagring. Både X.509-certifikat och SAS-token kan lagras i HSM. HSM kan användas med både attestering mekanismer etablering tjänsten stöder.

> [!TIP]
> Vi rekommenderar starkt att använda en HSM med enheter ska lagras på ett säkert sätt hemligheter på dina enheter.

Enheten hemligheter kan också lagras i programvaran (minne), men det är en mindre säker form av lagring än en HSM.

## <a name="registration-id"></a>Registrerings-ID

Registrerings-ID används för att unikt identifiera en enhet i enheten Etableringstjänsten. Enhets-ID måste vara unikt i tjänsten etablering [ID scope](#id-scope). Varje enhet måste ha en registrering-ID. Registrerings-ID är alfanumeriska, gemener och får innehålla bindestreck.

* När det gäller TPM som registrerings-ID själva TPM-Modulen.
* När det gäller X.509-baserade attestering har registrerings-ID angetts som ämnesnamnet för certifikatet.

## <a name="device-id"></a>Enhets-ID

Enhets-ID är ID som det visas i IoT-hubb. Önskad enhets-ID kan anges i posten registrering, men behöver inte anges. Om inga önskade enhets-ID har angetts i listan över registrering används registrerings-ID som enhets-ID när du registrerar enheten. Lär dig mer om [enhets-ID i IoT-hubb](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>ID-scope

ID-scope har tilldelats en tjänst för etablering när den har skapats av användaren och används för att identifiera tjänsten specifika etablering enheten registreras via. Området ID genereras av tjänsten och kan inte ändras, vilket garanterar att unika.

> [!NOTE]
> Unikhet är viktigt för fusion och förvärv scenarier och tidskrävande distributionsåtgärder.
