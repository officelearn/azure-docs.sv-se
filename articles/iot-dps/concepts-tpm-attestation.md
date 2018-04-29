---
title: Azure IoT-hubb enheten etablering Service - TPM-Nyckelattestering
description: Den här artikeln innehåller en översikt över TPM attestering flödet med Etableringstjänsten för IoT-enhet.
services: iot-dps
keywords: ''
author: nberdy
ms.author: nberdy
ms.date: 04/23/2018
ms.topic: conceptual
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: dec024c5c23bf8c628457127af57b8d18800660e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="tpm-attestation"></a>TPM-attestering

IoT-hubb Device etablering Service är en helper-tjänsten för IoT-hubb som används för att konfigurera zero touch enhet etablering till en angiven IoT-hubb. Du kan etablera miljoner enheter med tjänsten etablering enheten på ett säkert sätt.

Den här artikeln beskriver identitet attestering processen när du använder en [TPM](./concepts-device.md). TPM står för Trusted Platform Module och är en typ av maskinvarusäkerhetsmodul (HSM). Den här artikeln förutsätter att du använder en diskret, inbyggd programvara eller inbyggd TPM. Programvara emulerade TPM lämpar sig väl för prototyper eller testning, men de gör inte ger samma nivå av säkerhet som diskreta, inbyggd programvara eller göra inbyggd TPM. Vi rekommenderar inte med hjälp av programvara TPM: er i produktion. [Lär dig mer](http://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf) om vilka typer av TPM: er.

Den här artikeln gäller endast för enheter med stöd för HMAC-nyckel och nycklar stöder TPM 2.0. Det är inte för enheter med X.509-certifikat för autentisering. TPM är en branschomfattande, ISO-standard från Trusted Computing Group, och du kan läsa mer om TPM på den [fullständig TPM 2.0-specifikationen](https://trustedcomputinggroup.org/tpm-library-specification/) eller [ISO/IEC 11889 spec](https://www.iso.org/standard/66510.html). Den här artikeln förutsätter också att du är bekant med offentliga och privata nyckelpar och hur de används för kryptering.

Etablering av tjänst enheten SDK hantera allt som beskrivs i den här artikeln för dig. Det finns inget behov av du implementera något mer om du använder SDK: erna på dina enheter. Den här artikeln hjälper dig att förstå begreppsmässigt vad som händer med din TPM-chip för säkerhet när din enhet bestämmelser och varför det är så är säker.

## <a name="overview"></a>Översikt

TPM: er använda något som kallas bekräftelsenyckel (EK) som säkra förtroenderoten. En Bekräftelsenyckel som är unika för TPM och ändra den i stort sett ändrar enheten till en ny.

Det finns en annan typ av nyckel att TPM: er har, kallas lagringsrotnyckel (SRK). En SRK kan genereras av TPM-ägare när ägarskapet av TPM. Bli ägare till TPM är hur TPM-specifika detsamma ”någon anger ett lösenord för HSM”. Om en TPM-enheten säljs till en ny ägare, överta den nya ägaren ägarskapet för att generera en ny SRK TPM. Den nya SRK generationen garanterar den tidigare ägaren inte kan använda TPM. Eftersom SRK är unika för ägaren av TPM, användas SRK för att försegla data in i TPM sig själv för den aktuella ägaren. SRK innehåller en sandbox för ägare att lagra sina nycklar samt revocability åtkomst om enheten eller TPM säljs. Det är som att flytta till en ny house: ägarskap ändrar lås på dörrar och förstöra alla möbler lämnat tidigare ägare (SRK), men du kan inte ändra adressen till house (EK).

När en enhet har ställts in och klar att användas, den har både en EK och en SRK som är tillgängliga för användning.

![Bli ägare till TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

En anteckning på ägarskap av TPM: ägarskap av TPM beror på många olika saker, inklusive TPM-tillverkare, uppsättning TPM-verktyg som används och enhetens operativsystem. Följ instruktionerna i systemet för att bli ägare.

Tjänsten etablering enheten använder den offentliga delen av EK (EK_pub) för att identifiera och registrera enheter. Tillverkaren kan läsa EK_pub under tillverkning eller sista testning och ladda upp EK_pub etablering tjänsten så att enheten identifieras när den ansluter till etablera. Etablering av tjänst kontrollerar inte SRK eller ägare, så att ”rensa” TPM raderar kundinformation, men en Bekräftelsenyckel (och andra leverantörsdata) bevaras och enheten fortfarande känns av tjänsten etablering enheten när den ansluter till etablera.

## <a name="detailed-attestation-process"></a>Attesteringen av detaljerad process

När en enhet med en TPM först ansluter till enheten Etableringstjänsten kontrollerar tjänsten först den angivna EK_pub mot EK_pub lagras i listan över registrering. Om inte EK_pubs matchar tillåts inte enheten att etablera. Om EK_pubs matchar kräver tjänsten sedan enheten för att bevisa ägarskapet för den privata delen av EK via en utmaning har temporärt ID, vilket är en säker utmaning som används för att bevisa identiteten. Enheten Etableringstjänsten genererar ett temporärt ID och krypteras den med SRK och EK_pub, som tillhandahålls av enheten under registreringen anropet. TPM behålls alltid den privata delen av en Bekräftelsenyckel säker. Detta förhindrar förfalskning och SAS-token tillhandahålls på ett säkert sätt till auktoriserade enheter.

Låt oss gå igenom processen attestering i detalj.

### <a name="device-requests-an-iot-hub-assignment"></a>Enhet begär en IoT-hubb tilldelning

Först enheten ansluter till enheten Etableringstjänsten och begär att etablera. På så sätt kan innehåller enheten tjänsten med dess registrerings-ID, ett ID-scope och EK_pub och SRK_pub från TPM. Tjänsten skickar krypterade temporärt ID tillbaka till enheten och ber enheten att dekryptera temporärt ID och använda den för att logga en SAS-token för att ansluta igen och slutföra etablering.

![Begäranden om etablering och enhet](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Temporärt ID utmaning

Enheten tar temporärt ID och den privata delen av EK och SRK för att dekryptera temporärt ID in i TPM; ordningen på temporärt ID kryptering delegater förtroende från en Bekräftelsenyckel som kan inte ändras till SRK som kan ändra om en ny ägare ägarskapet till TPM.

![Dekryptera temporärt ID](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Validera temporärt ID och ta emot autentiseringsuppgifter

Enheten kan logga en SAS-token med dekrypterade temporärt ID och återupprätta en anslutning till enheten Etableringstjänsten med hjälp av SAS-token som signerade. Med den temporärt ID utmaning har slutförts, kan tjänsten etablera enheten.

![Enheten återupprättar anslutningen till DP för att validera EK-ägare](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Nästa steg

Nu när enheten ansluter till IoT-hubb och du placerar säkra i vetskapen om att dina enheter nycklar lagras på ett säkert sätt. Nu när du vet hur tjänsten etablering enheten på ett säkert sätt bekräftar identiteten för en enhet med hjälp av TPM checka ut i följande artiklar om du vill veta mer:

* [Lär dig mer om koncept Automatisk etablering](./concepts-auto-provisioning.md)
* [Komma igång med Automatisk etablering](./quick-setup-auto-provision.md) använder SDK: erna för att ta hand om flödet.
