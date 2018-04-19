---
title: Hur du disenroll en enhet från Azure IoT Hub etablering av tjänst
description: Hur du disenroll en enhet för att förhindra etablering via Azure IoT Hub etablering av tjänst
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-jamebr;bryanla
ms.date: 04/05/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 50074eaecacf603d2bc6170183fd632b4a1ab2d1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Hur du disenroll en enhet från Azure IoT Hub etablering av tjänst

Rätt hantering av enheten autentiseringsuppgifter är avgörande för hög profil system som IoT-lösningar. Bästa praxis för sådana system är att ha en tydlig plan för hur du återkalla åtkomst för enheter när sina autentiseringsuppgifter, om en token för delad åtkomst (SAS)-signaturer eller ett X.509-certifikat är utsatt för risk. 

Registreringen i tjänsten etablering enhet gör att en enhet ska vara [automatiskt etablerade](concepts-auto-provisioning.md). En etablerad enhet är ett som har registrerats med IoT-hubben, så att den kan ta emot första [enheten dubbla](~/articles/iot-hub/iot-hub-devguide-device-twins.md) tillstånd och börja reporting telemetridata. Den här artikeln beskriver hur du disenroll en enhet från din etablering tjänstinstans förhindrar etablerats igen i framtiden.

> [!NOTE] 
> Tänk på återförsöksprincip för enheter som du har återkallat åtkomst för. Exempelvis kan en enhet som har en oändlig återförsöksprincip kontinuerligt försök att registrera med tjänsten etablering. Denna situation förbrukar resurser och eventuellt påverkar prestanda.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Svartlistat enheter med hjälp av en enskild registrering-post

Enskilda registreringar gäller för en enskild enhet och kan använda antingen X.509-certifikat eller SAS-token (i TPM verkliga eller virtuella) som metod för attestering. (-Enheter som använder SAS-token som deras attestering mekanism kan etableras endast via en enskild registrering.) För att blockeringslista en enhet som har en enskild registrering, kan du inaktivera eller ta bort registreringsposten. 

Tillfälligt svartlista enheten genom att inaktivera trätt registrering: 

1. Logga in på Azure portal och välj **alla resurser** i den vänstra menyn.
2. Välj tjänsten allokering som du vill svartlista enheten från i listan över resurser.
3. I din etablering tjänst väljer **hantera registreringar**, och välj sedan den **enskilda registreringar** fliken.
4. Välj posten registreringen för den enhet som du vill blockeringslista. 
5. Rulla ned och välj **inaktivera** på den **aktivera** växla och markera **spara**.  

   [![Inaktivera enskilda registrering post i portalen](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png#lightbox)  

Att blockeringslista enheten permanent genom att ta bort registreringsposten:

1. Logga in på Azure portal och välj **alla resurser** i den vänstra menyn.
2. Välj tjänsten allokering som du vill svartlista enheten från i listan över resurser.
3. I din etablering tjänst väljer **hantera registreringar**, och välj sedan den **enskilda registreringar** fliken.
4. Markera kryssrutan bredvid posten registreringen för den enhet som du vill svartlista. 
5. Välj **ta bort** överst i fönstret och välj sedan **Ja** att bekräfta att du vill ta bort registreringen. 

   ![Ta bort enskilda registrering post i portalen](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
När du är klar med proceduren bör du se posten tas bort från listan över enskilda registreringar.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Svartlista ett X.509 mellanliggande eller rotcertifikatutfärdarens certifikat med hjälp av en grupp för registrering

X.509-certifikat är vanligtvis ordnade i en certifikatkedja med förtroenden. Om ett certifikat när som helst i en kedja komprometterats har förtroendet brutits. Certifikatet måste vara övriga för att förhindra att enheten Etableringstjänsten etablering enheter nedströms i kedja som innehåller certifikatet. Läs mer om X.509-certifikat och hur de används med tjänsten etablering i [X.509-certifikat](./concepts-security.md#x509-certificates). 

En grupp för registrering är en post för enheter som delar en gemensam attestering mekanismen för X.509-certifikat som signerats av mellanliggande samma eller rotcertifikatutfärdare. Registrering grupp posten har konfigurerats med X.509-certifikat som är associerade med mellanliggande eller rotcertifikatutfärdare. Transaktionen är också konfigurerad med någon konfigurationsvärden, till exempel dubbla tillstånd och IoT-hubbanslutning som delas av enheter med certifikatet i sina certifikatkedja. För att blockeringslista certifikatet, kan du inaktivera eller ta bort gruppen, registrering.

Tillfälligt svartlista certifikatet genom att inaktivera gruppen, registrering: 

1. Logga in på Azure portal och välj **alla resurser** i den vänstra menyn.
2. Välj tjänsten allokering som du vill svartlista signeringscertifikat från i listan över resurser.
3. I din etablering tjänst väljer **hantera registreringar**, och välj sedan den **registrering grupper** fliken.
4. Markera gruppen registrering med det certifikat som du vill svartlista.
5. Välj **inaktivera** på den **aktivera** växla och markera **spara**.  

   ![Inaktivera registrering grupp post i portalen](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Att svartlista permanent certifikatet genom att ta bort dess registrering grupp:

1. Logga in på Azure portal och välj **alla resurser** i den vänstra menyn.
2. Välj tjänsten allokering som du vill svartlista enheten från i listan över resurser.
3. I din etablering tjänst väljer **hantera registreringar**, och välj sedan den **registrering grupper** fliken.
4. Markera gruppen registrering för det certifikat som du vill blockeringslista. 
5. Välj **ta bort** överst i fönstret och välj sedan **Ja** att bekräfta att du vill ta bort gruppen registrering. 

   ![Ta bort registreringen grupp post i portalen](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

När du är klar med proceduren bör du se posten tas bort från listan över grupper för registrering.  

> [!NOTE]
> Om du tar bort en grupp för registrering för ett certifikat för kan enheter som har certifikat i sina certifikatkedja fortfarande att kunna registrera om en aktiverad registrering grupp för rotcertifikatet eller en annan mellanliggande certifikat högre upp i sina certifikat kedjan finns.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Svartlistat specifika enheter i en grupp för registrering

Enheter som implementerar mekanismen för attestering X.509 använder enhetens certifikatkedja och privat nyckel för att autentisera. När en enhet ansluter och autentiserar med etablering av tjänst, efter tjänsten först en enskild registrering som matchar enhetens autentiseringsuppgifter. Tjänsten söker sedan registrering grupper för att fastställa om enheten kan etableras. Om tjänsten hittar en inaktiverad enskilda registrering för enhetens, kan enheten inte ansluta. Tjänsten förhindrar anslutningen även om det finns en aktiverad registrering grupp för ett mellanliggande eller rot-CA i enhetens certifikatkedja. 

Följ dessa steg för att svartlista en enskild enhet i en grupp för registrering:

1. Logga in på Azure portal och välj **alla resurser** i den vänstra menyn.
2. Välj tjänsten allokering som innehåller gruppen registreringen för den enhet som du vill svartlista från listan över resurser.
3. I din etablering tjänst väljer **hantera registreringar**, och välj sedan den **enskilda registreringar** fliken.
4. Välj den **Lägg till** längst upp. 
5. Välj **X.509** som metod för hälsoattestering för enheten och överför enhetens certifikat. Det här är den signerade slutenhet vilket certifikat installerat på enheten. Enheten används för att generera certifikat för autentisering.
6. För **IoT-hubb enhets-ID**, ange ID för enheten. 
7. Välj **inaktivera** på den **aktivera** växla och markera **spara**. 

    [![Använd inaktiverad enskilda registrering post att inaktivera enheten från gruppen registrering i portalen](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

När du skapar har din registrering, visas på enheten ska visas i **enskilda registreringar** fliken.

## <a name="next-steps"></a>Nästa steg

Disenrollment är också en del av större avetableringsprocessen. Borttagning av en enhet innehåller både disenrollment från etablering av tjänst och deregistering från IoT-hubb. Mer information om hela processen, se [så ta bort etableringen av enheter som tidigare har etablerats för automatisk](how-to-unprovision-devices.md) 

