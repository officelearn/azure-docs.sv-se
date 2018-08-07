---
title: Hur du avetablera enheter som har etablerats med Azure IoT Hub Device Provisioning-tjänsten | Microsoft Docs
description: Hur du avetablera enheter som har etablerats med Azure IoT Hub Device Provisioning-tjänsten
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 3c6e2a9006d73d269422292dc959866d3f6d8a82
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522680"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Hur du avetablera enheter som har tidigare Automatisk etablering 

Du kan det vara nödvändigt att avetablering enheter som har tidigare Automatisk etablering via Device Provisioning-tjänsten. Till exempel en enhet kan säljas eller flyttas till en annan IoT-hubb eller den kan tappas bort, blir stulen eller annat sätt äventyras. 

I allmänhet omfattar avetablering en enhet två steg:

1. Disenroll enheten från din etableringstjänst för att förhindra framtida automatisk för etablering. Beroende på om du vill återkalla åtkomsten tillfälligt eller permanent, kan du inaktivera eller ta bort en post för registrering. För enheter som använder X.509-attestering, kanske du vill inaktivera/ta bort en post i hierarkin för din befintliga registreringsgrupper.  
 
   - Läs hur du disenroll en enhet i [så disenroll en enhet från Azure IoT Hub Device Provisioning Service](how-to-revoke-device-access-portal.md).
   - Läs hur du disenroll en enhet programmässigt med någon av SDK: er för etablering service i [hantera enhetsregistreringar med tjänst-SDK: er](how-to-manage-enrollments-sdks.md).

2. Avregistrera enheten från din IoT-hubb, att förhindra framtida kommunikation och dataöverföring. Igen, kan du inaktivera tillfälligt eller permanent ta bort enhetens post i identitetsregistret för IoT-hubben där den har etablerats. Se [inaktivera enheter](/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) mer information om avstängning. Se ”/ IoT enheter” för din IoT Hub-resurs i den [Azure-portalen](https://portal.azure.com).

Hur du utför för att ta bort etableringen av en enhet beror på dess attesteringsmetod och dess tillämpliga registreringspost till etableringstjänsten. Följande avsnitt innehåller en översikt över processen, beroende på vilken registrering och attestering.

## <a name="individual-enrollments"></a>Enskilda registreringar
Enheter som använder TPM-attestering eller X.509-attestering med en lövcertifikatet tillhandahålls via en individuell registreringspost. 

Om du vill ta bort etableringen av en enhet har som en enskild registrering: 

1. Disenroll enheten från etableringstjänsten:

   - Ta bort posten för enskild registrering om du vill återkalla permanent Enhetsåtkomst till etableringstjänsten för enheter som använder TPM-attestering, eller inaktivera på posten för att tillfälligt återkalla åtkomsten. 
   - För enheter som använder X.509-attestering, kan du ta bort eller inaktivera posten. Tänk dock om du tar bort en enskild registrering för en enhet som använder X.509 och en aktiverad registreringsgrupp finns för ett signeringscertifikat i att enhetens certifikatkedjan, enheten kan registrera igen. Det kan vara säkrare att inaktivera registreringsposten för sådana enheter. På så vis förhindra enheten från Omregistrera, oavsett om en aktiverad registreringsgrupp finns för en av dess certifikat för tokensignering.

2. Inaktivera eller ta bort enheten i identitetsregistret i IoT-hubben som etablerades till. 


## <a name="enrollment-groups"></a>Registreringsgrupper
Med X.509-attestering, kan du också etablera enheter via en grupp för registrering. Registreringsgrupper har konfigurerats med ett signeringscertifikat, antingen ett mellanliggande certifikat eller rot CA-certifikat och styr åtkomsten till etableringstjänsten för enheter med detta certifikat i certifikatkedjan. Läs mer om registrering av grupper och X.509-certifikat med etableringstjänsten i [X.509-certifikat](concepts-security.md#x509-certificates). 

Du kan visa information om grupp för registrering om du vill se en lista över enheter som har etablerats via en grupp för registrering. Det här är ett enkelt sätt att förstå vilken IoT-hubb varje enhet har etablerats. Visa listan över enheter: 

1. Logga in på Azure-portalen och klicka på **alla resurser** i den vänstra menyn.
2. Klicka på din etableringstjänst i listan över resurser.
3. I etableringstjänsten, klickar du på **hantera registreringar**och välj sedan **Registreringsgrupper** fliken.
4. Klicka på grupp för registrering för att öppna den.

   ![Visa post för registreringsgruppen i portalen](./media/how-to-unprovision-devices/view-enrollment-group.png)

Det finns två scenarier för att tänka på med registreringsgrupper:

- Att avetablera alla enheter som har etablerats via en grupp för registrering:
  1. Inaktivera gruppen registrering för att blockeringslista dess signeringscertifikat. 
  2. Använda i listan med etablerade enheter för registreringsgruppen för att inaktivera eller ta bort varje enhet från identitetsregistret för dess respektive IoT-hubb. 
  3. Du kan också ta bort grupp för registrering när du inaktiverar eller tar bort alla enheter från deras respektive IoT-hubbar. Tänk dock på att om du tar bort grupp för registrering och det finns en aktiverad registreringsgrupp för ett signeringscertifikat som är högre upp i certifikatkedjan för en eller flera av enheterna som dessa enheter kan registrera igen. 

- Att avetablera en enskild enhet från en grupp för registrering:
  1. Skapa en inaktiverad enskild registrering för dess lövcertifikatet (enhet). Det här återkallar åtkomst till etableringstjänsten för enheten och tillåter åtkomst för andra enheter som har en grupp för registrering signeringscertifikat i sina kedjan samtidigt. Ta inte bort den inaktiverade enskilda registreringen för enheten. På så sätt kan enheten för att registrera igen via grupp för registrering. 
  2. Använda i listan med etablerade enheter för registreringsgruppen för att hitta IoT-hubben som enheten har etablerats till och inaktivera eller ta bort det från den hubbens identitetsregister. 
  
  










