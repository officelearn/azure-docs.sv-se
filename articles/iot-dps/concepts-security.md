---
title: Säkerhetsbegrepp i Azure IoT Hub-enhet etablering Service | Microsoft Docs
description: Beskriver säkerhet etablering begrepp som är specifika för enheter med etablering av tjänst- och IoT-hubb
services: iot-dps
keywords: ''
author: nberdy
ms.author: nberdy
ms.date: 03/30/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: f6410aa3ab21e7c50ec6918930f31b9e1455c464
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>IoT-hubb enheten Etableringstjänsten säkerhetsbegrepp 

IoT-hubb Device etablering Service är en helper-tjänsten för IoT-hubb som används för att konfigurera zero touch enhet etablering till en angiven IoT-hubb. Med tjänsten etablering enhet kan du [automatiskt etablera](concepts-auto-provisioning.md) miljoner enheter på en säker och skalbar sätt. Den här artikeln ger en översikt över de *säkerhet* begrepp som är involverad i enhetsetableringen. Den här artikeln är relevant för alla personer som är inblandade i att förbereda en enhet för distribution.

## <a name="attestation-mechanism"></a>Mekanism för hälsoattestering

Mekanism för attestering är den metod som används för att bekräfta identiteten för en enhet. Mekanismen för attestering är också relevant för listan registrering som talar om tjänsten etablering vilken metod för att använda med en viss enhet.

> [!NOTE]
> IoT-hubb använder ”autentiseringsschema” för ett liknande koncept i tjänsten.

Etablering av tjänst stöder två typer av intyg:
* **X.509-certifikat** baserat på standard autentiseringsflödet för X.509-certifikat.
* **Trusted Platform Module (TPM)** baserat på ett temporärt ID utmaningen med TPM-standard nycklarna för att presentera en signerad delade signatur åtkomst (SAS)-token. Detta kräver inte en fysisk TPM på enheten, men tjänsten förväntar att bekräfta att använda bekräftelsenyckeln per den [TPM-specifikationen](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).

## <a name="hardware-security-module"></a>Maskinvarusäkerhetsmodul

Maskinvarusäkerhetsmodul eller HSM, används för säker, maskinvarubaserad lagring av enheten hemligheter och är den mest säkra formen av hemliga lagring. Både X.509-certifikat och SAS-token kan lagras i HSM. HSM kan användas med både attestering mekanismer etablering stöder.

> [!TIP]
> Vi rekommenderar starkt att använda en HSM med enheter ska lagras på ett säkert sätt hemligheter på dina enheter.

Enheten hemligheter kan också lagras i programvaran (minne), men det är en mindre säker form av lagring än en HSM.

## <a name="trusted-platform-module"></a>Trusted Platform Module

TPM kan referera till en standard för säker lagring av nycklar som används för att autentisera plattformen eller den kan referera till i/o-gränssnitt som används för att interagera med moduler som implementerar standarden. TPM: er kan finnas som diskreta maskinvara, inbyggd maskinvara, inbyggd programvara eller programvara. Lär dig mer om [TPM: er och TPM-nyckelattestering](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Etablering av tjänst stöder endast TPM 2.0.

TPM-nyckelattestering baseras på en temporärt ID utmaning som använder rotnycklar attestering och lagring för att presentera en signerad delade signatur åtkomst (SAS)-token.

### <a name="endorsement-key"></a>Bekräftelsenyckel

Bekräftelsenyckeln är en asymmetrisk nyckel finns inuti TPM, som internt genererats eller matas in vid tillverkning tid och är unikt för varje TPM. Bekräftelsenyckeln kan inte ändras eller tas bort. Den privata delen av bekräftelsenyckeln är inte tillgänglig utanför TPM, medan den offentliga delen av bekräftelsenyckeln används för att identifiera en äkta TPM. Lär dig mer om den [bekräftelsenyckel](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Lagringsrotnyckel

Lagringsrotnyckel lagras i TPM och används för att skydda TPM-nycklar som skapas av program, så att dessa nycklar inte kan användas utan TPM. Lagringsrotnyckel genereras när du blir ägare till TPM; När du avmarkerar TPM så att en ny användare kan bli ägare, skapas en ny lagringsrotnyckel. Lär dig mer om den [lagringsrotnyckel](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>X.509-certifikat

Med X.509-certifikat som en mekanism för attestering är en utmärkt sätt att skala produktion och förenkla enhetsetableringen. X.509-certifikat är vanligtvis ordnade i en certifikatkedja med förtroenden där varje certifikat i kedjan är signerat av den privata nyckeln för nästa högre certifikat och så vidare, avslutar i ett självsignerat rotcertifikat. Detta skapar en delegerad förtroendekedja för från rotcertifikatet som genererats av en betrodd rotcertifikatutfärdare (CA) ned via varje mellanliggande Certifikatutfärdare till slutanvändare ”lägsta” certifikatet på enheten. Läs mer i [autentisering med X.509-certifikat](/azure/iot-hub/iot-hub-x509ca-overview). 

Certifikatkedjan representerar ofta vissa logisk eller fysisk hierarki har associerats med enheter. Exempelvis kan en tillverkare:
- utfärda ett självsignerat rot-CA-certifikat
- Använd rotcertifikatet för att generera en unik mellanliggande CA-certifikat för varje factory
- Använd certifikat för varje factory för att generera en unik mellanliggande CA-certifikat för varje rad i produktion i anläggningen
- och slutligen använder produktionen-certifikat för att generera ett unikt enhets (slutenhet)-certifikat för varje enhet som tillverkats på raden. 

Läs mer i [grundläggande förståelse för X.509-certifikat i branschen IoT](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Rotcertifikat

Ett rotcertifikat är ett självsignerat X.509-certifikat som representerar en certifikatutfärdare (CA). Det är terminus eller förtroendeankare av certifikatkedjan. Rotcertifikat kan själva är utfärdat av en organisation eller köpts från en rotcertifikatutfärdare. Läs mer i [hämta X.509-certifikat](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). Rotcertifikatet kan också kallas en Certifikatutfärdares rotcertifikat.

### <a name="intermediate-certificate"></a>Mellanliggande certifikat

En mellanliggande certifikat är ett X.509-certifikat som har signerats av rotcertifikatet (eller av en annan mellanliggande certifikat med rotcertifikat i kedjan). Senaste mellanliggande certifikat i en kedja används för att signera certifikatet lövmedlemmar. Ett mellanliggande certifikat kan också kallas en mellanliggande certifikatutfärdare.

### <a name="end-entity-leaf-certificate"></a>Slutanvändare ”” lövcertifikatet

Lövcertifikatet eller slutentitetscertifikat, identifierar certifikatinnehavarens. Den har rotcertifikat i dess certifikatkedja samt noll eller flera mellanliggande certifikat. Lägsta certifikatet används inte för att signera andra certifikat. Unikt identifierar enheten till tjänsten etablering och ibland kallas enhetens certifikat. Under autentiseringen använder enheten den privata nyckeln som associeras med det här certifikatet ska svara på ett bevis på tillgång challenge från tjänsten. Läs mer i [autentisera enheter signerad med X.509-certifikat](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Kontrollera Enhetsåtkomst till tjänsten etablering med X.509-certifikat

Tjänsten etablering visar två typer av registrering posten som du kan använda för att kontrollera åtkomsten för enheter som använder mekanismen för attestering X.509:  

- [Registrering av enskilda](./concepts-service.md#individual-enrollment) poster har konfigurerats med enhetens certifikat som är associerade med en specifik enhet. Dessa poster styr registreringar för specifika enheter.
- [Registrering grupp](./concepts-service.md#enrollment-group) poster som är associerade med en specifik mellanliggande eller certifikatet för rotcertifikatutfärdaren. Dessa poster styr registreringar för alla enheter som har som mellanliggande eller rot certifikat i sina certifikatkedjan. 

När en enhet som ansluter till tjänsten etablering, prioriterar tjänsten mer specifika registrering transaktioner över mindre specifikt registrering poster. Om det inte finns en enskild registrering för enhetens gäller etablering tjänsten som är posten. Om det inte finns några enskilda registrering för enhetens och en registrering för det första mellanliggande certifikatet i certifikatkedjan för enhetens finns, gäller tjänsten att posten och så vidare upp i kedjan till roten. Tjänsten gäller den första tillämpliga posten som hittas, så att:

- Om den första registrering posten som hittades är aktiverad, Etablerar tjänsten enheten.
- Om den första registrering posten som hittades är inaktiverad kommer tjänsten inte att etablera enheten.  
- Om det finns ingen post för registrering för alla certifikat i certifikatkedjan för enhetens tjänsten inte att etablera enheten. 

Den här mekanismen och certifikatkedjor hierarkisk struktur innehåller kraftfulla flexibilitet i hur du kan kontrollera åtkomsten för enskilda enheter samt för grupper av enheter. Anta att fem enheter med följande certifikatkedjor: 

- *Enhet 1*: rotcertifikat -> certifikatet A -> certifikat enhet 1
- *Enhet 2*: rotcertifikat -> certifikatet A -> certifikat enhet 2
- *Enheten 3*: rotcertifikat -> certifikatet A -> enhetscertifikatet 3
- *Enhet 4*: rotcertifikat -> certifikatet B -> certifikat enhet 4
- *Enhet 5*: rotcertifikat -> certifikatet B -> certifikat enhet 5

Från början, kan du skapa en post med aktiverad grupp registrering för rotcertifikat att aktivera åtkomst för alla fem enheter. Om certifikat B senare har komprometterats, kan du skapa en grupp post i inaktiverad registrering för certifikat B för att förhindra *enhet 4* och *enhet 5* registrera. Om det fortfarande senare *enheten 3* blir komprometteras, kan du skapa en inaktiverad enskilda registrering post för dess certifikat. Detta återkallar åtkomst för *enheten 3*, men kan fortfarande *enhet 1* och *enhet 2* att registrera.