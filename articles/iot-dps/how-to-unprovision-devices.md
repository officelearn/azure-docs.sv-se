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
ms.openlocfilehash: 83ed72c0f2eb342c372ef97e5443d60eab1e2174
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2018
---
# <a name="how-to-unprovision-devices-enrolled-by-your-provisioning-service"></a>Så här avetablera enheter som registrerats av etablering tjänsten

Du kan det vara nödvändigt att avetablera enheter som har etablerats via tjänsten etablering av enheten. Till exempel en enhet kan säljas eller flyttas till en annan IoT-hubb eller den kan tappas bort, blir stulen eller annat sätt äventyras. 

I allmänhet avetablering en enhet i två steg:

1. Återkalla åtkomst för enheten till din etablering tjänst. Beroende på om du vill återkalla åtkomst tillfälligt eller permanent eller, för mekanismen X.509 attestering på hierarkin för registrering av befintliga grupper, kanske du vill inaktivera eller ta bort en post för registrering. 
 
   - Information om hur du återkalla Enhetsåtkomst med hjälp av portalen finns [återkalla Enhetsåtkomst](how-to-revoke-device-access-portal.md).
   - Information om hur du återkalla Enhetsåtkomst programmässigt med någon av SDK: er för etablering tjänsten finns [hantera enhetsregistrering med tjänsten SDK](how-to-manage-enrollments-sdks.md).

2. Inaktivera eller ta bort enhetens post i identitetsregistret för IoT-hubb där den etablerades. Läs mer i [Hantera identiteter för enheten](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) i Azure IoT Hub-dokumentationen. 

Hur du utför för att ta bort etableringen för en enhet beror på mekanismen attestering används.

Enheter som använder TPM-nyckelattestering eller X.509 attestering med ett självsignerat lövcertifikatet (ingen certifikatkedja) tillhandahålls via en post i enskilda registrering. För dessa enheter kan du ta bort posten för att återkalla enhetens åtkomst till tjänsten etablering eller inaktivera posten för att tillfälligt återkalla dess åtkomst och uppföljning genom att antingen ta bort eller inaktivera enheten i identitetsregistret med IoT-permanent hubb som den etablerades till.

Med attestering av X.509 kan enheter etableras via en grupp för registrering. Registrering grupper har konfigurerats med ett signeringscertifikat, antingen på mellannivå eller rot-CA-certifikat och styr åtkomsten till tjänsten etablering för enheter med certifikatet i sina certifikatkedja. Mer information om registrering av grupper och X.509-certifikat med tjänsten etablering finns [X.509-certifikat](concepts-security.md#x509-certificates). 

Du kan visa gruppen registrering information om du vill se en lista över enheter som har etablerats genom en grupp för registrering. Detta är ett enkelt sätt att förstå vilka IoT-hubb varje enhet har etablerats till. Visa listan över enheter: 

1. Logga in på Azure portal och klicka på **alla resurser** på den vänstra menyn.
2. Klicka på din etablering service i listan över resurser.
3. I din etablering tjänst klickar du på **hantera registreringar**och välj **registrering grupper** fliken.
4. Klicka på gruppen registrering för att öppna den.

   ![Visa registrering grupp post i portalen](./media/how-to-unprovision-devices/view-enrollment-group.png)

Med registrering grupper finns det två scenarier att tänka på:

- Om du vill avetablera alla enheter som har etablerats genom en grupp för registrering måste du först inaktivera registrering gruppen för att svartlista dess signeringscertifikat. Du kan sedan använda listan över etablerade enheter för registrering av gruppen inaktivera eller ta bort varje enhet från dess respektive IoT-hubb identitetsregistret. Efter att inaktivera eller ta bort alla enheter från deras respektive IoT-hubbar kan du om du vill ta bort gruppen registrering. Tänk dock på att om du tar bort gruppen registreringen och det finns en grupp med aktiverad registrering för ett högre upp i certifikatkedjan signeringscertifikat av en eller flera av enheterna de enheterna kan registrera igen. 
- Om du vill avetablera en enhet från en grupp för registrering måste du först skapa en inaktiverad enskilda registrering för dess lövcertifikatet (enhet). Detta återkallar åtkomst till tjänsten etablering för enheten och tillåter samtidigt åtkomst för andra enheter som har gruppen registrering signeringscertifikat i sina kedjan. Sedan kan du använda listan över etablerade enheter i gruppen registreringsinformation hitta IoT-hubb som enheten har etablerats till och inaktivera eller ta bort den från att hubben identitetsregistret. Ta inte bort inaktiverad enskilda registreringen för enheten. På så sätt kan enheten registrera på nytt via gruppen registrering. 










