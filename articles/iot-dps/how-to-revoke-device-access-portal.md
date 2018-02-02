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
ms.openlocfilehash: 12aebf3a56aa7469a765ab6fc67aa65b254db71a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="revoke-device-access-to-your-provisioning-service-in-the-azure-portal"></a>Återkalla Enhetsåtkomst till din etablering tjänst i Azure-portalen

Rätt hantering av enheten autentiseringsuppgifter är avgörande för hög profil system som IoT-lösningar. Bästa praxis för sådana system är att ha en tydlig plan för hur du återkalla åtkomst för enheter när sina autentiseringsuppgifter, om en token för delad åtkomst (SAS)-signaturer eller ett X.509-certifikat är utsatt för risk. Den här artikeln beskriver hur du återkalla Enhetsåtkomst etablering steget.

Läs om återkalla Enhetsåtkomst till en IoT-hubb när enheten har etablerats i [inaktivera enheter med](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices).

> [!NOTE] 
> Tänk på återförsöksprincip för enheter som du har återkallat åtkomst för. Exempelvis kan en enhet som har en oändlig återförsöksprincip kontinuerligt försök att registrera med tjänsten etablering. Denna situation förbrukar resurser och eventuellt påverkar prestanda.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Svartlistat enheter med hjälp av en enskild registrering-post

Enskilda registreringar gäller för en enskild enhet och kan använda antingen X.509-certifikat eller SAS-token (i TPM verkliga eller virtuella) som metod för attestering. (-Enheter som använder SAS-token som deras attestering mekanism kan etableras endast via en enskild registrering.) För att svartlista en enhet som har en enskild registrering, kan du inaktivera eller ta bort posten registrering. 

Tillfälligt svartlista enheten genom att inaktivera trätt registrering: 

1. Logga in på Azure portal och välj **alla resurser** i den vänstra menyn.
2. Välj tjänsten allokering som du vill svartlista enheten från i listan över resurser.
3. I din etablering tjänst väljer **hantera registreringar**, och välj sedan den **enskilda registreringar** fliken.
4. Välj posten registreringen för den enhet som du vill svartlista. 
5. Välj **inaktivera** på den **aktivera** växla och markera **spara**.  

   ![Inaktivera enskilda registrering post i portalen](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)
    
Att svartlista permanent enheten genom att ta bort posten registrering:

1. Logga in på Azure portal och välj **alla resurser** i den vänstra menyn.
2. Välj tjänsten allokering som du vill svartlista enheten från i listan över resurser.
3. I din etablering tjänst väljer **hantera registreringar**, och välj sedan den **enskilda registreringar** fliken.
4. Markera kryssrutan bredvid posten registreringen för den enhet som du vill svartlista. 
5. Välj **ta bort** överst i fönstret och välj sedan **Ja** att bekräfta att du vill ta bort registreringen. 

   ![Ta bort enskilda registrering post i portalen](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
När du är klar med proceduren bör du se posten tas bort från listan över enskilda registreringar.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Svartlista ett X.509 mellanliggande eller rotcertifikatutfärdarens certifikat med hjälp av en grupp för registrering

X.509-certifikat är vanligtvis ordnade i en certifikatkedja med förtroenden. Om ett certifikat när som helst i en kedja komprometterats har förtroendet brutits. Certifikatet måste vara övriga för att förhindra att enheten Etableringstjänsten etablering enheter nedströms i kedja som innehåller certifikatet. Läs mer om X.509-certifikat och hur de används med tjänsten etablering i [X.509-certifikat](./concepts-security.md#x509-certificates). 

En grupp för registrering är en post för enheter som delar en gemensam attestering mekanismen för X.509-certifikat som signerats av mellanliggande samma eller rotcertifikatutfärdare. Registrering grupp posten har konfigurerats med X.509-certifikat som är associerade med mellanliggande eller rotcertifikatutfärdare. Transaktionen är också konfigurerad med någon konfigurationsvärden, till exempel dubbla tillstånd och IoT-hubbanslutning som delas av enheter med certifikatet i sina certifikatkedja. För att svartlista certifikatet, kan du inaktivera eller ta bort gruppen, registrering.

Tillfälligt svartlista certifikatet genom att inaktivera gruppen, registrering: 

1. Logga in på Azure portal och välj **alla resurser** i den vänstra menyn.
2. Välj tjänsten allokering som du vill svartlista signeringscertifikat från i listan över resurser.
3. I din etablering tjänst väljer **hantera registreringar**, och välj sedan den **registrering grupper** fliken.
4. Markera gruppen registrering för det certifikat som du vill svartlista.
5. Välj i ämnesgruppen registrering **redigera grupp**.
6. Välj **inaktivera** på den **aktivera** växla och markera **spara**.  

   ![Inaktivera registrering grupp post i portalen](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Att svartlista permanent certifikatet genom att ta bort dess registrering grupp:

1. Logga in på Azure portal och välj **alla resurser** i den vänstra menyn.
2. Välj tjänsten allokering som du vill svartlista enheten från i listan över resurser.
3. I din etablering tjänst väljer **hantera registreringar**, och välj sedan den **registrering grupper** fliken.
4. Markera kryssrutan bredvid gruppen registrering för det certifikat som du vill svartlista. 
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
5. Välj **X.509** som säkerhetsmekanism för enheten och överför certifikat för enheten. Det här är den signerade slutenhet vilket certifikat installerat på enheten. Enheten används för att generera certifikat för autentisering.
6. För **IoT-hubb enhets-ID**, ange ID för enheten. 
7. Välj **inaktivera** på den **aktivera** växla och markera **spara**. 

   ![Inaktivera enskilda registrering post i portalen](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

När du skapar har din registrering, visas på enheten ska visas i **enskilda registreringar** fliken.


