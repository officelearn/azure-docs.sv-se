---
title: Azure IoT Hub Device Provisioning Service-X. 509 certifikat attestering
description: Beskriver begrepp som är speciella för att använda 509 certifikat attestering med enhets etablerings tjänsten (DPS) och IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 911f819343f675ebe0a2604d912e6e26aa646eb5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90533092"
---
# <a name="x509-certificate-attestation"></a>X.509-certifikatattestering

Den här artikeln ger en översikt över de begrepp som ingår vid etablering av enheter med hjälp av 509-certifikat för certifikat. Den här artikeln är relevant för alla personer som ingår i att få en enhet redo för distribution.

X. 509-certifikat kan lagras i en maskinvaru-säkerhetsmodul HSM.

> [!TIP]
> Vi rekommenderar starkt att du använder en HSM med enheter för att lagra hemligheter på ett säkert sätt, t. ex. X. 509-certifikatet på enheterna i produktion.


## <a name="x509-certificates"></a>X. 509-certifikat

Att använda X. 509-certifikat som en mekanism för attestering är ett utmärkt sätt att skala produktion och förenkla enhets etablering. X. 509-certifikat ordnas vanligt vis i en certifikat kedja med förtroende där varje certifikat i kedjan signeras av den privata nyckeln för nästa högre certifikat, och så vidare, och sedan avslutas i ett självsignerat rot certifikat. Den här överenskommelsen upprättar en delegerad förtroende kedja från rot certifikatet som genererats av en betrodd rot certifikat utfärdare (CA) via varje mellanliggande certifikat utfärdare till certifikatet "löv" som har installerats på en enhet. Mer information finns i [enhets autentisering med X. 509 CA-certifikat](/azure/iot-hub/iot-hub-x509ca-overview). 

Certifikat kedjan representerar ofta vissa logiska eller fysiska hierarkier som är kopplade till enheter. En tillverkare kan till exempel:
- utfärda ett självsignerat certifikat för rot certifikat utfärdare
- Använd rot certifikatet för att generera ett unikt mellanliggande CA-certifikat för varje fabrik
- Använd varje fabriks certifikat för att generera ett unikt mellanliggande CA-certifikat för varje produktions linje i anläggningen
- och Använd slutligen produktions rads certifikatet för att generera ett unikt enhets certifikat (slutenhet) för varje enhet som tillverkas på linjen. 

Mer information finns i [konceptuell förståelse av X. 509 CA-certifikat i IoT-branschen](/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Rot certifikat

Ett rot certifikat är ett självsignerat X. 509-certifikat som representerar en certifikat utfärdare (CA). Det är upphör, eller förtroende ankare, för certifikat kedjan. Rot certifikat kan vara självutfärdade av en organisation eller köpas från en rot certifikat utfärdare. Läs mer i [Hämta X. 509 CA-certifikat](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). Rot certifikatet kan också kallas för ett rot certifikat för certifikat UTFÄRDAre.

### <a name="intermediate-certificate"></a>Mellanliggande certifikat

Ett mellanliggande certifikat är ett X. 509-certifikat som har signerats av rot certifikatet (eller ett annat mellanliggande certifikat med rot certifikatet i kedjan). Det sista mellanliggande certifikatet i en kedja används för att signera löv certifikatet. Ett mellanliggande certifikat kan också kallas ett mellanliggande CA-certifikat.

### <a name="end-entity-leaf-certificate"></a>"Löv"-certifikat för slutanvändare

Löv certifikatet eller certifikatet för slutentiteten identifierar certifikat innehavaren. Den har rot certifikatet i sin certifikat kedja samt noll eller flera mellanliggande certifikat. Löv certifikatet används inte för att signera andra certifikat. Den identifierar enheten unikt för etablerings tjänsten och kallas ibland för enhets certifikatet. Vid autentiseringen använder enheten den privata nyckel som är kopplad till det här certifikatet för att svara på ett bevis på innehav av en utmaning från tjänsten.

Löv certifikat som används med en [enskild registrerings](./concepts-service.md#individual-enrollment) post har ett krav på att **ämnes namnet** måste anges till registrerings-ID för den enskilda registrerings posten. Löv certifikat som används med en [registrerings grupp](./concepts-service.md#enrollment-group) post ska ha **ämnes namnet** inställt på önskat enhets-ID som visas i **registrerings posterna** för den autentiserade enheten i registrerings gruppen.

Mer information finns i [autentisera enheter som är signerade med X. 509 CA-certifikat](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Kontrol lera enhets åtkomst till etablerings tjänsten med X. 509-certifikat

Etablerings tjänsten exponerar två typer av registrerings poster som du kan använda för att kontrol lera åtkomsten för enheter som använder mekanismen för 509-attestering i X.:  

- [Enskilda registrerings](./concepts-service.md#individual-enrollment) poster konfigureras med enhets certifikatet som är associerat med en viss enhet. Dessa poster styr registreringar för vissa enheter.
- [Registrerings grupps](./concepts-service.md#enrollment-group) poster är associerade med ett angivet mellanliggande eller rotcertifikatutfärdarcertifikat. Dessa poster styr registreringar för alla enheter som har samma mellanliggande eller rot certifikat i certifikat kedjan. 

När en enhet ansluter till etablerings tjänsten prioriterar tjänsten mer detaljerade registrerings poster över mindre angivna registrerings poster. Det vill säga, om det finns en enskild registrering för enheten, använder etablerings tjänsten den posten. Om det inte finns någon individuell registrering för enheten och en registrerings grupp för det första mellanliggande certifikatet i enhetens certifikat kedja finns, tillämpar tjänsten den posten och så vidare, så att kedjan placeras i roten. Tjänsten använder den första tillämpliga posten som den hittar, till exempel:

- Om den första registrerings posten som hittas är aktive rad, etablerar enheten enheten.
- Om den första registrerings posten som hittas är inaktive rad, etablerar inte tjänsten enheten.  
- Om ingen registrerings post hittas för något av certifikaten i enhetens certifikat kedja, etablerar inte tjänsten enheten. 

Den här mekanismen och den hierarkiska strukturen i certifikat kedjor ger kraftfull flexibilitet i hur du kan styra åtkomsten för enskilda enheter samt för grupper av enheter. Tänk dig till exempel fem enheter med följande certifikat kedjor: 

- *Enhet 1*: rot certifikat-> certifikat A-> enhet 1 certifikat
- *Enhet 2*: rot certifikat-> certifikat A-> enhet 2 certifikat
- *Enhet 3*: rot certifikat-> certifikat A-> enhet 3 certifikat
- *Enhet 4*: rot certifikat – > certifikat B – > Device 4-certifikat
- *Enhet 5*: rot certifikat-> certifikat B-> enhet 5-certifikat

Du kan börja med att skapa en enda aktive rad grupp registrerings post för rot certifikatet för att aktivera åtkomst för alla fem enheterna. Om certifikat B senare blir komprometterade kan du skapa en inaktive rad registrerings grupp för certifikat B för att förhindra att *enhet 4* och *enhet 5* registreras. Om du fortfarande har en senare *enhet 3* kan du skapa en inaktive rad enskild registrerings post för certifikatet. Detta återkallar åtkomst för *enhet 3*, men tillåter fortfarande att *enhet 1* och *enhet 2* registreras.