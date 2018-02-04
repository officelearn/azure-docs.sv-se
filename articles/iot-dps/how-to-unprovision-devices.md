---
title: "Så här avetablera enheter som registrerats med Azure IoT-hubb Device etablering Service | Microsoft Docs"
description: "Så här avetablera enheter som registrerats av DP-tjänsten i Azure Portal"
services: iot-dps
keywords: 
author: JimacoMS
ms.author: v-jamebr
ms.date: 01/08/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 1d057a4df43cf25e6817672d198207d9a50e462e
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
# <a name="how-to-unprovision-devices-enrolled-by-your-provisioning-service"></a>Så här avetablera enheter som registrerats av etablering tjänsten

Du kan det vara nödvändigt att avetablera enheter som har etablerats via tjänsten etablering av enheten. Till exempel en enhet kan säljas eller flyttas till en annan IoT-hubb eller den kan tappas bort, blir stulen eller annat sätt äventyras. 

I allmänhet avetablering en enhet i två steg:

1. Återkalla åtkomst för enheten till din etablering tjänst. Beroende på om du vill återkalla åtkomst tillfälligt eller permanent eller, för mekanismen X.509 attestering på hierarkin för registrering av befintliga grupper, kanske du vill inaktivera eller ta bort en post för registrering. 
 
   - Information om hur du återkalla Enhetsåtkomst med hjälp av portalen finns [återkalla Enhetsåtkomst](how-to-revoke-device-access-portal.md).
   - Information om hur du återkalla Enhetsåtkomst programmässigt med någon av SDK: er för etablering tjänsten finns [hantera enhetsregistrering med tjänsten SDK](how-to-manage-enrollments-sdks.md).

2. Inaktivera eller ta bort enhetens post i identitetsregistret för IoT-hubb där den etablerades. Läs mer i [Hantera identiteter för enheten](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) i Azure IoT Hub-dokumentationen. 

Hur du utför för att ta bort etableringen för en enhet beror på dess mekanism för attestering av och tillämpliga registrering posten med din etablering tjänst.

## <a name="individual-enrollments"></a>Enskilda registreringar
Enheter som använder TPM-nyckelattestering eller X.509 attestering med en lövcertifikatet tillhandahålls via en post i enskilda registrering. 

Om du vill ta bort etableringen för en enhet har som en enskild registrering: 
1. Ta bort enskilda registrering-post för att permanent återkalla enhetens åtkomst till tjänsten etablering eller inaktivera posten för att tillfälligt återkalla dess åtkomst för enheter som använder TPM-nyckelattestering. För enheter som använder X.509 attestering, kan du ta bort eller inaktivera posten. Tänk dock att om du tar bort ett enskilt registrering för en enhet som använder X.509 intyg och en aktiverad registrering gruppen finns för ett signeringscertifikat i att enhetens certifikatkedja enheten kan registrera igen. För sådana enheter kan det vara säkrare att inaktivera registrering av transaktionen. På så vis förhindra att enheten omregistreras, oavsett om en aktiverad registrering gruppen finns för en av dess certifikat för tokensignering.
2. Inaktivera eller ta bort enheten i identitetsregistret för IoT-hubb som den etablerades till. 


## <a name="enrollment-groups"></a>Registrering av grupper
Med attestering av X.509 kan enheter etableras via en grupp för registrering. Registrering grupper har konfigurerats med ett signeringscertifikat, antingen på mellannivå eller rot-CA-certifikat och styr åtkomsten till tjänsten etablering för enheter med certifikatet i sina certifikatkedja. Mer information om registrering av grupper och X.509-certifikat med tjänsten etablering finns [X.509-certifikat](concepts-security.md#x509-certificates). 

Du kan visa gruppen registrering information om du vill se en lista över enheter som har etablerats genom en grupp för registrering. Detta är ett enkelt sätt att förstå vilka IoT-hubb varje enhet har etablerats till. Visa listan över enheter: 

1. Logga in på Azure portal och klicka på **alla resurser** på den vänstra menyn.
2. Klicka på din etablering service i listan över resurser.
3. I din etablering tjänst klickar du på **hantera registreringar**och välj **registrering grupper** fliken.
4. Klicka på gruppen registrering för att öppna den.

   ![Visa registrering grupp post i portalen](./media/how-to-unprovision-devices/view-enrollment-group.png)

Med registrering grupper finns det två scenarier att tänka på:

- Att ta bort etableringen av de enheter som har etablerats genom en grupp för registrering:
  1. Inaktivera gruppen registrering för att svartlista dess signeringscertifikat. 
  2. Använd listan över etablerade enheter för registrering av gruppen inaktivera eller ta bort varje enhet från dess respektive IoT-hubb identitetsregistret. 
  3. Efter att inaktivera eller ta bort alla enheter från deras respektive IoT-hubbar kan du om du vill ta bort gruppen registrering. Tänk dock på att om du tar bort gruppen registreringen och det finns en grupp med aktiverad registrering för ett högre upp i certifikatkedjan signeringscertifikat av en eller flera av enheterna de enheterna kan registrera igen. 
- Att avetablera en enhet från en grupp för registrering:
  1. Skapa en inaktiverad enskilda registrering för dess lövcertifikatet (enhet). Detta återkallar åtkomst till tjänsten etablering för enheten och tillåter samtidigt åtkomst för andra enheter som har gruppen registrering signeringscertifikat i sina kedjan. Ta inte bort inaktiverad enskilda registreringen för enheten. På så sätt kan enheten registrera på nytt via gruppen registrering. 
  2. Använda listan över etablerade enheter för registrering av gruppen för att hitta IoT-hubb som enheten har etablerats till och inaktivera eller ta bort den från att hubben identitetsregistret. 
  
  










