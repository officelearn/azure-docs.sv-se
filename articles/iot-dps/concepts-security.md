---
title: Säkerhetsbegrepp i Azure IoT Hub Device Provisioning-tjänsten | Microsoft Docs
description: Beskriver security etablering begrepp som är specifika för enheter med Device Provisioning-tjänsten och IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: e35330874c647eba2cddde694563c8a1d9e83df5
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490280"
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Säkerhetsbegrepp för IoT Hub Device Provisioning-tjänsten 

IoT Hub Device Provisioning Service är en hjälptjänst för IoT-hubb som används för att konfigurera zero touch-enhetsetablering till en angiven IoT-hubb. Med Device Provisioning-tjänsten, kan du [automatiskt etablera](concepts-auto-provisioning.md) miljontals enheter på ett säkert och skalbart sätt. Den här artikeln ger en översikt över den *security* begrepp som ingår i enhetsetablering. Den här artikeln är relevant för alla personer som ingår i förbereder en enhet för distribution.

## <a name="attestation-mechanism"></a>Attesteringsmetod

Attesteringsmetod är den metod som används för att bekräfta en enhetens identitet. Attesteringsmetod är också relevant för registreringslistan, som anger vilken metod för attestering ska användas med en given enhet för etableringstjänsten.

> [!NOTE]
> IoT Hub använder ”autentiseringsschema” för ett liknande koncept i tjänsten.

Device Provisioning-tjänsten stöder följande typer av attestering:
* **X.509-certifikat** baserat på standard autentiseringsflödet för X.509-certifikat.
* **Trusted Platform Module (TPM)** baserat på ett tillfälligt utmaningen med TPM-standarden för nycklar för att presentera en signerade token för signatur för delad åtkomst (SAS). Den här typen av attestering kräver inte en fysisk TPM på enheten, men tjänsten förväntar sig att bekräfta att använda bekräftelsenyckeln per den [TPM-specifikationen](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Symmetrisk nyckel** baserat på signatur för delad åtkomst (SAS) [säkerhetstoken](../iot-hub/iot-hub-devguide-security.md#security-tokens), som innehåller en hashade signatur och en inbäddad upphör att gälla. Mer information finns i [symmetriska nyckelattestering](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Modul för maskinvarusäkerhet

Hardware security module eller HSM, används för säker, maskinvarubaserad lagring av enheten hemligheter och är den mest säkra formen av hemliga lagring. Både X.509-certifikat och SAS-token kan lagras i HSM. HSM: er kan användas med både attesteringsmetoder etablering stöder.

> [!TIP]
> Vi rekommenderar starkt att använda en HSM med enheter för att lagra hemligheter säkert på dina enheter.

Enheten hemligheter kan också lagras i programvara (minne), men det är ett mindre säkert formulär lagring än en HSM.

## <a name="trusted-platform-module"></a>Trusted Platform Module

TPM kan referera till en standard för att på ett säkert sätt lagra nycklar som används för att autentisera plattformen eller den kan referera till i/o-gränssnitt som används för att interagera med moduler som implementerar standarden. TPM: er kan finnas som diskreta maskinvara, integrerad maskinvara, inbyggd programvara eller programvara. Läs mer om [TPM: er och TPM-attestering](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Device Provisioning-tjänsten stöder endast TPM 2.0.

TPM-attestering baseras på ett tillfälligt utmaning som använder rotnycklar bekräftelsenyckeln och lagring för att presentera en signerade token för signatur för delad åtkomst (SAS).

### <a name="endorsement-key"></a>Bekräftelsenyckel

Bekräftelsenyckeln är en asymmetrisk nyckel som finns i TPM, som internt har genererats eller införs i manufacturing tid och är unikt för varje TPM. Bekräftelsenyckeln kan inte ändras eller tas bort. Den privata delen av bekräftelsenyckeln är inte tillgänglig utanför TPM, medan den offentliga delen av bekräftelsenyckeln används för att identifiera en äkta TPM. Läs mer om den [bekräftelsenyckeln](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Lagringsrotnyckel

Lagringsrotnyckel lagras i TPM och används för att skydda TPM-nycklar som skapas av program, så att dessa nycklar inte kan användas utan TPM. Lagringsrotnyckel genereras när du blir ägare till TPM; När du avmarkerar TPM så att en ny användare kan bli ägare, skapas en ny rotnyckel för lagring. Läs mer om den [lagringsrotnyckel](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>X.509-certifikat

Med X.509-certifikat som en attesteringsmetod är ett utmärkt sätt att skala produktion och förenkla enhetsetablering. X.509-certifikat som är normalt ordnade i en certifikatkedja med förtroenden där varje certifikat i kedjan är signerat av den privata nyckeln för nästa högre certifikatet och så vidare, avslutar i ett självsignerat rotcertifikat. Den här ordningen upprättar en delegerad certifikatkedja från rotcertifikatet som genererats av en betrodd rotcertifikatutfärdare (CA) ned via varje mellanliggande Certifikatutfärdare till slutanvändare ”löv” certifikatet på enheten. Mer information finns i [autentisering med X.509 CA-certifikat](/azure/iot-hub/iot-hub-x509ca-overview). 

Certifikatkedjan representerar ofta vissa logisk eller fysisk hierarki har associerats med enheter. Exempelvis kan en tillverkare:
- utfärda ett självsignerat rot-CA-certifikat
- använda rotcertifikatet för att generera ett unikt mellanliggande CA-certifikat för varje factory
- använda certifikat för varje factory för att generera ett unikt mellanliggande CA-certifikat för varje produktionslinje i anläggningen
- och slutligen använder produktionslinje-certifikatet för att generera ett unikt enhets (slutenheter)-certifikat för varje enhet som tillverkats på raden. 

Mer information finns i [förstår X.509 CA-certifikat i IoT-branschen](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Rotcertifikat

Ett rotcertifikat är ett självsignerat X.509-certifikat som representerar en certifikatutfärdare (CA). Det är terminus eller förtroendeankare för certifikatkedjan. Rotcertifikat kan själva utfärdats av en organisation eller har köpt från en rotcertifikatutfärdare. Mer information finns i [hämta X.509 CA-certifikat](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). Rotcertifikatet kan också kallas en Certifikatutfärdares rotcertifikat.

### <a name="intermediate-certificate"></a>Mellanliggande certifikat

Ett mellanliggande certifikat är ett X.509-certifikat som har signerats av rotcertifikatet (eller av en annan mellanliggande certifikat med rotcertifikat i kedjan). Senaste mellanliggande certifikat i en kedja används för att signera lövcertifikatet. Ett mellanliggande certifikat kan även kallas ett mellanliggande CA-certifikat.

### <a name="end-entity-leaf-certificate"></a>”Löv”-certifikat

Lövcertifikatet eller slutentitetscertifikat, identifierar certifikatinnehavarens. Den har rotcertifikat i dess certifikatkedjan samt noll eller flera mellanliggande certifikat. Lövcertifikatet används inte för att signera andra certifikat. Den identifierar enheten till etableringstjänsten och ibland kallas enhetscertifikat. Enheten använder den privata nyckeln som är associerade med det här certifikatet för att svara på en proof of tillgång utmaning från tjänsten under autentiseringen.

Löv certifikat som används med en [enskild registrering](./concepts-service.md#individual-enrollment) post har ett krav som den **ämnesnamn** måste vara inställt på registrerings-ID för posten för enskild registrering. Löv certifikat som används med en [registreringsgruppen](./concepts-service.md#enrollment-group) post ska ha den **ämnesnamn** inställd på önskat enhets-ID som visas i den **Registreringsposter** för den autentiserade enheten i registreringsgruppen.

Mer information finns i [autentisera enheter signeras med X.509 CA-certifikat](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Kontrollera Enhetsåtkomst till etableringstjänsten med X.509-certifikat

Etableringstjänsten visar två typer av registreringspost som du kan använda för att kontrollera åtkomsten för enheter som använder mekanismen för X.509-attestering:  

- [Enskild registrering](./concepts-service.md#individual-enrollment) poster har konfigurerats med det certifikat som är associerade med en specifik enhet. De här posterna styra registreringar för specifika enheter.
- [Grupp för registrering](./concepts-service.md#enrollment-group) poster som är associerade med en specifik mellanliggande eller certifikatet för rotcertifikatutfärdaren. De här posterna styra registreringar för alla enheter som har som mellanliggande eller rot certifikat i certifikatkedjan. 

När en enhet ansluter till etableringstjänsten, prioriterar tjänsten mer specifika registrering poster över mindre specifika poster för registrering. Om en enskild registrering för enheten finns, det vill säga gäller posten etableringstjänsten. Om det finns ingen enskild registrering för enheten och en registreringsgrupp för det första mellanliggande certifikatet i certifikatkedjan för enhetens finns, gäller den posten och så vidare upp i kedjan till roten i tjänsten. Tjänsten gäller den första tillämpliga posten som hittas, så att:

- Om den första registreringsposten som hittades är aktiverat etablerar enheten i tjänsten.
- Om den första registreringsposten hitta inaktiveras, kommer tjänsten inte att etablera enheten.  
- Om det finns ingen post för registrering för någon av certifikat i certifikatkedjan för enhetens tjänsten inte att etablera enheten. 

Den här mekanismen och certifikatkedjor hierarkiska struktur ger kraftfulla flexibilitet i hur du kan kontrollera åtkomsten för enskilda enheter samt för grupper av enheter. Anta att fem enheter med följande certifikatkedjor: 

- *Enhet 1*: rotcertifikat -> certifikatet A -> 1 certifikat
- *Enhet 2*: rotcertifikat -> certifikatet A -> enhetscertifikat 2
- *Enheten 3*: rotcertifikat -> certifikatet A -> 3 certifikat
- *Enhet 4*: rotcertifikat -> certifikatet B -> 4 certifikat
- *Enheten 5*: rotcertifikat -> certifikatet B -> 5 certifikat

Första gången kan du skapa en post för registrering av aktiverade grupp för rotcertifikatet som du vill aktivera åtkomst för alla fem enheter. Om certifikat B senare har komprometterats, kan du skapa en post för grupp av inaktiverad registrering för certifikat B för att förhindra *enhet 4* och *enhet 5* från att registreras. Om det fortfarande senare *enheten 3* blir äventyras, kan du skapa en inaktiverad individuell registreringspost för dess certifikat. Det här återkallar åtkomst för *enheten 3*, men kan fortfarande *enhet 1* och *enhet 2* att registrera.