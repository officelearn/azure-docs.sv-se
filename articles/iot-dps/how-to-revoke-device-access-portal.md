---
title: Hur du disenroll en enhet från Azure IoT Hub Device Provisioning-tjänsten
description: Hur du disenroll en enhet för att förhindra etablering via Azure IoT Hub Device Provisioning-tjänsten
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: e66d896a7df48645dad39b5b978c4f7c2f8d8cb9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954559"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Hur du disenroll en enhet från Azure IoT Hub Device Provisioning-tjänsten

Korrekt hantering av enhetens autentiseringsuppgifter är avgörande för viktiga system som IoT-lösningar. Bästa praxis för sådana system är att ha en tydlig hur att återkalla åtkomst för enheter när sina autentiseringsuppgifter, om en token för delad åtkomst-signaturer (SAS) eller ett X.509-certifikat kan komprometteras. 

Registrering i Device Provisioning-tjänsten gör det möjligt för en enhet ska vara [Automatisk etablering](concepts-auto-provisioning.md). En etablerad enhet är ett program som har registrerats med IoT Hub, vilket gör att den tar emot första [enhetstvillingen](~/articles/iot-hub/iot-hub-devguide-device-twins.md) tillstånd och börja reporting telemetridata. Den här artikeln beskriver hur du disenroll en enhet från din etablering tjänstinstans som förhindrar att det håller på att etableras igen i framtiden.

> [!NOTE] 
> Tänk på återförsöksprincipen för enheter som du återkalla åtkomst för. Till exempel kan en enhet som har en oändlig återförsöksprincip kontinuerligt försöka registrera med etableringstjänsten. Denna situation förbrukar resurser och eventuellt påverkar prestanda.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Svartlistat enheter med hjälp av en individuell registreringspost

Enskilda registreringar gäller för en enskild enhet och kan använda X.509-certifikat eller SAS-token (i en verklig eller virtuell TPM) som attesteringsmekanism. (Enheter som använder SAS-token som kan du etablera sina attesteringsmetod endast via en enskild registrering.) För att blockeringslista en enhet som har en enskild registrering, kan du inaktivera eller ta bort registreringsposten. 

Tillfälligt blockeringslista enheten genom att inaktivera dess registreringspost: 

1. Logga in på Azure portal och väljer **alla resurser** menyn till vänster.
2. Välj etableringstjänsten som du vill blockeringslista enheten från i listan över resurser.
3. Välj i etableringstjänsten **hantera registreringar**, och välj sedan den **enskilda registreringar** fliken.
4. Välj posten registreringen för den enhet som du vill blockeringslista. 

    ![Välj din enskild registrering](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. Bläddra längst ned på sidan med registrering och välj **inaktivera** för den **aktivera post** växla och markera **spara**.  

   ![Inaktivera posten för enskild registrering i portalen](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Att blockeringslista enheten permanent genom att ta bort registreringsposten:

1. Logga in på Azure portal och väljer **alla resurser** menyn till vänster.
2. Välj etableringstjänsten som du vill blockeringslista enheten från i listan över resurser.
3. Välj i etableringstjänsten **hantera registreringar**, och välj sedan den **enskilda registreringar** fliken.
4. Markera kryssrutan bredvid registreringspost för den enhet som du vill blockeringslista. 
5. Välj **ta bort** högst upp i fönstret och välj sedan **Ja** att bekräfta att du vill ta bort registreringen. 

   ![Ta bort posten för enskild registrering i portalen](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


När du har slutfört proceduren bör du se ditt bidrag som tagits bort från listan över enskilda registreringar.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Blockeringslista ett mellanliggande X.509-certifikat eller ett rotcertifikatutfärdarcertifikat med hjälp av en grupp för registrering

X.509-certifikat som är normalt ordnade i en certifikatkedja med förtroenden. Om ett certifikat för varje steg på en kedja har komprometterats, har förtroendet brutits. Certifikatet måste vara Svartlistad förhindra etablering enheter nedströms i kedjor som innehåller certifikatet Device Provisioning-tjänsten. Läs mer om X.509-certifikat och hur de används med etableringstjänsten i [X.509-certifikat](./concepts-security.md#x509-certificates). 

En grupp för registrering är en post för enheter som delar en gemensam attesteringsmetod av X.509-certifikat som signerats av mellanliggande samma eller rotcertifikatutfärdare. Posten för registreringsgruppen har konfigurerats med X.509-certifikat som är associerade med mellanliggande eller rotcertifikatutfärdare. Posten konfigureras också med valfri konfigurationsvärden, till exempel tvillingstatus och IoT hub-anslutning som delas av enheter med detta certifikat i certifikatkedjan. För att blockeringslista certifikatet, kan du inaktivera eller ta bort gruppen, registrering.

Tillfälligt blockeringslista certifikatet genom att inaktivera dess grupp för registrering: 

1. Logga in på Azure portal och väljer **alla resurser** menyn till vänster.
2. Välj etableringstjänsten som du vill blockeringslista signeringscertifikatet från i listan över resurser.
3. Välj i etableringstjänsten **hantera registreringar**, och välj sedan den **Registreringsgrupper** fliken.
4. Välj grupp för registrering med det certifikat som du vill blockeringslista.
5. Välj **inaktivera** på den **aktivera post** växla och markera **spara**.  

   ![Inaktivera registreringsposten för gruppen i portalen](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Att blockeringslista permanent certifikatet genom att ta bort grupp för registrering:

1. Logga in på Azure portal och väljer **alla resurser** menyn till vänster.
2. Välj etableringstjänsten som du vill blockeringslista enheten från i listan över resurser.
3. Välj i etableringstjänsten **hantera registreringar**, och välj sedan den **Registreringsgrupper** fliken.
4. Markera gruppen registrering för det certifikat som du vill blockeringslista. 
5. Välj **ta bort** högst upp i fönstret och välj sedan **Ja** att bekräfta att du vill ta bort grupp för registrering. 

   ![Ta bort post för registreringsgruppen i portalen](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

När du har slutfört proceduren bör du se ditt bidrag som tagits bort från listan med registreringsgrupper.  

> [!NOTE]
> Om du tar bort en registreringsgrupp för ett certifikat för kan enheter som har certifikat i certifikatkedjan fortfarande att kunna registrera om en aktiverad registreringsgrupp för rotcertifikatet eller en annan mellanliggande högre upp i sina certifikat Det finns en kedja.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Svartlistat specifika enheter i en grupp för registrering

Enheter som implementerar X.509-attesteringsmetod använda enhetens certifikatkedjan och den privata nyckeln för att autentisera. När en enhet ansluter och autentiserar med Device Provisioning-tjänsten, letar tjänsten först efter en enskild registrering som matchar enhetens autentiseringsuppgifter. Tjänsten söker sedan registreringsgrupper för att avgöra om enheten kan etableras. Om tjänsten hittar en inaktiverad enskild registrering för enheten, kan enheten inte ansluta. Tjänsten förhindrar anslutningen även om det finns en aktiverad registreringsgrupp för ett mellanliggande certifikat eller rot-CA i enhetens certifikatkedjan. 

Följ dessa steg för att blockeringslista en enskild enhet i en grupp för registrering:

1. Logga in på Azure portal och väljer **alla resurser** menyn till vänster.
2. Välj etableringstjänsten som innehåller grupp för registrering för den enhet som du vill blockeringslista från listan över resurser.
3. Välj i etableringstjänsten **hantera registreringar**, och välj sedan den **enskilda registreringar** fliken.
4. Välj den **Lägg till enskild registrering** längst upp. 
5. På den **Lägg till registrering** väljer **X.509** som bestyrkande **mekanism** för enheten.

    Ladda upp enhetens certifikat och ange enhets-ID för enheten att vara Svartlistad. Använda signerade slutenhet certifikat har installerats på enheten för certifikatet. Enheten använder den signerade slutentitetscertifikat för autentisering.

    ![Ange egenskaper för enhet för svartlistade enheten](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Bläddra till slutet av den **Lägg till registrering** och välj **inaktivera** på den **aktivera post** växla och markera **spara**. 

    [![Använd inaktiverad individuell registreringspost för att inaktivera enheten från gruppregistrering i portalen](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

När du har skapat din registrering, bör du se din inaktiverad registrering visas på den **enskilda registreringar** fliken. 

## <a name="next-steps"></a>Nästa steg

Avregistrering ingår också i större avetableringsprocessen. Avetablering av en enhet innehåller både avregistrering från provisioning-tjänst och deregistering från IoT hub. Mer information om hela processen, se [så ta bort etableringen av enheter som har tidigare Automatisk etablering](how-to-unprovision-devices.md) 

