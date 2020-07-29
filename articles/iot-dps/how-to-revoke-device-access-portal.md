---
title: Avregistrera enheten från Azure IoT Hub Device Provisioning Service
description: Så här avregistrerar du en enhet för att förhindra etablering via Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: af883da67f4e1bc819514e88ff480526e16124db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "74974929"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Så här avregistrerar du en enhet från Azure IoT Hub Device Provisioning Service

Korrekt hantering av autentiseringsuppgifter för enheten är avgörande för system med hög profil som IoT-lösningar. Bästa praxis för sådana system är att ha en tydlig plan för hur du återkallar åtkomst för enheter när deras autentiseringsuppgifter, om en SAS-token (signatur för delad åtkomst) eller ett X. 509-certifikat kan ha komprometterats. 

Registrering i enhets etablerings tjänsten gör det möjligt att tillhandahålla en enhet [automatiskt](concepts-auto-provisioning.md). En etablerad enhet är en som har registrerats med IoT Hub, vilket gör att den kan ta emot sin ursprungliga [enhet med dubbla](~/articles/iot-hub/iot-hub-devguide-device-twins.md) tillstånd och börja rapportera telemetridata. Den här artikeln beskriver hur du avregistrerar en enhet från din etablerings tjänst instans, vilket gör att den inte kan tillhandahållas igen i framtiden.

> [!NOTE] 
> Var medveten om principen för återförsök för enheter som du återkallar åtkomst för. Till exempel kan en enhet som har en oändlig återförsöks princip kontinuerligt försöka registrera med etablerings tjänsten. Den situationen förbrukar tjänst resurser och kan påverka prestandan.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Black-enheter med hjälp av en individuell registrerings post

Enskilda registreringar gäller för en enskild enhet och kan använda antingen X. 509-certifikat eller SAS-token (i en verklig eller virtuell TPM) som mekanism för attestering. (Enheter som använder SAS-token som bestyrkande mekanism kan endast tillhandahållas via en enskild registrering.) Om du vill svartlista en enhet som har en enskild registrering kan du antingen inaktivera eller ta bort registrerings posten. 

För att tillfälligt svartlista enheten genom att inaktivera registrerings posten: 

1. Logga in på Azure Portal och välj **alla resurser** på den vänstra menyn.
2. I listan över resurser väljer du den etablerings tjänst som du vill svartlista din enhet från.
3. I etablerings tjänsten väljer du **Hantera registreringar**och väljer sedan fliken **enskilda registreringar** .
4. Välj registrerings posten för den enhet som du vill Black. 

    ![Välj en individuell registrering](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. Rulla längst ned på sidan registrering och välj **inaktivera** för växeln **Aktivera post** och välj sedan **Spara**.  

   ![Inaktivera enskilda registrerings poster i portalen](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Om du vill svartlista enheten permanent genom att ta bort dess registrerings post:

1. Logga in på Azure Portal och välj **alla resurser** på den vänstra menyn.
2. I listan över resurser väljer du den etablerings tjänst som du vill svartlista din enhet från.
3. I etablerings tjänsten väljer du **Hantera registreringar**och väljer sedan fliken **enskilda registreringar** .
4. Markera kryss rutan bredvid registrerings posten för den enhet som du vill Black. 
5. Välj **ta bort** överst i fönstret och välj sedan **Ja** för att bekräfta att du vill ta bort registreringen. 

   ![Ta bort enskilda registrerings poster i portalen](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


När du har slutfört proceduren bör du se att posten tas bort från listan över enskilda registreringar.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Svartlista ett X. 509-mellanliggande certifikat eller rotcertifikatutfärdarcertifikat med hjälp av en registrerings grupp

X. 509-certifikat ordnas vanligt vis i en certifikat kedja med förtroende. Om ett certifikat vid något Stadium i en kedja blir komprometterat bryts förtroendet. Certifikatet måste vara svartlistat för att det ska gå att förhindra att enhets etablerings tjänsten från etablering av enheter visas i en kedja som innehåller certifikatet. Mer information om X. 509-certifikat och hur de används med etablerings tjänsten finns i [X. 509-certifikat](./concepts-security.md#x509-certificates). 

En registrerings grupp är en post för enheter som delar en gemensam mekanism för attestering av X. 509-certifikat signerade av samma mellanliggande eller rot certifikat utfärdare. Posten för registrerings gruppen har kon figurer ATS med det X. 509-certifikat som är associerat med mellanliggande eller rot certifikat utfärdare. Posten konfigureras också med konfigurations värden, till exempel dubbla tillstånd och IoT Hub-anslutning, som delas av enheter med certifikatet i certifikat kedjan. Om du vill svartlista certifikatet kan du antingen inaktivera eller ta bort registrerings gruppen.

För att tillfälligt svartlista certifikatet genom att inaktivera dess registrerings grupp: 

1. Logga in på Azure Portal och välj **alla resurser** på den vänstra menyn.
2. I listan över resurser väljer du den etablerings tjänst som du vill svartlista signerings certifikatet från.
3. I etablerings tjänsten väljer du **Hantera registreringar**och väljer sedan fliken **registrerings grupper** .
4. Välj registrerings gruppen med det certifikat som du vill Black.
5. Välj **inaktivera** på växeln **Aktivera post** och välj sedan **Spara**.  

   ![Inaktivera registreringen av registrerings gruppen i portalen](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Om du vill svartlista certifikatet permanent genom att ta bort dess registrerings grupp:

1. Logga in på Azure Portal och välj **alla resurser** på den vänstra menyn.
2. I listan över resurser väljer du den etablerings tjänst som du vill svartlista din enhet från.
3. I etablerings tjänsten väljer du **Hantera registreringar**och väljer sedan fliken **registrerings grupper** .
4. Markera kryss rutan bredvid registrerings gruppen för det certifikat som du vill Black. 
5. Välj **ta bort** överst i fönstret och välj sedan **Ja** för att bekräfta att du vill ta bort registrerings gruppen. 

   ![Ta bort posten för registrerings gruppen i portalen](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

När du har slutfört proceduren bör du se att posten tas bort från listan över registrerings grupper.  

> [!NOTE]
> Om du tar bort en registrerings grupp för ett certifikat kan enheter som har certifikatet i certifikat kedjan fortfarande kunna registrera om en aktive rad registrerings grupp för rot certifikatet eller ett annat mellanliggande certifikat högre upp i certifikat kedjan finns.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Black-/regionsspecifika enheter i en registrerings grupp

Enheter som implementerar mekanismen för 509-attestering använder enhetens certifikat kedja och privat nyckel för att autentisera. När en enhet ansluter och autentiserar med enhets etablerings tjänsten söker tjänsten först efter en enskild registrering som matchar enhetens autentiseringsuppgifter. Tjänsten söker sedan efter registrerings grupper för att avgöra om enheten kan tillhandahållas. Om tjänsten hittar en inaktive rad enskild registrering för enheten förhindrar den att enheten ansluter. Tjänsten förhindrar anslutningen även om en aktive rad registrerings grupp för en mellanliggande eller rot certifikat utfärdare finns i enhetens certifikat kedja. 

Följ dessa steg om du vill svartlista en enskild enhet i en registrerings grupp:

1. Logga in på Azure Portal och välj **alla resurser** på den vänstra menyn.
2. I listan över resurser väljer du etablerings tjänsten som innehåller registrerings gruppen för den enhet som du vill Black.
3. I etablerings tjänsten väljer du **Hantera registreringar**och väljer sedan fliken **enskilda registreringar** .
4. Välj knappen **Lägg till individuell registrering** överst. 
5. På sidan **Lägg till registrering** väljer du **X. 509** som **mekanism** för attestering för enheten.

    Ladda upp enhetens certifikat och ange enhets-ID för enheten som ska svartlistas. För certifikatet använder du det signerade slutenhets certifikatet som är installerat på enheten. Enheten använder det signerade slut enhets certifikatet för autentisering.

    ![Ange enhets egenskaper för den svartlistade enheten](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Rulla längst ned på sidan **Lägg till registrering** och välj **inaktivera** på växeln **Aktivera post** och välj sedan **Spara**. 

    [![Använd inaktiverad individuell registrerings post för att inaktivera enhet från grupp registrering i portalen](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

När du har skapat registreringen bör du se din inaktiverade enhets registrering på fliken **enskilda registreringar** . 

## <a name="next-steps"></a>Nästa steg

Avregistrering är också en del av den större avetablerings processen. Avetablering av en enhet omfattar både avregistrering från etablerings tjänsten och avregistrering från IoT Hub. Läs mer om den fullständiga processen i [så här avetablerar du enheter som tidigare var automatiskt etablerade](how-to-unprovision-devices.md) 

