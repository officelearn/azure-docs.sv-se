---
title: "Säkerhetsbegrepp i Azure IoT Hub-enhet etablering Service | Microsoft Docs"
description: "Beskriver säkerhet etablering begrepp som är specifika för enheter med etablering av tjänst- och IoT-hubb"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 3ccbaaf55d2bdfedffcdb5ca069798328e2d75fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>IoT-hubb enheten Etableringstjänsten säkerhetsbegrepp 

IoT-hubb Device etablering Service är en helper-tjänsten för IoT-hubb som används för att konfigurera zero touch enhet etablering till en angiven IoT-hubb. Du kan etablera miljontals enheter på en säker och skalbar sätt med enheten Etableringstjänsten. Den här artikeln ger en översikt över de *säkerhet* begrepp som är involverad i enhetsetableringen. Den här artikeln är relevant för alla personer som är inblandade i att förbereda en enhet för distribution.

## <a name="attestation-mechanism"></a>Mekanism för hälsoattestering

Mekanism för attestering är den metod som används för att bekräfta identiteten för en enhet. Mekanismen för attestering är också relevant för listan registrering som talar om tjänsten etablering vilken metod för att använda med en viss enhet.

> [!NOTE]
> IoT-hubb använder ”autentiseringsschema” för ett liknande koncept i tjänsten.

Etablering av tjänst stöder två typer av intyg:
* **X.509-certifikat** baserat på standard autentiseringsflödet för X.509-certifikat.
* **SAS-token** baserat på ett tillfälligt anrop med TPM-standarden för nycklar. Detta kräver inte en fysisk TPM på enheten, men tjänsten förväntar att bekräfta att använda bekräftelsenyckeln per den [TPM-specifikationen](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).

## <a name="hardware-security-module"></a>Maskinvarusäkerhetsmodul

Maskinvarusäkerhetsmodul eller HSM, används för säker, maskinvarubaserad lagring av enheten hemligheter och är den mest säkra formen av hemliga lagring. Både X.509-certifikat och SAS-token kan lagras i HSM. HSM kan användas med både attestering mekanismer etablering stöder.

> [!TIP]
> Vi rekommenderar starkt att använda en HSM med enheter ska lagras på ett säkert sätt hemligheter på dina enheter.

Enheten hemligheter kan också lagras i programvaran (minne), men det är en mindre säker form av lagring än en HSM.

## <a name="trusted-platform-module-tpm"></a>Trusted Platform Module (TPM)

TPM kan referera till en standard för säker lagring av nycklar som används för att autentisera plattformen eller den kan referera till i/o-gränssnitt som används för att interagera med moduler som implementerar standarden. TPM: er kan finnas som diskreta maskinvara, inbyggd maskinvara, inbyggd programvara eller programvara. Lär dig mer om [TPM: er och TPM-nyckelattestering](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Etablering av tjänst stöder endast TPM 2.0.

## <a name="endorsement-key"></a>Bekräftelsenyckel

Bekräftelsenyckeln är en asymmetrisk nyckel finns inuti TPM som matades vid tillverkning tid och är unikt för varje TPM. Bekräftelsenyckeln kan inte ändras eller tas bort. Den privata delen av bekräftelsenyckeln är inte tillgänglig utanför TPM, medan den offentliga delen av bekräftelsenyckeln används för att identifiera en äkta TPM. Lär dig mer om den [bekräftelsenyckel](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

## <a name="storage-root-key"></a>Lagringsrotnyckel

Lagringsrotnyckel lagras i TPM och används för att skydda TPM-nycklar som skapas av program, så att dessa nycklar inte kan användas utan TPM. Lagringsrotnyckel genereras när du blir ägare till TPM; När du avmarkerar TPM så att en ny användare kan bli ägare, skapas en ny lagringsrotnyckel. Lär dig mer om den [lagringsrotnyckel](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="root-certificate"></a>Rotcertifikat

Ett rotcertifikat är en typ av X.509-certifikat som representerar en certifikatutfärdare och är självsignerat. Det är terminus av certifikatkedjan.

## <a name="intermediate-certificate"></a>Mellanliggande certifikat

En mellanliggande certifikat är ett X.509-certifikat som har signerats av rotcertifikatet (eller av ett annat certifikat med rotcertifikat i kedjan) och som används för att signera certifikatet lövmedlemmar.

## <a name="leaf-certificate"></a>Lövcertifikatet

En lövcertifikatet eller certifikat för slutenhet används för att identifiera certifikatinnehavarens och rotcertifikatet har i sin certifikatkedja. Lägsta certifikatet används inte för att signera andra certifikat.
