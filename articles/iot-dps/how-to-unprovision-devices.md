---
title: Hur du ta bort etableringen av enheter som har etablerats med Azure IoT Hub-enhet etablering Service | Microsoft Docs
description: Avetablering enheter som har etablerats med Azure IoT Hub etablering av tjänst
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-jamebr;bryanla
ms.date: 05/11/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 87932c1ae1676d36ea0eb1ba7885f2ac455949ae
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Hur du ta bort etableringen av enheter som tidigare har etablerats för automatisk 

Du kan det vara nödvändigt att avetablering enheter som fanns tidigare automatiskt etablerade via tjänsten etablering av enheten. Till exempel en enhet kan säljas eller flyttas till en annan IoT-hubb eller den kan tappas bort, blir stulen eller annat sätt äventyras. 

I allmänhet avetablering en enhet i två steg:

1. Disenroll enheten från etablering tjänsten, för att förhindra framtida auto-etablering. Beroende på om du vill återkalla åtkomst tillfälligt eller permanent, kanske du vill inaktivera eller ta bort en post för registrering. För enheter som använder X.509 attestering, kanske du vill inaktivera/ta bort en post i hierarkin för din befintliga grupper för registrering.  
 
   - Information om hur du disenroll en enhet finns [så disenroll en enhet från Azure IoT-hubb Device etablering Service](how-to-revoke-device-access-portal.md).
   - Information om hur du disenroll en enhet med en av SDK: er för etablering service programmässigt finns [hantera enhetsregistrering med tjänsten SDK](how-to-manage-enrollments-sdks.md).

2. Avregistrera enheten från din IoT-hubb för att förhindra framtida kommunikation och dataöverföringen. Igen, kan du tillfälligt inaktivera eller ta bort enhetens post i identitetsregistret för IoT-hubb där den etablerades permanent. Se [inaktivera enheter](/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) lära dig mer om avstängning. Se ”Device Management / IoT-enheter” för din IoT-hubb-resurs i den [Azure-portalen](https://portal.azure.com).

Hur du utför för att ta bort etableringen av en enhet beror på dess mekanism för attestering av och tillämpliga registrering posten med din etablering tjänst. Följande avsnitt innehåller en översikt över processen, beroende på vilken typ av registrering och attestering.

## <a name="individual-enrollments"></a>Enskilda registreringar
Enheter som använder TPM-nyckelattestering eller X.509 attestering med en lövcertifikatet tillhandahålls via en post i enskilda registrering. 

Om du vill ta bort etableringen av en enhet har som en enskild registrering: 

1. Disenroll enheten från etablering tjänsten:

   - Ta bort enskilda registrering post om du vill återkalla permanent enhetens åtkomst till tjänsten etablering för enheter som använder TPM-nyckelattestering, eller inaktivera posten för att tillfälligt återkalla dess åtkomst. 
   - För enheter som använder X.509 attestering, kan du ta bort eller inaktivera posten. Tänk dock om du tar bort en enskild registrering för en enhet som använder X.509 och en aktiverad registrering gruppen finns för ett signeringscertifikat i att enhetens certifikatkedja enheten kan registrera igen. För sådana enheter kan det vara säkrare att inaktivera registrering av transaktionen. På så vis förhindra att enheten omregistreras, oavsett om en aktiverad registrering gruppen finns för en av dess certifikat för tokensignering.

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

- Att ta bort etableringen av alla enheter som har etablerats genom en grupp för registrering:
  1. Inaktivera gruppen registrering för att blockeringslista dess signeringscertifikat. 
  2. Använd listan över etablerade enheter för registrering av gruppen inaktivera eller ta bort varje enhet från dess respektive IoT-hubb identitetsregistret. 
  3. Efter att inaktivera eller ta bort alla enheter från deras respektive IoT-hubbar kan du om du vill ta bort gruppen registrering. Tänk dock på att om du tar bort gruppen registreringen och det finns en grupp med aktiverad registrering för ett högre upp i certifikatkedjan signeringscertifikat av en eller flera av enheterna de enheterna kan registrera igen. 

- Att ta bort etableringen av en enskild enhet från en grupp för registrering:
  1. Skapa en inaktiverad enskilda registrering för dess lövcertifikatet (enhet). Detta återkallar åtkomst till tjänsten etablering för enheten och tillåter samtidigt åtkomst för andra enheter som har gruppen registrering signeringscertifikat i sina kedjan. Ta inte bort inaktiverad enskilda registreringen för enheten. På så sätt kan enheten registrera på nytt via gruppen registrering. 
  2. Använda listan över etablerade enheter för registrering av gruppen för att hitta IoT-hubb som enheten har etablerats till och inaktivera eller ta bort den från att hubben identitetsregistret. 
  
  










