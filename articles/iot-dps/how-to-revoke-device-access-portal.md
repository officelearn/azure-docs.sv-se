---
title: Avregistrera enheten från Azure IoT Hub Device Provisioning Service
description: Så här avregistrerar du en enhet för att förhindra etablering via DPS (Azure IoT Hub Device Provisioning Service)
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: af883da67f4e1bc819514e88ff480526e16124db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974929"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Så här avregistrerar du en enhet från Azure IoT Hub Device Provisioning Service

Korrekt hantering av enhetsautentiseringsuppgifter är avgörande för högprofilerade system som IoT-lösningar. Bästa praxis för sådana system är att ha en tydlig plan för hur du återkallar åtkomst för enheter när deras autentiseringsuppgifter, oavsett om en SAS-token (Shared Access Signatures) eller ett X.509-certifikat kan äventyras. 

Registrering i tjänsten Enhetsetablering gör att en enhet [kan etableras automatiskt](concepts-auto-provisioning.md). En etablerad enhet är en enhet som har registrerats med IoT Hub, så att den kan ta emot sitt ursprungliga [enhetstvillingtillstånd](~/articles/iot-hub/iot-hub-devguide-device-twins.md) och börja rapportera telemetridata. I den här artikeln beskrivs hur du avregistrerar en enhet från etableringstjänstinstansen, vilket förhindrar att den etableras igen i framtiden.

> [!NOTE] 
> Tänk på principen för återförsök för enheter som du återkallar åtkomst för. En enhet som har en princip för ett oändligt återförsök kan till exempel kontinuerligt försöka registrera sig hos etableringstjänsten. Den situationen förbrukar serviceresurser och eventuellt påverkar prestanda.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Svartlista enheter med hjälp av en enskild registreringspost

Enskilda registreringar gäller för en enda enhet och kan använda antingen X.509-certifikat eller SAS-token (i en verklig eller virtuell TPM) som attestation mekanism. (Enheter som använder SAS-token som sin attestationmekanism kan endast etableras genom en enskild registrering.) Om du vill svartlista en enhet som har en enskild registrering kan du antingen inaktivera eller ta bort registreringsposten. 

Så här svartlistar du enheten tillfälligt genom att inaktivera registreringsposten: 

1. Logga in på Azure-portalen och välj **Alla resurser** på den vänstra menyn.
2. I listan över resurser väljer du den etableringstjänst som du vill svartlista enheten från.
3. I etableringstjänsten väljer du **Hantera registreringar**och väljer sedan fliken **Enskilda registreringar.**
4. Välj registreringsposten för den enhet som du vill svartlista. 

    ![Välj din individuella registrering](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. På registreringssidan bläddrar du längst ned och väljer **Inaktivera** för **inmatningsväxeln Aktivera** och väljer sedan **Spara**.  

   ![Inaktivera enskilda registreringspost i portalen](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Så här svartlistar du enheten permanent genom att ta bort dess registreringspost:

1. Logga in på Azure-portalen och välj **Alla resurser** på den vänstra menyn.
2. I listan över resurser väljer du den etableringstjänst som du vill svartlista enheten från.
3. I etableringstjänsten väljer du **Hantera registreringar**och väljer sedan fliken **Enskilda registreringar.**
4. Markera kryssrutan bredvid registreringsposten för den enhet som du vill svartlista. 
5. Välj **Ta bort** högst upp i fönstret och välj sedan **Ja** för att bekräfta att du vill ta bort registreringen. 

   ![Ta bort enskild registreringspost i portalen](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


När du är klar med proceduren bör du se din post tas bort från listan över enskilda registreringar.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Svartlista ett X.509 mellanliggande certifikat eller rotcertifikatutfärdare med hjälp av en registreringsgrupp

X.509-certifikat är vanligtvis ordnade i en certifikatkedja. Om ett certifikat i något skede i en kedja äventyras bryts förtroendet. Certifikatet måste svartlistas för att förhindra att enhetsetableringstjänsten etablerar enheter nedströms i någon kedja som innehåller certifikatet. Mer information om X.509-certifikat och hur de används med etableringstjänsten finns i [X.509-certifikat](./concepts-security.md#x509-certificates). 

En registreringsgrupp är en post för enheter som delar en gemensam attestation-mekanism för X.509-certifikat som signerats av samma mellanliggande certifikatutfärdare eller rotcertifikatutfärdare. Registreringsgruppposten är konfigurerad med X.509-certifikatet som är associerat med mellanliggande certifikat eller rotcertifikatutfärdaren. Posten är också konfigurerad med alla konfigurationsvärden, till exempel tvillingtillstånd och IoT-hubbanslutning, som delas av enheter med certifikatet i certifikatkedjan. Om du vill svartlista certifikatet kan du antingen inaktivera eller ta bort registreringsgruppen.

Så här svartlistar du certifikatet tillfälligt genom att inaktivera registreringsgruppen: 

1. Logga in på Azure-portalen och välj **Alla resurser** på den vänstra menyn.
2. I listan över resurser väljer du den etableringstjänst som du vill svartlista signeringscertifikatet från.
3. I etableringstjänsten väljer du **Hantera registreringar**och väljer sedan fliken **Registreringsgrupper.**
4. Markera registreringsgruppen med det certifikat som du vill svartlista.
5. Välj **Inaktivera** vid **växeln Aktivera inmatning** och välj sedan **Spara**.  

   ![Inaktivera registreringsgrupppost i portalen](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Så här svartlistar du certifikatet permanent genom att ta bort dess registreringsgrupp:

1. Logga in på Azure-portalen och välj **Alla resurser** på den vänstra menyn.
2. I listan över resurser väljer du den etableringstjänst som du vill svartlista enheten från.
3. I etableringstjänsten väljer du **Hantera registreringar**och väljer sedan fliken **Registreringsgrupper.**
4. Markera kryssrutan bredvid registreringsgruppen för det certifikat som du vill svartlista. 
5. Välj **Ta bort** högst upp i fönstret och välj sedan **Ja** för att bekräfta att du vill ta bort registreringsgruppen. 

   ![Ta bort registreringsgrupppost i portalen](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

När du är klar med proceduren bör du se din post tas bort från listan över registreringsgrupper.  

> [!NOTE]
> Om du tar bort en registreringsgrupp för ett certifikat kan enheter som har certifikatet i certifikatkedjan fortfarande kunna registrera sig om en aktiverad registreringsgrupp för rotcertifikatet eller ett annat mellanliggande certifikat högre upp i certifikatet kedjan finns.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Svartlista specifika enheter i en registreringsgrupp

Enheter som implementerar X.509-attestationsmekanismen använder enhetens certifikatkedja och privata nyckel för att autentisera. När en enhet ansluter och autentiserar med enhetsetableringstjänsten söker tjänsten först efter en enskild registrering som matchar enhetens autentiseringsuppgifter. Tjänsten söker sedan i registreringsgrupper för att avgöra om enheten kan etableras. Om tjänsten hittar en inaktiverad individuell registrering för enheten förhindrar den att enheten ansluter. Tjänsten förhindrar anslutningen även om det finns en aktiverad registreringsgrupp för en mellanliggande eller rotcertifikatutfärdare i enhetens certifikatkedja. 

Så här svartlistar du en enskild enhet i en registreringsgrupp:

1. Logga in på Azure-portalen och välj **Alla resurser** på den vänstra menyn.
2. Välj den etableringstjänst som innehåller registreringsgruppen för den enhet som du vill svartlista i listan över resurser.
3. I etableringstjänsten väljer du **Hantera registreringar**och väljer sedan fliken **Enskilda registreringar.**
4. Välj knappen **Lägg till individuell registrering** högst upp. 
5. På sidan **Lägg till registrering** väljer du **X.509** som attestationmekanism för enheten. **Mechanism**

    Ladda upp enhetscertifikatet och ange enhets-ID:t för den enhet som ska svartlistas. Använd det signerade slutentitetscertifikat som är installerat på enheten för certifikatet. Enheten använder det signerade slutentitetscertifikatet för autentisering.

    ![Ange enhetsegenskaper för den svartlistade enheten](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Bläddra längst ned på sidan **Lägg till registrering** och välj **Inaktivera** på **växeln Aktivera** och välj sedan **Spara**. 

    [![Använd inaktiverad enskild registreringspost för att inaktivera enhet från gruppregistrering i portalen](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

När du har skapat registreringen bör du se registreringen av den inaktiverade enheten på fliken **Enskilda registreringar.** 

## <a name="next-steps"></a>Nästa steg

Avanmälning är också en del av den större avetableringsprocessen. Avetablera en enhet omfattar både avregistrering från etableringstjänsten och avregistrering från IoT-hubben. Mer information om hela processen finns i [Så här avetableras enheter som tidigare har etablerats automatiskt](how-to-unprovision-devices.md) 

