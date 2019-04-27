---
title: Azure IoT Hub Device Provisioning Service - TPM-attestering
description: Den här artikeln innehåller en översikt över TPM-attestering flödet med hjälp av IoT Device Provisioning-tjänsten.
author: nberdy
ms.author: nberdy
ms.date: 04/23/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 07c5dbce0b98d1c197164f4fc77682f78ede57f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746478"
---
# <a name="tpm-attestation"></a>TPM-attestering

IoT Hub Device Provisioning Service är en hjälptjänst för IoT-hubb som används för att konfigurera zero touch-enhetsetablering till en angiven IoT-hubb. Med Device Provisioning-tjänsten, kan du etablera miljontals enheter på ett säkert sätt.

Den här artikeln beskriver attesteringsprocessen identitet när du använder en [TPM](./concepts-device.md). TPM står för Trusted Platform Module och är en typ av maskinvarusäkerhetsmodul (HSM). Den här artikeln förutsätter att du använder en diskret, inbyggd programvara eller integrerad TPM. Programvara emulerade TPM: er lämpar sig väl för prototyper eller testning, men de gör inte ger samma nivå av säkerhet som diskreta, inbyggd programvara eller integrerad TPM: er att göra. Vi rekommenderar inte att använda programvara TPM: er i produktionen. Mer information om typer av TPM: er finns i [en kort introduktion till TPM](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf).

Den här artikeln gäller endast för enheter som använder TPM 2.0 med stöd för HMAC-nyckel och bekräftelsenyckeln nycklar. Det är inte för enheter som använder X.509-certifikat för autentisering. TPM är en branschomfattande, ISO-standarden från Trusted Computing Group och du kan läsa mer om TPM på den [fullständig TPM 2.0-specifikationen](https://trustedcomputinggroup.org/tpm-library-specification/) eller [ISO/IEC 11889 spec](https://www.iso.org/standard/66510.html). Den här artikeln förutsätts även att du är bekant med offentliga och privata nyckelpar och hur de används för kryptering.

Enheten Device Provisioning-tjänsten med hjälp av SDK: er hantera allt som beskrivs i den här artikeln för dig. Det finns inget behov av att implementera något mer om du använder SDK: erna på dina enheter. Den här artikeln hjälper dig att förstå begreppsmässigt vad som händer med din TPM-chip för säkerhet när din enhet etablerar och varför det är det skyddar.

## <a name="overview"></a>Översikt

TPM: er använder något som kallas bekräftelsenyckeln (EK) som säker förtroenderoten. En Bekräftelsenyckel som är unika för TPM och ändra den i grunden ändrar enheten till en ny.

Det finns en annan typ av nyckel att TPM: er har, heter lagringsrotnyckel (SRK). En SRK kan genereras av TPM-ägare när den ägarskapet till TPM. Ägarskap av TPM är på TPM-specifika sätt av som säger ”någon anger ett lösenord för HSM”. Om en TPM-enhet säljs till en ny ägare, kan den nya ägaren bli ägare till TPM: en för att generera en ny SRK. Den nya generationen SRK säkerställer den föregående ägaren inte kan använda TPM. Eftersom SRK är unika för ägaren av TPM, användas SRK för att försegla data in i TPM själva för den aktuella ägaren. SRK ger en sandlåda för ägare för att kunna lagra sina nycklar och ger åtkomst revocability om enheten eller TPM säljs. Det är som att flytta till en ny house: ägarskap förändrar lås på dörrarna och förstöra alla möbler som lämnats kvar av de föregående ägarna (SRK), men du kan inte ändra adressen för house (EK).

När en enhet har ställts in och klar att användas, får både ett EK och en SRK som är tillgängliga för användning.

![Bli ägare till en TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

En anteckning på ägarskap av TPM: Bli ägare till en TPM beror på många olika saker, inklusive TPM-tillverkare, uppsättning TPM-verktyg som används och enhetens operativsystem. Följ instruktionerna i systemet för att bli ägare.

Device Provisioning-tjänsten använder den offentliga delen av EK (EK_pub) för att identifiera och registrera enheter. Tillverkaren kan läsa EK_pub under tillverkning eller slutlig testning och ladda upp EK_pub till etableringstjänsten, så att enheten identifieras när den ansluter till etablera. Device Provisioning-tjänsten kontrollerar inte SRK eller ägare, så att ”rensa” TPM rensar kunddata, men en Bekräftelsenyckel (och andra leverantörsdata) bevaras och enheten fortfarande känns igen av Device Provisioning-tjänsten när den ansluter till etablera.

## <a name="detailed-attestation-process"></a>Detaljerad attesteringsprocessen

När en enhet med en TPM ansluter först till Device Provisioning-tjänsten, kontrollerar tjänsten först den angivna EK_pub mot EK_pub som lagras i registreringslistan. Om inte EK_pubs matchar tillåts inte enheten att etablera. Om EK_pubs matchar kräver tjänsten sedan enheten att bevisa ägarskapet för den privata delen av EK via en tillfälligt utmaning, vilket är en säker utmaning som används för att bevisa identiteten. Device Provisioning-tjänsten genererar en nonce och krypterar den med SRK och sedan EK_pub, som tillhandahålls av enheten under det första registreringen-anropet. TPM skyddar alltid den privata delen av en Bekräftelsenyckel. Detta förhindrar förfalskning och SAS-token etableras på ett säkert sätt till auktoriserade enheter.

Låt oss gå igenom attesteringsprocessen i detalj.

### <a name="device-requests-an-iot-hub-assignment"></a>Enhet begär en IoT Hub-tilldelning

Enheten ansluter först till Device Provisioning-tjänsten och begär att etablera. Du gör det förser enheten tjänsten med dess registrerings-ID, ett ID-omfång och EK_pub och SRK_pub från TPM. Tjänsten skickar den krypterade nonce till enheten och frågar enheten för att dekryptera nonce och använda den för att logga en SAS-token för att ansluta igen och avsluta etableringen.

![Begäranden om etablering och enhet](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Tillfälligt utmaning

Enheten temporärt ID och använder de privata delarna av EK och SRK för att dekryptera temporärt ID in i TPM; ordningen på tillfälligt kryptering delegerar förtroende från en Bekräftelsenyckel som inte kan ändras till SRK, som kan ändra om en ny ägare ägarskapet till TPM.

![Dekryptera temporärt ID](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Verifiera nonce och ta emot autentiseringsuppgifter

Enheten kan sedan registrera en SAS-token med hjälp av den dekrypterade nonce och återupprätta en anslutning till Device Provisioning-tjänsten med hjälp av signerade SAS-token. Nonce utmaningen har slutförts, kan tjänsten enheten för etablering.

![Enheten som återupprättar anslutningen till Device Provisioning-tjänsten att validera EK ägarskap](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Nästa steg

Nu enheten ansluter till IoT Hub och du helt säker i vetskapen om att dina enheter nycklar lagras på ett säkert sätt. Nu när du vet hur Device Provisioning-tjänsten på ett säkert sätt verifierar en enhetsidentitet som använder TPM, finns i följande artiklar om du vill veta mer:

* [Lär dig mer om alla begreppen i Automatisk etablering](./concepts-auto-provisioning.md)
* [Kom igång med hjälp av Automatisk etablering](./quick-setup-auto-provision.md) använder SDK: erna för att ta hand om flödet.
