---
title: Avetablera enheter som har etablerats med Azure IoT Hub Device Provisioning Service
description: Så här avetablerar du enheter som har etablerats med DPS (Azure IoT Hub Device Provisioning Service)
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 8a3677ba285f5b02407ca3d176979bf6c016ef9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974844"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Så här avetableras enheter som tidigare har etablerats automatiskt 

Det kan vara nödvändigt att avetablera enheter som tidigare har etablerats automatiskt via enhetsetableringstjänsten. En enhet kan till exempel säljas eller flyttas till ett annat IoT-nav, eller så kan den förloras, stjälas eller på annat sätt komprometteras. 

I allmänhet innebär avetablering av en enhet två steg:

1. Avregistrera enheten från etableringstjänsten för att förhindra framtida automatisk etablering. Beroende på om du vill återkalla åtkomsten tillfälligt eller permanent kanske du vill inaktivera eller ta bort en registreringspost. För enheter som använder X.509-intyget kanske du vill inaktivera/ta bort en post i hierarkin för dina befintliga registreringsgrupper.  
 
   - Mer information om hur du inaktiverar en enhet finns i [Så här avregistrerar du en enhet från Azure IoT Hub Device Provisioning Service](how-to-revoke-device-access-portal.md).
   - Mer information om hur du avregistrerar en enhet programmässigt med hjälp av en av etableringstjänsten SDK:er finns i [Hantera enhetsregistreringar med tjänst-SDK:er](how-to-manage-enrollments-sdks.md).

2. Avregistrera enheten från din IoT Hub för att förhindra framtida kommunikation och dataöverföring. Återigen kan du tillfälligt inaktivera eller permanent ta bort enhetens post i identitetsregistret för IoT Hub där den etablerades. Se [Inaktivera enheter](/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) om du vill veta mer om inaktivering. Se "Enhetshantering / IoT-enheter" för din IoT Hub-resurs i [Azure-portalen](https://portal.azure.com).

De exakta stegen du vidtar för att avetablera en enhet beror på dess attestation-mekanism och dess tillämpliga registreringspost med din etableringstjänst. Följande avsnitt innehåller en översikt över processen, baserat på registrerings- och intygstypen.

## <a name="individual-enrollments"></a>Enskilda registreringar
Enheter som använder TPM-intyg eller X.509-intyg med lövcertifikat etableras via en enskild registreringspost. 

Så här avetableras en enhet som har en enskild registrering: 

1. Avregistrera enheten från etableringstjänsten:

   - För enheter som använder TPM-intyg tar du bort den enskilda registreringsposten för att permanent återkalla enhetens åtkomst till etableringstjänsten eller inaktivera posten för att tillfälligt återkalla dess åtkomst. 
   - För enheter som använder X.509-intyget kan du antingen ta bort eller inaktivera posten. Tänk dock på att om du tar bort en enskild registrering för en enhet som använder X.509 och det finns en aktiverad registreringsgrupp för ett signeringscertifikat i enhetens certifikatkedja, kan enheten registrera sig igen. För sådana enheter kan det vara säkrare att inaktivera registreringsposten. Om du gör det hindras enheten från att registrera sig igen, oavsett om det finns en aktiverad registreringsgrupp för ett av dess signeringscertifikat.

2. Inaktivera eller ta bort enheten i identitetsregistret för IoT-hubben som den har etablerats i. 


## <a name="enrollment-groups"></a>Registreringsgrupper
Med X.509 attestation kan enheter också etableras via en registreringsgrupp. Registreringsgrupper konfigureras med ett signeringscertifikat, antingen ett mellanliggande certifikat eller ett rotcertifikatutfärdarcertifikat, och kontrollen av åtkomsten till etableringstjänsten för enheter med certifikatet i certifikatkedjan. Mer information om registreringsgrupper och X.509-certifikat med etableringstjänsten finns i [X.509-certifikat](concepts-security.md#x509-certificates). 

Om du vill visa en lista över enheter som har etablerats via en registreringsgrupp kan du visa information om registreringsgruppen. Detta är ett enkelt sätt att förstå vilken IoT-hubb som varje enhet har etablerats till. Så här visar du enhetslistan: 

1. Logga in på Azure-portalen och klicka på **Alla resurser** på menyn till vänster.
2. Klicka på etableringstjänsten i listan över resurser.
3. Klicka på **Hantera registreringar**i etableringstjänsten och välj sedan fliken **Registreringsgrupper.**
4. Klicka på registreringsgruppen för att öppna den.

   ![Visa registreringsgrupppost i portalen](./media/how-to-unprovision-devices/view-enrollment-group.png)

Med registreringsgrupper finns det två scenarier att tänka på:

- Så här avorförerar du alla enheter som har etablerats via en registreringsgrupp:
  1. Inaktivera registreringsgruppen för att svartlista signeringscertifikatet. 
  2. Använd listan över etablerade enheter för den registreringsgruppen för att inaktivera eller ta bort varje enhet från identitetsregistret för dess respektive IoT-hubb. 
  3. När du har inaktiverat eller tagit bort alla enheter från sina respektive IoT-hubbar kan du också ta bort registreringsgruppen. Tänk dock på att om du tar bort registreringsgruppen och det finns en aktiverad registreringsgrupp för ett registreringscertifikat högre upp i certifikatkedjan för en eller flera enheter, kan dessa enheter registrera sig igen. 

- Så här avetablering av en enhet från en registreringsgrupp:
  1. Skapa en inaktiverad individuell registrering för dess bladcertifikat (enhet). Detta återkallar åtkomsten till etableringstjänsten för den enheten samtidigt som åtkomst tillåts för andra enheter som har registreringsgruppens signeringscertifikat i sin kedja. Ta inte bort den inaktiverade enskilda registreringen för enheten. Om du gör det kan enheten registrera sig igen via registreringsgruppen. 
  2. Använd listan över etablerade enheter för den registreringsgruppen för att hitta IoT-hubben som enheten har etablerats för och inaktivera eller ta bort den från hubbens identitetsregister. 
  
  










