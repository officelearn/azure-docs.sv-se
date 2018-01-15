---
title: "Hantera Enhetsåtkomst för Azure IoT Hub etablering av tjänst | Microsoft Docs"
description: "Hur du återkalla Enhetsåtkomst till din DP-tjänst i Azure Portal"
services: iot-dps
keywords: 
author: JimacoMS
ms.author: v-jamebr
ms.date: 12/22/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 6f1dad648b228163219c8f722eed3897f4ba4d22
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="how-to-revoke-device-access-to-your-provisioning-service-in-the-azure-portal"></a>Hur du återkalla Enhetsåtkomst till din etablering tjänst i Azure Portal

Rätt hantering av enheten autentiseringsuppgifter är avgörande för hög profil system som IoT-lösningar. Bästa praxis för sådana system är att ha en tydlig plan på hur du återkalla åtkomst för enheter i fall där sina autentiseringsuppgifter, om en SAS-token eller ett X.509-certifikat kan ha skadats. Den här artikeln beskriver hur du återkalla Enhetsåtkomst etablering steget.

Mer information om att återkalla Enhetsåtkomst till en IoT-hubb när enheten har etablerats. Se [inaktivera enheter](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices).

> [!NOTE] 
> Tänk på återförsöksprincip för enheter som du har återkallat åtkomst för. Exempelvis kan en enhet med en oändlig återförsöksprincip kontinuerligt försök att registrera med tjänsten etablering, förbrukar resurser och eventuellt påverka prestanda.

## <a name="blacklist-devices-with-an-individual-enrollment-entry"></a>Svartlistat enheter med en post i enskilda registrering

Enskilda registreringar gäller för en enskild enhet och kan använda X.509-certifikat eller SAS-token (i TPM verkliga eller virtuella) som metod för attestering. (-Enheter som använder SAS-token som deras attestering mekanism kan endast etableras via en enskild registrering.) För att svartlista en enhet som har en enskild registrering, kan du inaktivera eller ta bort posten registrering: 

- För att tillfälligt svartlista enheten, kan du inaktivera registrering av posten. 

    1. Logga in på Azure portal och klicka på **alla resurser** i den vänstra menyn.
    2. Klicka på etablering tjänsten som du vill svartlista enheten i listan över resurser.
    3. I din etablering tjänst klickar du på **hantera registreringar**och välj **enskilda registreringar** fliken.
    4. Klicka på posten för registrering för den enhet du vill svartlista för att öppna den. 
    5. Klicka på **inaktivera** längst ned i listpost registrering Klicka **spara**.  

        ![Inaktivera enskilda registrering post i portalen](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)
    
- Du kan ta bort posten registrering för att svartlista permanent enheten.

    1. Logga in på Azure portal och klicka på **alla resurser** i den vänstra menyn.
    2. Klicka på etablering tjänsten som du vill svartlista enheten i listan över resurser.
    3. I din etablering tjänst klickar du på **hantera registreringar**och välj **enskilda registreringar** fliken.
    4. Markera kryssrutan bredvid posten registreringen för enheten som du vill svartlista. 
    5. Klicka på **ta bort** längst upp i fönstret klicka **Ja** att bekräfta att du vill ta bort registreringen. 

        ![Ta bort enskilda registrering post i portalen](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
    6. När åtgärden har slutförts visas posten tas bort från listan över enskilda registreringar.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-using-an-enrollment-group"></a>Svartlista ett X.509 mellanliggande eller rotcertifikatutfärdarens certifikat med hjälp av en grupp för registrering

X.509-certifikat är vanligtvis ordnade i en certifikatkedja med förtroenden. Om ett certifikat när som helst i en kedja komprometterats förtroendet har brutits certifikatet måste vara övriga för att förhindra att enheter ned dataström i kedja som innehåller certifikatet från tillhandahålls av tjänsten etablering enhet Läs mer om X.509-certifikat och hur de används med tjänsten etablering i [X.509-certifikat](./concepts-security.md#x509-certificates). 

En grupp för registrering är en post för enheter som delar en gemensam attestering mekanismen för X.509-certifikat som signerats av mellanliggande samma eller rotcertifikatutfärdare. Registrering grupp posten har konfigurerats med X.509-certifikat som är associerade med mellanliggande eller rot-CA, samt eventuella configuration värden, till exempel dubbla tillstånd och IoT-hubbanslutning som delas av enheter med certifikatet i sina certifikat kedja. För att svartlista certifikatet, kan du inaktivera eller ta bort dess registrering grupp:

- För att tillfälligt svartlista certifikatet, kan du inaktivera gruppen, registrering. 

    1. Logga in på Azure portal och klicka på **alla resurser** i den vänstra menyn.
    2. Klicka på etablering tjänsten som du vill svartlista signeringscertifikatet från i listan över resurser.
    3. I din etablering tjänst klickar du på **hantera registreringar**och välj **registrering grupper** fliken.
    4. Klicka på gruppen registrering för certifikat som du vill att svartlista för att öppna den.
    5. Klicka på **redigera grupp** längst upp till vänster i ämnesgruppen registrering.
    6. För att inaktivera registrering posten väljer **inaktivera** på den **aktivera** växla och klicka sedan på **spara**.  

        ![Inaktivera registrering grupp post i portalen](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
- För att svartlista permanent certifikatet, du kan ta bort gruppen, registrering.

    1. Logga in på Azure portal och klicka på **alla resurser** i den vänstra menyn.
    2. Klicka på etablering tjänsten som du vill svartlista enheten i listan över resurser.
    3. I din etablering tjänst klickar du på **hantera registreringar**och välj **registrering grupper** fliken.
    4. Markera kryssrutan bredvid gruppen registrering för certifikat som du vill att svartlista. 
    5. Klicka på **ta bort** längst upp i fönstret klicka **Ja** att bekräfta att du vill ta bort gruppen registrering. 

        ![Ta bort registreringen grupp post i portalen](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

    6. När åtgärden har slutförts visas posten tas bort från listan över grupper för registrering.  

> [!NOTE]
> Om du tar bort en grupp för registrering för ett certifikat för att enheter som har certifikatet i sina certifikatkedja fortfarande kunna registrera om en aktiverad registrering grupp för rotcertifikatet eller en annan mellanliggande certifikat högre upp i sina certifikat kedjan finns.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Svartlistat specifika enheter i en grupp för registrering

Enheter som implementerar mekanismen för attestering X.509 använder enhetens certifikatkedja och privat nyckel för att autentisera. När en enhet ansluter och autentiserar med etablering av tjänst, letar först tjänsten för en enskild registrering matchar enhetens autentiseringsuppgifter innan du söker registrering grupper för att fastställa om enheten kan etableras. Om tjänsten hittar en inaktiverad enskilda registrering för enhetens, förhindrar det att enheten ansluter, även om det finns en aktiverad registrering grupp för ett mellanliggande eller rot-CA i enhetens certifikatkedja. Följ dessa steg för att svartlista en enskild enhet i en grupp för registrering:

1. Logga in på Azure portal och klicka på **alla resurser** från den vänstra menyn.
2. Klicka på tjänsten allokering som innehåller gruppen registreringen för den enhet du vill svartlista från listan över resurser.
3. I din etablering tjänst klickar du på **hantera registreringar**och välj **enskilda registreringar** fliken.
4. Klicka på den **Lägg till** längst upp. 
5. Välj **X.509** som säkerhetsmekanism för enheten och överför certifikat för enheten. Det här är den signerade slutenhet vilket certifikat installeras på enheten som används för att generera certifikat för autentisering.
6. Ange den **IoT-hubb enhets-ID** för enheten. 
7. För att inaktivera registrering posten väljer **inaktivera** på den **aktivera** växla. 
8. Klicka på **Spara**. På har skapats för din registrering, bör du se enheten visas under den **enskilda registreringar** fliken. 

    ![Inaktivera enskilda registrering post i portalen](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)




