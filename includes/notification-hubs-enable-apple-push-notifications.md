---
title: ta med fil
description: ta med fil
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2fc4f26f187301ea7a7a1e3051038f75da728547
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60426683"
---
## <a name="generate-the-certificate-signing-request-file"></a>Generera filen för begäran om certifikatsignering

APNS (Apple Push Notification Service) använder sig av certifikat för att autentisera dina push-meddelanden. Följ de här instruktionerna för att skapa nödvändiga push-certifikat för att skicka och ta emot meddelanden. Mer information om de här koncepten finns i den officiella [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)-dokumentationen.

Generera filen för begäran om certifikatsignering (CSR), som används av Apple för att generera ett signerat push-certifikat.

1. Kör **Nyckelhanteraren** på din Mac. Den kan öppnas från mappen **Verktyg** eller mappen **Annat** i startmenyn.
2. Klicka på **Nyckelhanteraren**, expandera **Certifikatassistenten** och klicka sedan på **Begär ett certifikat från en certifikatutfärdare...**.

    ![Använd nyckelhanterare för att begära ett nytt certifikat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. Ange en **e-postadress för användaren** och ett **Vanligt namn**, se till att **Sparas till disk** är markerat och klicka sedan på **Fortsätt**. Lämna fältet **CA-e-postadress** tomt eftersom det inte behövs.

    ![Nödvändig certifikatsinformation](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Ange ett namn för filen för begäran om certifikatsignering (CSR) i **Spara som**, ange en plats i **Var** och klicka sedan på **Spara**.

    ![Välj filnamn för certifikatet](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    CSR-filen sparas på den valda platsen. Standardplatsen är skrivbordet. Kom ihåg den plats du valde för filen.

Därefter registrerar du din app med Apple, aktivera push-meddelanden och laddar upp den exporterade CSR-filen för att skapa ett push-certifikat.

## <a name="register-your-app-for-push-notifications"></a>Registrera din app för push-meddelanden

För att kunna skicka push-meddelanden till en iOS-app måste du registrera programmet med Apple och registrera dig för push-meddelanden.  

1. Om du inte redan har registrerat din app så navigerar du till [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) i Apple Developer Center, loggar in med ditt Apple-ID, klickar först på **Identifierare**, sedan på **App-ID:n** och slutligen på **+** Logga in för att registrera en ny app.

    ![Sidan för App-ID:n i etableringsportalen för iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Uppdatera följande tre fält för din nya app och klicka sedan på **Fortsätt**:

   * **Namn**: Ange ett beskrivande namn för din app i den **namn** i den **ID Appbeskrivning** avsnittet.
   * **Paketidentifierare**: Under den **Explicit App-ID** anger en **paketidentifieraren** i formuläret `<Organization Identifier>.<Product Name>` som anges i den [Appdistributionsguiden](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Den *organisationsidentifierare* och det *produktnamn* som du använder måste matcha organisationsidentifieraren och produktnamnet du använder när du skapar ditt XCode-projekt. På följande skärmbild används *NotificationHubs* som ett organisations-ID och *GetStarted* används som produktnamnet. Kontrollera att detta värde matchar de värden som du använder i ditt XCode-projekt, eftersom du då kan använda rätt publiceringsprofil med XCode.
   * **Push-meddelanden**: Kontrollera den **Push-meddelanden** alternativet i den **Apptjänster** avsnittet.

     ![Formulär för att registrera ett nytt App-ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

     Denna åtgärd genererar ditt app-ID och ber dig att bekräfta informationen. Klicka på **Registrera** för att bekräfta det nya app-ID:et.

     När du klickar på **Registrera** visas skärmen **Registreringen har slutförts** såsom på följande bild. Klicka på **Klar**.

     ![Registreringen av App-ID:et har slutförts och visar berättigande](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

3. Under App-ID:n i Developer Center letar du upp den App-ID som du skapade och klickar på dess rad.

    ![Lista över App-ID:n](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    Om du klickar på App-ID:n så visas appens information. Klicka på **Redigera**-knappen längst ned.

    ![Redigera sidan för App-ID:n](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

4. Bläddra till längst ned på skärmen och klicka på knappen **Skapa certifikat...** i avsnittet **Development Push SSL-certifikat**.

    ![Skapa certifikat för knappen för App-ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Assistenten **Lägg till iOS-certifikat** visas.

    > [!NOTE]
    > Den här guiden använder ett utvecklarcertifikat. Du använder samma process när du registrerar ett driftscertifikat. Se bara till att du använder samma certifikattyp när du skickar meddelanden.

5. Klicka på **Välj fil**, bläddra till platsen där du sparade CSR-filen som du skapade i den första uppgiften och klicka sedan på **Generera**.

    ![Uppladdningssidan för genererade certifikat i CSR-format](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

6. När certifikatet har skapats av portalen klickar du på **Hämta**-knappen och klickar sedan på **Klar**.

    ![Nedladdningssidan för genererade certifikat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Certifikatet laddas ned och sparas på datorn i mappen **Hämtade filer**.

    ![Hitta certifikatfilen i mappen Hämtade filer](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Som standard kallas den hämtade filen, ett utvecklingscertifikat, **aps_development.cer**.

7. Dubbelklicka på det hämtade push-certifikatet **aps_development.cer**.

    Den här åtgärden installerar det nya certifikatet i nyckelringen enligt följande bild:

    ![Nya certifikat visas i listan med certifikat för nyckelhanteraren](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Certifikatnamnet kan skilja sig, men prefixet kommer att vara **Apple Development iOS Push Services:**.

8. I nyckelhanteraren högerklickar du på det nya push-certifikatet som du skapade i **Certifikat**-kategorin. Klicka på **Exportera**, namnge filen, välj **.p12**-formatet och klicka sedan på **Spara**.

    ![Exportera certifikatet i p12-format](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Anteckna filnamnet och platsen dit .p12-certifikatet exporterats. Det används för att aktivera autentisering med APNS.

    > [!NOTE]
    > Den här guiden skapar en QuickStart.p12-fil. Filnamnet och sökvägen kan vara olika för dig.

## <a name="create-a-provisioning-profile-for-the-app"></a>Skapa en etableringsprofil för appen

1. I [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) väljer du **Etableringsprofiler**, markerar **Alla** och klickar sedan på **+**-knappen (plusknappen) för att skapa en ny profil. Du ser guiden **Lägg till iOS-etableringsprofil**:

    ![Lista med etableringsprofiler](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Välj **iOS App Development** under **Utveckling** som etableringsprofiltyp och klicka på **Fortsätt**.

3. Därefter väljer du det App-ID som du skapat från listrutan **App-ID** och klickar på **Fortsätt**

    ![Välj App-ID:et](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

4. På skärmen **Välj certifikat**, väljer du ditt vanliga utvecklingscertifikat som används för kodsignering och klickar på **Fortsätt**. Det här certifikatet är inte det push-certifikat som du skapade.

    ![Välj certifikatet](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

5. Därefter väljer du de **enheter** som ska användas för testning och klickar på **Fortsätt**

    ![Välj enheterna](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

6. Slutligen väljer du ett namn för profilen i **Profilnamn** och klickar på **Generera**.

    ![Välj namn på etableringsprofilen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

7. När den nya etableringsprofilen har skapats klickar du och hämtar den och installerar den på din XCode-utvecklingsdator. Klicka sedan på **Klar**.

    ![Ladda ned etableringsprofilen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)
