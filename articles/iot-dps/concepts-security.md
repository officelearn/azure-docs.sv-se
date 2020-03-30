---
title: Etablerande tjänst för Azure IoT Hub-enhet – säkerhetsbegrepp
description: Beskriver koncept för säkerhetsetablering som är specifika för enheter med DPS (Device Provisioning Service) och IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3191e9886604af9b2a26b71a89cee699197585c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271569"
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Säkerhetsbegrepp för IoT Hub Device Provisioning Service 

IoT Hub Device Provisioning Service är en hjälptjänst för IoT Hub som du använder för att konfigurera zero-touch-enhetsetablering till en angiven IoT-hubb. Med enhetsetableringstjänsten kan du tillhandahålla miljontals enheter [automatiskt](concepts-auto-provisioning.md) på ett säkert och skalbart sätt. Den här artikeln innehåller en översikt över de *säkerhetsbegrepp* som ingår i enhetsetablering. Den här artikeln är relevant för alla personer som är involverade i att göra en enhet redo för distribution.

## <a name="attestation-mechanism"></a>Attesteringsmekanism

Attestation mekanismen är den metod som används för att bekräfta en enhets identitet. Attestation-mekanismen är också relevant för registreringslistan, som talar om för etableringstjänsten vilken metod för attestering som ska användas med en viss enhet.

> [!NOTE]
> IoT Hub använder "autentiseringsschema" för ett liknande koncept i den tjänsten.

Enhetsetableringstjänsten stöder följande former av intyg:
* **X.509-certifikat** baserat på standardflödet för X.509-certifikatautentisering.
* **TPM (Trusted Platform Module)** baserat på en nonce-utmaning med hjälp av TPM-standarden för nycklar för att presentera en signerad SAS-token (Shared Access Signature). Denna form av intyg kräver inte en fysisk TPM på enheten, men tjänsten förväntar sig att intyga att använda godkännande nyckeln per [TPM spec](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Symmetrisk nyckel** baserat på [SAS-säkerhetstoken](../iot-hub/iot-hub-devguide-security.md#security-tokens)(Shared Access Signature), som innehåller en hash-signatur och en inbäddad förfallodatum. Mer information finns i [Symmetrisk nyckeltysst .](concepts-symmetric-key-attestation.md)


## <a name="hardware-security-module"></a>Säkerhetsmodul för maskinvara

Maskinvarusäkerhetsmodulen, eller HSM, används för säker, maskinvarubaserad lagring av enhetshemligheter och är den säkraste formen av hemlig lagring. Både X.509-certifikat och SAS-token kan lagras i HSM. HSM kan användas med båda attestation mekanismer som etablering stöder.

> [!TIP]
> Vi rekommenderar starkt att du använder en HSM med enheter för att säkert lagra hemligheter på dina enheter.

Enhetshemligheter kan också lagras i programvara (minne), men det är en mindre säker form av lagring än en HSM.

## <a name="trusted-platform-module"></a>Trusted Platform Module

TPM kan referera till en standard för säker lagring av nycklar som används för att autentisera plattformen, eller det kan hänvisa till I / O-gränssnittet som används för att interagera med modulerna som implementerar standarden. TPM kan finnas som diskret maskinvara, integrerad maskinvara, firmware-baserad eller programvarubaserad. Läs mer om [TPM:er och TPM-intyget](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Enhetsetableringstjänsten stöder endast TPM 2.0.

TPM-attestation baseras på en nonce-utmaning, som använder stöd- och lagringsrotnycklarna för att presentera en signerad SAS-token (Shared Access Signature).

### <a name="endorsement-key"></a>Nyckel för godkännande

Stödnyckeln är en asymmetrisk nyckel som finns inuti TPM: et, som genererades internt eller injicerades vid tillverkningstid och är unik för varje TPM. Godkännandenyckeln kan inte ändras eller tas bort. Den privata delen av godkännandenyckeln släpps aldrig utanför TPM:en, medan den offentliga delen av bekräftelsenyckeln används för att känna igen en äkta TPM. Läs mer om [bekräftelsenyckeln](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Rotnyckel för lagring

Lagringsrotnyckeln lagras i TPM:en och används för att skydda TPM-nycklar som skapats av program, så att dessa nycklar inte kan användas utan TPM.The storage root key is stored in the TPM and is used to protect TPM keys created by programs, so that these keys cannot be used without the TPM. Lagringsrotnyckeln genereras när du blir ägare till TPM:en. När du rensar TPM:en så att en ny användare kan bli ägare genereras en ny lagringsrotnyckel. Läs mer om [lagringsrotnyckeln](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>X.509 certifikat

Att använda X.509-certifikat som en attestation-mekanism är ett utmärkt sätt att skala produktionen och förenkla enhetsetablering. X.509-certifikat är vanligtvis ordnade i en certifikatkedja där varje certifikat i kedjan signeras av den privata nyckeln för nästa högre certifikat, och så vidare, avslutas i ett självsignerat rotcertifikat. Det här arrangemanget upprättar en delegerad förtroendekedja från rotcertifikatet som genereras av en betrodd rotcertifikatutfärdarutfärdning (CA) ned genom varje mellanliggande certifikatutfärdaren till slutenditetens "leaf"-certifikat som är installerat på en enhet. Mer information finns i [Enhetsautentisering med X.509 CA-certifikat](/azure/iot-hub/iot-hub-x509ca-overview). 

Ofta representerar certifikatkedjan någon logisk eller fysisk hierarki som är associerad med enheter. En tillverkare kan till exempel:
- utfärda ett självsignerat rotcertifikatutfärdarcertifikat
- använda rotcertifikatet för att generera ett unikt mellanliggande certifikatutfärdarcertifikat för varje fabrik
- använda varje fabrikscertifikat för att generera ett unikt mellanliggande certifikatutfärdar för varje produktionslinje i anläggningen
- och slutligen använda produktionsraden certifikat, för att generera en unik enhet (slutentitet) certifikat för varje enhet som tillverkas på linjen. 

Mer information finns i [Begreppsmässig förståelse av X.509 CA-certifikat i IoT-branschen](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Rotcertifikat

Ett rotcertifikat är ett självsignerat X.509-certifikat som representerar en certifikatutfärdar.A root certificate is a self-signed X.509 certificate representing a certificate authority (CA). Det är ändstationen, eller förtroendeankaret, för certifikatkedjan. Rotcertifikat kan utfärdas själv av en organisation eller köpas från en rotcertifikatutfärdare. Mer information finns i [Hämta X.509 CA-certifikat](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). Rotcertifikatet kan också kallas ett rotcertifikatutfärdarcertifikat.

### <a name="intermediate-certificate"></a>Mellanliggande certifikat

Ett mellanliggande certifikat är ett X.509-certifikat som har signerats av rotcertifikatet (eller av ett annat mellanliggande certifikat med rotcertifikatet i kedjan). Det sista mellanliggande certifikatet i en kedja används för att signera lövcertifikatet. Ett mellanliggande certifikat kan också kallas ett mellanliggande CA-certifikat.

### <a name="end-entity-leaf-certificate"></a>"bladcertifikat" för slutentitet

Lövcertifikatet, eller slutenhetscertifikatet, identifierar certifikatinnehavaren. Den har rotcertifikatet i sin certifikatkedja samt noll eller fler mellanliggande certifikat. Lövcertifikatet används inte för att signera andra certifikat. Den identifierar enheten unikt för etableringstjänsten och kallas ibland enhetscertifikatet. Under autentiseringen använder enheten den privata nyckeln som är associerad med det här certifikatet för att svara på ett bevis på ägoutmaning från tjänsten.

Lövcertifikat som används med en [enskild registreringspost](./concepts-service.md#individual-enrollment) har ett krav på att **ämnesnamnet** måste anges till registrerings-ID för den enskilda registreringsposten. Lövcertifikat som används med en [registreringsgrupppost](./concepts-service.md#enrollment-group) bör ha **ämnesnamnet** inställt på önskat enhets-ID som visas i **registreringsposterna** för den autentiserade enheten i registreringsgruppen.

Mer information finns i [Autentisera enheter som är signerade med X.509 CA-certifikat](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Styra enhetens åtkomst till etableringstjänsten med X.509-certifikat

Etableringstjänsten exponerar två typer av registreringspost som du kan använda för att styra åtkomsten för enheter som använder X.509-attestationsmekanismen:  

- [Enskilda registreringsposter](./concepts-service.md#individual-enrollment) konfigureras med enhetscertifikatet som är associerat med en viss enhet. Dessa poster styr registreringar för specifika enheter.
- [Registreringsgruppposter](./concepts-service.md#enrollment-group) associeras med ett specifikt mellanliggande certifikat eller rotcertifikatutfärdarcertifikat. Dessa poster styr registreringar för alla enheter som har det mellanliggande certifikatet eller rotcertifikatet i sin certifikatkedja. 

När en enhet ansluter till etableringstjänsten prioriterar tjänsten mer specifika registreringsposter framför mindre specifika registreringsposter. Det vill än om det finns en enskild registrering för enheten tillämpar etableringstjänsten den posten. Om det inte finns någon enskild registrering för enheten och en registreringsgrupp för det första mellanliggande certifikatet i enhetens certifikatkedja finns, gäller tjänsten den posten och så vidare uppåt i kedjan till roten. Tjänsten tillämpar den första tillämpliga post som den finner, så att

- Om den första registreringsposten som hittades är aktiverad, etablerar tjänsten enheten.
- Om den första registreringsposten som hittades är inaktiverad etablerar inte tjänsten.  
- Om ingen registreringspost hittas för något av certifikaten i enhetens certifikatkedja etablerar tjänsten inte enheten. 

Den här mekanismen och den hierarkiska strukturen för certifikatkedjor ger kraftfull flexibilitet i hur du kan styra åtkomsten för enskilda enheter samt för grupper av enheter. Tänk dig till exempel fem enheter med följande certifikatkedjor: 

- *Enhet 1:* rotcertifikat -> certifikat A -> enhet 1-certifikat
- *Enhet 2:* rotcertifikat -> certifikat A -> enhet 2-certifikat
- *Enhet 3:* rotcertifikat -> certifikat A -> enhet 3-certifikat
- *Enhet 4:* rotcertifikat -> certifikat B -> enhet 4-certifikat
- *Enhet 5:* rotcertifikat -> certifikat B -> enhet 5 certifikat

Inledningsvis kan du skapa en enda aktiverad gruppregistreringspost för rotcertifikatet för att aktivera åtkomst för alla fem enheterna. Om certifikat B senare äventyras kan du skapa en inaktiverad registreringsgrupppost för certifikat B för att förhindra att *enhet 4* och *enhet 5* registreras. Om ännu senare *Enhet 3* äventyras kan du skapa en inaktiverad enskild registreringspost för dess certifikat. Detta återkallar åtkomsten för *Enhet 3*, men tillåter ändå *enhet 1* och *enhet 2* att registrera.