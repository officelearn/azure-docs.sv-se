---
title: Etableringstjänst för Azure IoT Hub-enhet – TPM-attestation
description: Den här artikeln innehåller en begreppsmässig översikt över TPM-attestationflödet med DPS (IoT Device Provisioning Service).
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 624171ffc10a06ac3089b6dceb1683c63c88dbda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975286"
---
# <a name="tpm-attestation"></a>TPM-attestering

IoT Hub Device Provisioning Service är en hjälptjänst för IoT Hub som du använder för att konfigurera zero-touch-enhetsetablering till en angiven IoT-hubb. Med enhetsetableringstjänsten kan du tillhandahålla miljontals enheter på ett säkert sätt.

I den här artikeln beskrivs identitetsintygsprocessen när du använder en [TPM](./concepts-device.md). TPM står för Trusted Platform Module och är en typ av maskinvarusäkerhetsmodul (HSM). Den här artikeln förutsätter att du använder en diskret, firmware eller integrerad TPM. Programvarureglerade TPM:er är väl lämpade för prototyper eller testning, men de ger inte samma säkerhetsnivå som diskreta, inbyggda eller integrerade TPM:er gör. Vi rekommenderar inte att du använder program-TPM:er i produktion. Mer information om typer av TPM:er finns i [En kort introduktion till TPM](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf).

Den här artikeln är endast relevant för enheter som använder TPM 2.0 med HMAC-nyckelstöd och deras godkännandenycklar. Det är inte för enheter som använder X.509-certifikat för autentisering. TPM är en branschomfattande ISO-standard från Trusted Computing Group, och du kan läsa mer om TPM på [den kompletta TPM 2.0 spec](https://trustedcomputinggroup.org/tpm-library-specification/) eller [ISO / IEC 11889 spec](https://www.iso.org/standard/66510.html). Den här artikeln förutsätter också att du är bekant med offentliga och privata nyckelpar och hur de används för kryptering.

Enhetsetableringstjänstens SDK:er hanterar allt som beskrivs i den här artikeln åt dig. Du behöver inte implementera något ytterligare om du använder SDK:erna på dina enheter. Den här artikeln hjälper dig att förstå begreppsmässigt vad som händer med din TPM-säkerhet chip när enheten avsättningar och varför den är så säker.

## <a name="overview"></a>Översikt

TPM använder något som kallas godkännandenyckel (EK) som säker rot till förtroende. EK är unikt för TPM och ändra det i huvudsak ändrar enheten till en ny.

Det finns en annan typ av nyckel som TPM har, som kallas lagringsrotnyckeln (SRK). En SRK kan genereras av TPM: s ägare efter det att den tar över ägandet av TPM. Att ta ansvar för TPM är TPM-specifika sättet att säga "någon sätter ett lösenord på HSM." Om en TPM-enhet säljs till en ny ägare kan den nya ägaren bli ägare till TPM för att generera en ny SRK. The new SRK generation ensures the previous owner can't use the TPM. Eftersom SRK är unikt för ägaren av TPM, kan SRK användas för att försegla data i TPM själv för den ägaren. SRK ger en sandlåda för ägaren att lagra sina nycklar och ger tillgång till återkallbarhet om enheten eller TPM säljs. Det är som att flytta in i ett nytt hus: att ta ansvar är att ändra låsen på dörrarna och förstöra alla möbler kvar av de tidigare ägarna (SRK), men du kan inte ändra adressen till huset (EK).

När en enhet har ställts in och är klar att användas, kommer den att ha både en EK och en SRK tillgänglig för användning.

![Att bli ägare till en TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

En anteckning om att bli ägare till TPM: Att bli ägare till en TPM beror på många saker, inklusive TPM-tillverkare, uppsättningen TPM-verktyg som används och enheten OS. Följ instruktionerna som är relevanta för ditt system för att bli ägare.

Enhetsetableringstjänsten använder den offentliga delen av EK (EK_pub) för att identifiera och registrera enheter. Enhetsleverantören kan läsa EK_pub under tillverkning eller slutlig testning och ladda upp EK_pub till etableringstjänsten så att enheten identifieras när den ansluter till etablering. Enhetsetableringstjänsten kontrollerar inte SRK eller ägaren, så "rensa" TPM:en raderar kunddata, men EK (och andra leverantörsdata) bevaras och enheten kommer fortfarande att kännas igen av enhetsetableringstjänsten när den ansluter till etablering.

## <a name="detailed-attestation-process"></a>Detaljerad attestation process

När en enhet med en TPM först ansluter till enhetsetableringstjänsten kontrollerar tjänsten först den medföljande EK_pub mot EK_pub som lagras i registreringslistan. Om EK_pubs inte matchar tillåts inte enheten att etablera. Om EK_pubs matchar, kräver tjänsten sedan enheten för att bevisa äganderätten till den privata delen av EK via en nonce utmaning, vilket är en säker utmaning som används för att bevisa identitet. Enhetsetableringstjänsten genererar en nonce och krypterar den sedan med SRK och sedan EK_pub, som båda tillhandahålls av enheten under det första registreringssamtalet. TPM:en håller alltid den privata delen av EKen säker. Detta förhindrar förfalskning och säkerställer att SAS-token etableras på ett säkert sätt till auktoriserade enheter.

Låt oss gå igenom attestation processen i detalj.

### <a name="device-requests-an-iot-hub-assignment"></a>Enheten begär en IoT Hub-tilldelning

Först ansluter enheten till enhetsetableringstjänsten och begäranden om att etablera. På så sätt tillhandahåller enheten tjänsten med sitt registrerings-ID, ett ID-scope och EK_pub och SRK_pub från TPM.In doing so, the device provides the service with its registration ID, an ID scope, and the EK_pub and SRK_pub from the TPM. Tjänsten skickar den krypterade nonce tillbaka till enheten och ber enheten att dekryptera nonce och använda den för att underteckna en SAS-token för att ansluta igen och avsluta etablering.

![Etablering av enhetsbegäranden](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Nonce utmaning

Enheten tar nonce och använder den privata delar av EK och SRK att dekryptera nonce i TPM; ordningen för nonce kryptering delegater förtroende från EK, som är oföränderlig, till SRK, som kan ändras om en ny ägare tar ansvar för TPM.

![Dekryptera nonce](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Validera nonce och ta emot autentiseringsuppgifter

Enheten kan sedan signera en SAS-token med den dekrypterade nonce och återupprätta en anslutning till enhetsetableringstjänsten med den signerade SAS-token. Med Nonce-utmaningen avslutad tillåter tjänsten enheten att etablera.

![Enheten återupprättar anslutningen till enhetsetableringstjänsten för att verifiera EK-ägarskap](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Nästa steg

Nu ansluter enheten till IoT Hub och du är säker i vetskapen om att enheternas nycklar är säkert lagrade. Nu när du vet hur enhetsetableringstjänsten verifierar en enhets identitet på ett säkert sätt med TPM kan du läsa följande artiklar om du vill veta mer:

* [Lär dig mer om alla begrepp i automatisk etablering](./concepts-auto-provisioning.md)
* [Kom igång med automatisk etablering](./quick-setup-auto-provision.md) med hjälp av SDK:er för att ta hand om flödet.
