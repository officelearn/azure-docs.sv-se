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
ms.openlocfilehash: 3e4549a21ec32f1a2c1c869c3b2e0bd8c2e4204e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446576"
---
## <a name="generate-the-certificate-signing-request-file"></a>Generera filen signering av certifikat

Apple Push Notification Service (APNs) använder certifikat för att autentisera dina push-meddelanden. Följ de här instruktionerna för att skapa nödvändiga push-certifikat för att skicka och ta emot meddelanden. Mer information om de här koncepten finns i den officiella [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)-dokumentationen.

Generera filen för begäran om certifikatsignering (CSR), som Apple använder för att generera ett signerat push-certifikat.

1. Kör Nyckelhanteraren på din Mac. Den kan öppnas från den **verktyg** mapp eller **andra** mapp på Launchpad.

1. Välj **Nyckelhanterare**, expandera **Certifikatassistenten**, och välj sedan **begära ett certifikat från en certifikatutfärdare**.

    ![Använd nyckelhanterare för att begära ett nytt certifikat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

1. Välj din **användares e-postadress**, ange din **nätverksnamn** värde, se till att du anger **sparas till disk**, och välj sedan **Fortsätt**. Lämna **CA e-postadress** tom eftersom det inte krävs.

    ![Nödvändig certifikatsinformation](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Ange ett namn för CSR-filen i **Spara som**, ange en plats i **där**, och välj sedan **spara**.

    ![Välj ett filnamn för certifikatet](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Den här åtgärden sparar CSR-filen i den valda platsen. Standardplatsen är **Desktop**. Kom ihåg den plats du valde för filen.

Registrera din app med Apple, aktivera push-meddelanden och ladda upp den exporterade CSR-filen för att skapa ett push-certifikat.

## <a name="register-your-app-for-push-notifications"></a>Registrera din app för push-meddelanden

För att push-meddelanden till en iOS-app, registrera ditt program med Apple och registrera dig för push-meddelanden.  

1. Om du inte redan har registrerat din app, bläddra till den [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) på Apple Developer Center. Efter det kan du logga in med ditt Apple-ID, Välj **identifierare**väljer **App-ID: N**, och välj slutligen **+** att registrera en ny app.

    ![Sidan för App-ID:n i etableringsportalen för iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. Uppdatera följande tre värden för din nya app och välj sedan **Fortsätt**:

   * **Namn på**: Ange ett beskrivande namn för din app i den **namn** rutan den **ID Appbeskrivning** avsnittet.

   * **Paketidentifierare**: I den **Explicit App-ID** anger en **paketidentifieraren** formulärets `<Organization Identifier>.<Product Name>` som anges i den [Appdistributionsguiden](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Den *Organisationsidentifierare* och *produktnamn* värdena måste matcha organisationsidentifieraren och produktnamnet organisationsnamnet du använder när du skapar ditt Xcode-projekt. I följande skärmbild, den *NotificationHubs* värdet används som en organisations-ID och *GetStarted* värdet används som produktnamnet. Kontrollera att den **paket-ID** värdet matchar värdet i Xcode-projektet så att du kommer att använda rätt publiceringsprofil med Xcode.

   * **Push-meddelanden**: Kontrollera den **Push-meddelanden** alternativet i den **Apptjänster** avsnittet.

     ![Formulär för att registrera ett nytt App-ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

     Den här åtgärden genererar ditt App-ID och begäranden som du bekräfta informationen. Välj **registrera** att bekräfta den nya App-ID.

     När du har valt **registrera**, visas den **registreringen har slutförts** skärmen som visas i följande bild. Välj **Done** (Klar).

     ![Registreringen av App-ID:et har slutförts och visar berättigande](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

1. I Developer Center under **App-ID: N**letar du upp app-ID som du skapade och välj en rad.

    ![Lista över App-ID:n](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    Välj det app-ID för att visa information om appar och välj sedan den **redigera** längst ned.

    ![Redigera sidan för App-ID:n](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. Bläddra till längst ned på skärmen och välj den **skapa certifikat** knappen den **Development Push SSL-certifikat** avsnittet.

    ![Skapa certifikat för knappen för App-ID](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Du kommer nu se den **Lägg till iOS-certifikat** Installationsassistenten.

    > [!NOTE]
    > Den här guiden använder ett utvecklarcertifikat. Du använder samma process när du registrerar ett driftscertifikat. Se bara till att du använder samma certifikattyp när du skickar meddelanden.

1. Välj **Välj fil**, bläddra till den plats där du sparade CSR-filen från den första aktiviteten och välj sedan **generera**.

    ![Uppladdningssidan för genererade certifikat i CSR-format](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

1. När certifikatet skapar portalen, väljer du den **hämta** knappen och välj sedan **klar**.

    ![Nedladdningssidan för genererade certifikat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Certifikatet hämtas och sparas på din dator i din **hämtar** mapp.

    ![Hitta certifikatfilen i mappen Hämtade filer](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Som standard heter den hämtade utvecklingscertifikat **aps_development.cer**.

1. Välj det hämta push-certifikatet **aps_development.cer**.

    Den här åtgärden installerar det nya certifikatet i nyckelringen enligt följande bild:

    ![Nya certifikat visas i listan med certifikat för nyckelhanteraren](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Även om namnet i certifikatet kan vara olika, namnet inleds med **Apple Development iOS Push Services**.

1. I nyckelhanteraren högerklickar du på det nya push-certifikatet som du skapade i **Certifikat**-kategorin. Välj **exportera**, namnge filen, Välj den **.p12** formatera och välj sedan **spara**.

    ![Exportera certifikatet i p12-format](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Anteckna filnamnet och platsen dit .p12-certifikatet exporterats. De används för att aktivera autentisering med APNs.

    > [!NOTE]
    > Den här självstudiekursen skapar en fil med namnet **QuickStart.p12**. Filnamnet och sökvägen kan vara olika för dig.

## <a name="create-a-provisioning-profile-for-the-app"></a>Skapa en etableringsprofil för appen

1. I den [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456)väljer **Etableringsprofiler**väljer **alla**, och välj sedan **+** att skapa en ny profil. Du ser den **Lägg till iOS-profil för etablering** guiden.

    ![Lista med etableringsprofiler](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Välj **iOS App Development** under **utveckling** profiltyp som etablering och välj **Fortsätt**.

1. Välj det app-ID som du skapade från den **App-ID** listrutan och välj **Fortsätt**.

    ![Välj App-ID:et](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. I den **väljer certifikat** fönstret, Välj ditt vanliga utvecklingscertifikat som du använde för kodsignering och välj **Fortsätt**. Det här certifikatet är inte push-certifikatet som du skapade.

    ![Välj certifikatet](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

1. Sedan markera enheterna som ska användas för testning och välj **Fortsätt**.

    ![Välj enheterna](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

1. Slutligen väljer du ett namn för profilen i **profilnamn**, och välj **generera**.

    ![Välj namn på etableringsprofilen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. När den nya etableringsprofilen har skapats kan du välja att hämta och installera det på din Xcode-utvecklingsdator. Välj sedan **Done** (Klar).

    ![Ladda ned etableringsprofilen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)

## <a name="create-a-notification-hub"></a>Skapa en meddelandehubb

I det här avsnittet ska du skapa en meddelandehubb och konfigurera autentisering med APNs med hjälp av .p12 push-certifikatet som du skapade tidigare. Om du vill använda en meddelandehubb som du redan har skapat kan du gå vidare till steg 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Konfigurera din meddelandehubb med APNs-information

1. Välj **Apple (APNS)** under **Notification Services**.

1. Välj **Certifikat**.

1. Välj filen.

1. Välj .p12-filen som du exporterade tidigare.

1. Ange rätt lösenord.

1. Välj **Sandbox**-läge. Använd läget **Produktion** enbart om du vill skicka push-meddelanden till användare som har köpt din app i butiken.

    ![Konfigurera APNs-certifikat i Azure-portalen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

Du har nu konfigurerat din meddelandehubb med APNs. Du kan också ha anslutningssträngar för att registrera din app och skicka push-meddelanden.
